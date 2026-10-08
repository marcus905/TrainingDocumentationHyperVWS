# Day 3 — Advanced Hyper-V and Real-World Scenarios

## Learning objectives

By the end of Day 3, students should be able to:

- manage Hyper-V checkpoints safely;
- explain Standard vs Production checkpoints;
- explain why checkpoints are not backups;
- describe backup, restore, replication and high availability as separate concepts;
- prepare a second Hyper-V host;
- configure Hyper-V Replica between standalone hosts;
- validate replication health;
- perform a Test Failover and explain Planned and Unplanned Failover;
- compare local, shared and enterprise Hyper-V storage options;
- collect a basic host/guest performance baseline;
- identify common CPU, memory, storage and network performance signals;
- explain bare-metal provisioning and PXE at a high level;
- recognize current Windows Deployment Services limitations and direction.

---

# Module 1 — Advanced VM state management and checkpoints

A checkpoint captures a point-in-time state that can be used to return a VM to an earlier state.

Checkpoints are useful for controlled changes, testing and short-lived rollback scenarios. They are not intended to become permanent storage structures.

Microsoft reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/checkpoints

## Standard checkpoints

A Standard checkpoint captures VM configuration, virtual disk state and VM memory state when applicable.

Restoring it returns the VM to that captured state.

This can be useful in labs, but application state inside the guest might not be crash-consistent in the way a production workload expects.

## Production checkpoints

Production checkpoints use guest-supported backup technology to create a data-consistent checkpoint.

On Windows guests, this normally uses VSS integration.

They are preferred for production workloads when supported.

## Checkpoint files

When a checkpoint is created, Hyper-V normally creates differencing disk files with the AVHDX extension.

~~~text
Before checkpoint

SRV01.vhdx
    |
Current VM writes


After checkpoint

SRV01.vhdx
    |
SRV01-Checkpoint.avhdx
    |
Current VM writes
~~~

New writes are redirected into the AVHDX chain.

When the checkpoint is deleted, Hyper-V merges the differencing data back into the parent chain.

## Operational risks

Long-lived or unmanaged checkpoints can create:

- growing AVHDX files;
- storage-capacity pressure;
- longer merge operations;
- increasingly complex disk chains;
- additional operational risk during recovery.

A checkpoint should have a reason, an owner and a planned removal time.

---

## Integration Services

Hyper-V Integration Services are guest-facing components that improve communication and coordination between the Hyper-V host and supported guest operating systems.

Useful services include:

- **Heartbeat** — lets the host verify that the guest OS is responding;
- **Time Synchronization** — coordinates guest time with the Hyper-V host;
- **Shutdown** — allows the host to request a graceful guest shutdown;
- **Guest Service Interface** — enables selected host-to-guest file operations;
- **VSS / Backup integration** — supports application-consistent backup and Production Checkpoint workflows.

Inspect SRV01:

~~~powershell
Get-VMIntegrationService -VMName SRV01
~~~

Review:

- Name;
- Enabled;
- PrimaryStatusDescription.

### Why this matters

Integration Services sit at the boundary between VM configuration and guest behavior.

A feature can exist at the Hyper-V layer but still depend on guest support and guest-side health.

This is especially relevant to:

- Production Checkpoints;
- backup;
- time synchronization;
- graceful shutdown;
- guest health monitoring.

---

# Lab 3.1 — Create, restore and remove a checkpoint

## Objective

Observe the full checkpoint lifecycle and the relationship between VHDX and AVHDX files.

## Step 1 — Verify checkpoint configuration

~~~powershell
Get-VM SRV01 | Select-Object Name,CheckpointType
~~~

For a Windows Server workload, use Production checkpoints when possible.

~~~powershell
Set-VM -Name "SRV01" -CheckpointType Production
Get-VM SRV01 | Select-Object Name,CheckpointType
~~~

## Step 3 — Record the current disk chain

~~~powershell
Get-VMHardDiskDrive SRV01
Get-ChildItem "D:\Hyper-V\VHDX" | Select-Object Name,Length,LastWriteTime
~~~

## Step 4 — Create the checkpoint

~~~powershell
Checkpoint-VM -VMName "SRV01" -SnapshotName "Pre-Application-Change"
Get-VMSnapshot -VMName SRV01
~~~

Inspect storage again:

~~~powershell
Get-ChildItem "D:\Hyper-V\VHDX" | Select-Object Name,Length,LastWriteTime
~~~

Look for AVHDX files.

## Step 5 — Make a controlled guest change

Inside SRV01:

~~~powershell
New-Item -ItemType Directory -Path "C:\Lab" -Force
"Created after checkpoint" | Set-Content "C:\Lab\Checkpoint-Test.txt"
~~~

Confirm that C:\Lab\Checkpoint-Test.txt exists.

## Step 6 — Restore the checkpoint

~~~powershell
Restore-VMSnapshot -VMName "SRV01" -Name "Pre-Application-Change" -Confirm:$false
~~~

Start SRV01 if required and verify that the post-checkpoint change has been rolled back.

## Step 7 — Remove the checkpoint

~~~powershell
Remove-VMSnapshot -VMName "SRV01" -Name "Pre-Application-Change"
~~~

Monitor:

~~~powershell
Get-VMSnapshot SRV01
Get-ChildItem "D:\Hyper-V\VHDX" | Select-Object Name,Length,LastWriteTime
~~~

The AVHDX chain may remain visible while merge activity completes.

## Validation checkpoint

- [ ] Production checkpoint type selected.
- [ ] Checkpoint created.
- [ ] AVHDX behavior observed.
- [ ] Guest change rolled back.
- [ ] Checkpoint removed.
- [ ] Merge behavior discussed.

---

# Module 2 — Checkpoint, backup, Replica and HA are different things

These technologies solve different problems.

| Technology | Primary purpose | Typical scope |
|---|---|---|
| Checkpoint | Short-term rollback | One VM |
| Backup | Recover deleted, damaged or historical data | VM and data recovery |
| Hyper-V Replica | Disaster recovery to another host/site | VM-level asynchronous replication |
| Failover Clustering | High availability after host failure | Clustered workloads |

## Checkpoint

A checkpoint is operational state management.

It is stored with the VM and depends on the same underlying storage unless deliberately moved.

It does not protect against loss of the entire host/storage platform.

## Backup

A backup should provide an independent recovery copy according to defined retention and recovery objectives.

A production Hyper-V backup solution should be Hyper-V/VSS aware and should be tested through actual restore procedures.

A backup strategy should define:

- what is protected;
- retention;
- Recovery Point Objective, RPO;
- Recovery Time Objective, RTO;
- off-host or off-site protection;
- restore validation.

## Hyper-V Replica

Replica asynchronously copies selected VM changes to another Hyper-V host or cluster.

Replica is primarily a disaster-recovery technology.

It is not a replacement for backup because corruption, unwanted changes or application-level mistakes can also be replicated.

## High availability

High availability normally uses Windows Failover Clustering so a VM can restart or move to another cluster node when a host fails.

Replica does not automatically provide the same behavior as a failover cluster.

---

# Lab 3.2 — Export and recovery practice

## Objective

Practice VM export/import mechanics while clearly distinguishing them from a production backup system.

> Export-VM is useful for portability and controlled recovery exercises, but this lab does not redefine VM export as an enterprise backup solution.

## Step 1 — Prepare the export folder

~~~powershell
New-Item -ItemType Directory -Path "D:\Hyper-V\Export" -Force
~~~

## Step 2 — Export SRV01

~~~powershell
Export-VM -Name "SRV01" -Path "D:\Hyper-V\Export"
~~~

Inspect:

~~~powershell
Get-ChildItem "D:\Hyper-V\Export" -Recurse
~~~

Identify the VM configuration, virtual disks and any checkpoint folders.

## Step 3 — Discuss import modes

Hyper-V supports import choices that conceptually map to:

- register the VM in place;
- restore the VM;
- copy the VM and generate a new unique ID.

For a recovery-copy exercise, generating a new ID avoids identity conflict with the original VM.

## Step 4 — Optional isolated recovery import

If disk space permits, import a copy into an isolated location and do not connect it to the production lab switch.

The instructor can demonstrate:

~~~powershell
Import-VM -Path "<VM configuration path>" -Copy -GenerateNewId
~~~

Do not run the imported copy simultaneously on the same network with conflicting guest identity/IP settings.

## Restore to explicit locations

When importing with `-Copy`, Hyper-V can place the restored VM configuration and virtual disks in locations that you choose rather than only using the host defaults.

Example:

~~~powershell
Import-VM `
    -Path "<VM configuration path>" `
    -Copy `
    -GenerateNewId `
    -VhdDestinationPath "D:\Hyper-V\VHDX\SRV01-Restore" `
    -VirtualMachinePath "D:\Hyper-V\VMs\SRV01-Restore"
~~~

### `-VhdDestinationPath`

This specifies the folder where Hyper-V copies the restored VM's VHD/VHDX files.

Use it when:

- the original disk path does not exist on the recovery host;
- you want restored disks separated from the original VM;
- storage layout differs between source and recovery hosts;
- you want to avoid placing restored disks in an unintended default location.

### `-VirtualMachinePath`

This specifies the folder where Hyper-V stores the imported VM configuration files.

It is separate from `-VhdDestinationPath` because VM configuration/state files and virtual disks can be stored in different locations.

### Why `-GenerateNewId` is included here

When the original SRV01 still exists on the same host, a copied recovery VM needs a different VM identity. `-GenerateNewId` avoids a duplicate VM-ID conflict.

### Important distinction

`-VhdDestinationPath` and `-VirtualMachinePath` belong to the **Copy** import workflow. A register-in-place import does not copy the export into new locations.

Microsoft references:

https://learn.microsoft.com/powershell/module/hyper-v/import-vm?view=windowsserver2025-ps

https://learn.microsoft.com/windows-server/virtualization/hyper-v/deploy/export-and-import-virtual-machines

## Recovery discussion

A successful backup strategy is not proven until a restore is tested.

Students should be able to explain why:

~~~text
Backup succeeded
~~~

is not equivalent to:

~~~text
Recovery has been validated
~~~

---

# Module 3 — Prepare HV02

HV02 provides the recovery-side Hyper-V host used for Replica demonstrations.

# Lab 3.3 — Prepare HV02 as the recovery host

HV02 was created on Day 0 with:

- outer management IP 192.168.240.12/24;
- a dedicated approximately 200 GB raw data disk;
- nested virtualization enabled.

Day 3 now completes the recovery-host preparation.

## Step 1 — Initialize the HV02 data disk

On HV02:

~~~powershell
Get-Disk
~~~

Identify the approximately 200 GB raw training disk.

Do not assume it is always Disk 1.

For the example below, replace <DiskNumber> with the verified training-disk number:

~~~powershell
Initialize-Disk -Number <DiskNumber> -PartitionStyle GPT
New-Partition -DiskNumber <DiskNumber> -UseMaximumSize -DriveLetter D
Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel "Hyper-V Data" -Confirm:$false
~~~

Verify:

~~~powershell
Get-Volume -DriveLetter D
~~~

## Step 2 — Install Hyper-V

~~~powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
~~~

After restart:

~~~powershell
Get-WindowsFeature Hyper-V
Get-Service vmms
Get-VMHost
~~~

## Step 3 — Create recovery-host storage paths

~~~powershell
New-Item -ItemType Directory -Path "D:\Hyper-V\VMs" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\VHDX" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\Replica" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\Export" -Force
Set-VMHost -VirtualMachinePath "D:\Hyper-V\VMs" -VirtualHardDiskPath "D:\Hyper-V\VHDX"
~~~

## Step 4 — Create the recovery-side workload network

HV02 should provide the same workload network name used on HV01 so a recovered VM can map cleanly to the expected switch.

On HV02:

~~~powershell
New-VMSwitch -Name "vSW-Lab" -SwitchType Internal
New-NetIPAddress -InterfaceAlias "vEthernet (vSW-Lab)" -IPAddress 172.22.0.1 -PrefixLength 24
New-NetNat -Name "LabNAT" -InternalIPInterfaceAddressPrefix "172.22.0.0/24"
~~~

Verify:

~~~powershell
Get-VMSwitch -Name "vSW-Lab"
Get-NetIPAddress -InterfaceAlias "vEthernet (vSW-Lab)" -AddressFamily IPv4
Get-NetNat -Name "LabNAT"
~~~

HV01 and HV02 each have their own isolated 172.22.0.0/24 nested network. They do not share Layer-2 connectivity.

## Step 5 — Verify outer host-to-host connectivity

The deterministic Day 0 management addresses are:

~~~text
HV01: 192.168.240.11
HV02: 192.168.240.12
~~~

From HV01:

~~~powershell
Test-NetConnection 192.168.240.12
~~~

From HV02:

~~~powershell
Test-NetConnection 192.168.240.11
~~~

For Replica, host-name resolution must match the certificate identities prepared in the next lab.

---

# Module 4 — Hyper-V Replica

Hyper-V Replica asynchronously replicates VM changes from a primary Hyper-V host to a replica Hyper-V host or cluster.

Microsoft references:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/configure-replication-single-host

https://learn.microsoft.com/windows-server/virtualization/hyper-v/replication-virtual-machines

~~~text
Primary host                        Recovery host

HV01                                HV02
SRV01
  |
  +------ asynchronous -----------> SRV01 Replica
~~~

## Recovery objectives

Replica introduces two important DR concepts.

**RPO — Recovery Point Objective**

How much data loss can the business tolerate?

**RTO — Recovery Time Objective**

How long can the workload remain unavailable?

Replica frequency and operational recovery procedures affect these objectives.

## Authentication choices

### Kerberos over HTTP

Typical default port:

~~~text
TCP 80
~~~

Appropriate when hosts are joined to the same or trusted Active Directory domains.

### Certificate-based authentication over HTTPS

Typical default port:

~~~text
TCP 443
~~~

Required when hosts are not domain joined or are in untrusted domains, and also provides encrypted replication traffic.

Our course hosts are standalone, so the lab uses the **certificate/HTTPS design** created locally in Lab 3.4.

A valid Replica certificate must:

- not be expired;
- contain a private key;
- include both Client Authentication and Server Authentication EKUs;
- chain to a trusted root certificate;
- have a CN or SAN matching the host FQDN.

Microsoft reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/configure-replication-single-host

---

# Lab 3.4 — Prepare standalone-host Replica authentication

## Objective

Create a reproducible **lab-only PKI** for certificate-based Hyper-V Replica between the standalone HV01 and HV02 hosts.

Production environments should use certificates issued by an organization's trusted PKI. The self-signed lab CA below exists only so every student can build the same isolated training environment locally.

Recommended host names:

~~~text
hv01.lab.local
hv02.lab.local
~~~

## Step 1 — Set the primary DNS suffix on HV01 and HV02

The certificate names used in this lab are:

~~~text
hv01.lab.local
hv02.lab.local
~~~

A hosts-file entry can provide name resolution, but it does **not** change the Windows server's own fully qualified computer name.

Before creating the Replica certificates, configure the primary DNS suffix so each host's Windows FQDN matches the certificate identity.

On **HV01** and **HV02**:

1. Run `sysdm.cpl`.
2. Open the **Computer Name** tab.
3. Select **Change**.
4. Select **More**.
5. Set **Primary DNS suffix of this computer** to:

~~~text
lab.local
~~~

6. Confirm the dialogs.
7. Restart if prompted.

Expected configuration:

### HV01

~~~text
Computer name:       HV01
Primary DNS suffix:  lab.local
Full computer name:  hv01.lab.local
Membership:          WORKGROUP
~~~

### HV02

~~~text
Computer name:       HV02
Primary DNS suffix:  lab.local
Full computer name:  hv02.lab.local
Membership:          WORKGROUP
~~~

The servers remain workgroup members. Setting a primary DNS suffix does not join Active Directory.

After restart, verify the **Full computer name** on the Computer Name tab.

You can also review the DNS suffix in:

~~~powershell
ipconfig /all
~~~

Look for:

~~~text
Primary Dns Suffix
~~~

Do not continue with certificate creation until the local FQDNs match the certificate names.

## Step 2 — Add deterministic host-name mappings

The lab does not host a DNS zone for `lab.local`, so use the local hosts file to resolve the peer FQDN.

On HV01:

~~~powershell
Add-Content -Path "$env:SystemRoot\System32\drivers\etc\hosts" -Value "192.168.240.12 hv02.lab.local"
~~~

On HV02:

~~~powershell
Add-Content -Path "$env:SystemRoot\System32\drivers\etc\hosts" -Value "192.168.240.11 hv01.lab.local"
~~~

Validate through the normal Windows name-resolution path.

From HV01:

~~~powershell
Test-Connection hv02.lab.local -Count 2
Test-NetConnection hv02.lab.local
~~~

From HV02:

~~~powershell
Test-Connection hv01.lab.local -Count 2
Test-NetConnection hv01.lab.local
~~~

> **Name-resolution note**
>
> Use `Test-Connection` / `Test-NetConnection` to validate hosts-file mappings. `Resolve-DnsName` is most useful when validating actual DNS records and should not be used as the only proof of a hosts-file mapping.

## Step 3 — Create lab certificates on the physical Windows 11 host

On the physical Windows 11 host, create a folder:

~~~powershell
New-Item -ItemType Directory -Path "C:\HyperV-Course\ReplicaCerts" -Force
~~~

Create a self-signed lab root CA:

~~~powershell
$RootCA = New-SelfSignedCertificate -Type Custom -Subject "CN=HyperV-Course-Lab-RootCA" -KeyUsage CertSign,CRLSign,DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -HashAlgorithm SHA256 -CertStoreLocation "Cert:\CurrentUser\My" -TextExtension @("2.5.29.19={critical}{text}ca=1")
~~~

Create the HV01 Replica certificate:

~~~powershell
$HV01Cert = New-SelfSignedCertificate -Type Custom -Subject "CN=hv01.lab.local" -DnsName "hv01.lab.local" -Signer $RootCA -KeyAlgorithm RSA -KeyLength 2048 -HashAlgorithm SHA256 -KeyExportPolicy Exportable -CertStoreLocation "Cert:\CurrentUser\My" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2")
~~~

Create the HV02 Replica certificate:

~~~powershell
$HV02Cert = New-SelfSignedCertificate -Type Custom -Subject "CN=hv02.lab.local" -DnsName "hv02.lab.local" -Signer $RootCA -KeyAlgorithm RSA -KeyLength 2048 -HashAlgorithm SHA256 -KeyExportPolicy Exportable -CertStoreLocation "Cert:\CurrentUser\My" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2")
~~~

The host certificates include both Server Authentication and Client Authentication EKUs.

## Step 3 — Export the lab root and host certificates

Use a temporary **lab-only** PFX password chosen by the student:

~~~powershell
$PfxPassword = Read-Host "Enter a temporary lab PFX password" -AsSecureString

Export-Certificate -Cert $RootCA -FilePath "C:\HyperV-Course\ReplicaCerts\HyperV-Course-Lab-RootCA.cer"

Export-PfxCertificate -Cert $HV01Cert -FilePath "C:\HyperV-Course\ReplicaCerts\HV01-Replica.pfx" -Password $PfxPassword
Export-PfxCertificate -Cert $HV02Cert -FilePath "C:\HyperV-Course\ReplicaCerts\HV02-Replica.pfx" -Password $PfxPassword
~~~

Do not reuse a production password.

## Step 4 — Enable Guest Service Interface for HV01 and HV02

Still on the physical Windows 11 host:

~~~powershell
Enable-VMIntegrationService -VMName "HV01" -Name "Guest Service Interface"
Enable-VMIntegrationService -VMName "HV02" -Name "Guest Service Interface"
~~~

## Step 5 — Copy certificates into the outer hosts

Copy the root and HV01 PFX into HV01:

~~~powershell
Copy-VMFile -Name "HV01" -SourcePath "C:\HyperV-Course\ReplicaCerts\HyperV-Course-Lab-RootCA.cer" -DestinationPath "C:\ReplicaCerts\HyperV-Course-Lab-RootCA.cer" -FileSource Host -CreateFullPath
Copy-VMFile -Name "HV01" -SourcePath "C:\HyperV-Course\ReplicaCerts\HV01-Replica.pfx" -DestinationPath "C:\ReplicaCerts\HV01-Replica.pfx" -FileSource Host -CreateFullPath
~~~

Copy the root and HV02 PFX into HV02:

~~~powershell
Copy-VMFile -Name "HV02" -SourcePath "C:\HyperV-Course\ReplicaCerts\HyperV-Course-Lab-RootCA.cer" -DestinationPath "C:\ReplicaCerts\HyperV-Course-Lab-RootCA.cer" -FileSource Host -CreateFullPath
Copy-VMFile -Name "HV02" -SourcePath "C:\HyperV-Course\ReplicaCerts\HV02-Replica.pfx" -DestinationPath "C:\ReplicaCerts\HV02-Replica.pfx" -FileSource Host -CreateFullPath
~~~

## Step 6 — Import the root and host certificate on HV01

On HV01:

~~~powershell
Import-Certificate -FilePath "C:\ReplicaCerts\HyperV-Course-Lab-RootCA.cer" -CertStoreLocation "Cert:\LocalMachine\Root"
$PfxPassword = Read-Host "Enter the lab PFX password" -AsSecureString
Import-PfxCertificate -FilePath "C:\ReplicaCerts\HV01-Replica.pfx" -CertStoreLocation "Cert:\LocalMachine\My" -Password $PfxPassword
~~~

## Step 8 — Import the root and host certificate on HV02

On HV02:

~~~powershell
Import-Certificate -FilePath "C:\ReplicaCerts\HyperV-Course-Lab-RootCA.cer" -CertStoreLocation "Cert:\LocalMachine\Root"
$PfxPassword = Read-Host "Enter the lab PFX password" -AsSecureString
Import-PfxCertificate -FilePath "C:\ReplicaCerts\HV02-Replica.pfx" -CertStoreLocation "Cert:\LocalMachine\My" -Password $PfxPassword
~~~

## Step 9 — Verify certificate properties

On each host:

~~~powershell
Get-ChildItem Cert:\LocalMachine\My |
    Where-Object Subject -like "*lab.local*" |
    Select-Object Subject,DnsNameList,Thumbprint,NotAfter,HasPrivateKey,EnhancedKeyUsageList
~~~

Verify:

- the local host certificate has a private key;
- the DNS name matches the local host FQDN;
- the certificate is not expired;
- both Client Authentication and Server Authentication are present;
- the issuing lab root is trusted.

Record the thumbprints:

~~~text
HV01 certificate thumbprint: ______________________________
HV02 certificate thumbprint: ______________________________
~~~

## Step 10 — Disable certificate revocation checking for this isolated lab

Hyper-V Replica performs certificate revocation checking.

This lab-only CA does not publish a reachable CRL distribution point, so Microsoft documents the server-level DisableCertRevocationCheck value for this kind of isolated lab deployment.

On **both HV01 and HV02**:

~~~powershell
$ReplicaRegPath = "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Virtualization\Replication"

New-Item -Path $ReplicaRegPath -Force | Out-Null

New-ItemProperty -Path $ReplicaRegPath -Name "DisableCertRevocationCheck" -PropertyType DWord -Value 1 -Force
~~~

Verify:

~~~powershell
Get-ItemProperty -Path $ReplicaRegPath -Name "DisableCertRevocationCheck"
~~~

Expected value:

~~~text
DisableCertRevocationCheck : 1
~~~

> **Lab-only security note**
>
> Disabling certificate revocation checking weakens certificate validation. Use this only for the isolated training PKI created in this lab. Production environments should use certificates whose revocation infrastructure is available and should keep normal revocation validation enabled.

Microsoft references:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/configure-replication-single-host

https://learn.microsoft.com/troubleshoot/windows-server/virtualization/feature-performance-optimization-hyper-v-replica

---

# Lab 3.5 — Enable HV02 as a Replica server

## Step 1 — Enable HTTPS Replica

On HV02, use Hyper-V Manager:

~~~text
Hyper-V Settings
  -> Replication Configuration
  -> Enable this computer as a Replica server
  -> Use certificate-based authentication (HTTPS)
~~~

Select the valid HV02 certificate and allow replication from the required server or servers.

## Step 2 — Enable the firewall rule

On HV02:

~~~powershell
Get-NetFirewallRule | Where-Object DisplayName -like "*Replica*"
~~~

Enable the HTTPS Replica listener rule identified in the output.

## Step 3 — Verify listener configuration

Use Hyper-V Manager to confirm:

- Replica server enabled;
- HTTPS/certificate authentication selected;
- authorization/storage location configured.

## Step 4 — Test Replica connectivity

From HV01:

~~~powershell
Test-VMReplicationConnection -ReplicaServerName "hv02.lab.local" -ReplicaServerPort 443 -AuthenticationType Certificate -CertificateThumbprint "<HV01 certificate thumbprint>"
~~~

Expected result indicates that the Replica connection succeeded.

If it fails, do not continue until name resolution, certificate trust, certificate identity and firewall state are checked.

---

# Lab 3.6 — Enable replication for SRV01

## Step 1 — Start the Enable Replication wizard

In Hyper-V Manager on HV01:

~~~text
SRV01
  -> Enable Replication
~~~

Configure:

- Replica server: hv02.lab.local;
- HTTPS / certificate authentication;
- compression as appropriate;
- virtual disks to replicate;
- replication frequency;
- recovery history;
- initial replication method.

## Step 2 — Select VHDX files

Review all attached SRV01 disks.

Replicate only disks required by the workload/recovery plan.

## Step 3 — Choose replication frequency

Available choices can include:

- 30 seconds;
- 5 minutes;
- 15 minutes.

Discuss the tradeoff:

~~~text
Shorter interval
    -> lower potential data loss
    -> more replication traffic

Longer interval
    -> higher potential data loss
    -> lower replication traffic
~~~

## Step 4 — Start initial replication

For this lab, use network-based initial replication.

Monitor:

~~~powershell
Get-VMReplication -VMName SRV01
Measure-VMReplication -VMName SRV01
~~~

## Step 5 — Verify Replica health

Review:

- State;
- Health;
- PrimaryServer;
- ReplicaServer;
- LastReplicationTime.

## Validation checkpoint

- [ ] HV02 is enabled as Replica server.
- [ ] HTTPS authentication configured.
- [ ] Replica connection test succeeds.
- [ ] SRV01 replication enabled.
- [ ] Initial replication completed.
- [ ] Get-VMReplication shows normal state.
- [ ] Measure-VMReplication returns replication metrics.

---

# Module 5 — Replica failover and recovery

Microsoft reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/replication-failover

Hyper-V Replica supports three main failover scenarios.

## Test Failover

Used to validate the replica without disrupting normal replication.

It creates a temporary test VM from a selected recovery point.

By default, the test VM is not connected to a network unless a test network is configured.

## Planned Failover

Used when the primary VM and primary site are still available.

The primary VM is shut down gracefully and outstanding changes are replicated before workload direction changes.

This is designed to avoid data loss during a planned transition.

Planned failover is **not a substitute for high availability**.

## Unplanned Failover

Used when the primary workload is unavailable.

The replica starts from an available recovery point.

Because the latest primary-side changes might not have replicated, some data loss is possible.

---

# Lab 3.7 — Perform a Test Failover

## Objective

Validate SRV01 recovery on HV02 without interrupting production replication.

## Step 1 — Create a test network

On HV02:

~~~powershell
New-VMSwitch -Name "vSW-RecoveryTest" -SwitchType Private
Get-VMSwitch -Name "vSW-RecoveryTest"
~~~

A Private switch prevents the recovery test VM from accidentally conflicting with the active SRV01 network.

## Step 2 — Start Test Failover

Use Hyper-V Manager on HV02:

~~~text
SRV01
  -> Replication
  -> Test Failover
~~~

Select an appropriate recovery point.

Configure the test VM to use vSW-RecoveryTest if required.

## Step 3 — Validate the test VM

Verify:

- test VM exists;
- test VM boots;
- guest files/services are present;
- active primary SRV01 remains unaffected;
- replication continues.

On HV01:

~~~powershell
Get-VMReplication SRV01
~~~

## Step 4 — Stop Test Failover

After validation:

~~~text
Replication
  -> Stop Test Failover
~~~

Confirm that the temporary test VM is removed.

## Recovery discussion

Students should explain:

- why Test Failover is safe during normal replication;
- why isolated networking matters;
- when Planned Failover is more appropriate;
- why Unplanned Failover can involve data loss.

---

# Module 6 — High availability introduction

High availability and disaster recovery are related but different design goals.

~~~text
High Availability
        |
Failover Clustering
        |
Multiple Hyper-V hosts
        |
Shared/coordinated storage and networking


Disaster Recovery
        |
Hyper-V Replica / Backup
        |
Secondary recovery location
~~~

## Failover Clustering

In a clustered Hyper-V design:

- multiple hosts participate in a Windows Failover Cluster;
- clustered VMs are managed as highly available roles;
- VM storage is accessible in a supported shared/coordinated design;
- another node can take ownership when a node fails.

Technologies commonly associated with clustered Hyper-V include:

- Cluster Shared Volumes, CSV;
- SMB 3 storage;
- Storage Spaces Direct;
- SAN-based shared storage;
- Live Migration.

This course introduces the architecture but does not build a complete production cluster inside the nested lab.

---

## Live Migration, Storage Migration and Replica

These technologies are often grouped together because they all involve VM movement, but they solve different problems.

| Technology | Moves running workload? | Moves storage? | Keeps a DR copy? | Typical use |
|---|---:|---:|---:|---|
| Live Migration | Yes | Not necessarily | No | Move a running VM between hosts with minimal interruption |
| Storage Migration | No host change required | Yes | No | Move VM files between storage locations |
| Hyper-V Replica | Recovery workload | Replicates selected VM disks | Yes | Disaster recovery |
| Failover Clustering | Changes workload ownership between nodes | Uses shared/coordinated storage design | No separate replica required | High availability |

### Live Migration

Live Migration transfers execution of a running VM from one compatible Hyper-V host to another.

Typical reasons include:

- host maintenance;
- balancing workloads;
- hardware servicing.

### Storage Migration

Storage Migration moves VM files between storage locations without changing the VM's logical identity.

Typical reasons include:

- storage maintenance;
- capacity balancing;
- moving from older storage to newer storage.

### Hyper-V Replica

Replica maintains an asynchronous recovery copy.

It is not simply another form of Live Migration because the replica is intended for recovery rather than routine host maintenance.

---

# Module 7 — Advanced Hyper-V storage

Day 2 focused on individual VHDX files. Day 3 expands the discussion to the storage architecture underneath those files.

## Local host storage

~~~text
HV01
 |
Local SSD/NVMe
 |
VHDX
~~~

Advantages:

- simple;
- low infrastructure dependency.

Limitations:

- VM storage is tied to one host;
- host/storage failure can remove both compute and VM data;
- not suitable by itself for clustered HA.

## SMB 3 storage

Hyper-V can store supported VM files on SMB 3 file shares.

This separates compute from file storage and can support enterprise designs when the SMB infrastructure meets Hyper-V requirements.

## SAN / block storage

Enterprise environments can expose shared block storage to hosts using technologies such as Fibre Channel or iSCSI.

The Windows cluster/storage layer then coordinates safe access.

## Cluster Shared Volumes

CSV allows multiple cluster nodes to access the same NTFS/ReFS volume while Failover Clustering coordinates access.

## Storage Spaces Direct

Storage Spaces Direct aggregates local drives from cluster nodes into resilient software-defined storage.

It combines compute and storage in a hyperconverged design.

## Storage design questions

Consider:

- capacity;
- IOPS;
- throughput;
- latency;
- resiliency;
- backup integration;
- growth;
- failure domains;
- operational complexity;
- recovery requirements.

---

# Module 8 — Hyper-V performance fundamentals

Performance troubleshooting starts with a baseline.

A single high utilization value is not enough to prove a bottleneck.

The useful question is:

> Which resource is constrained, for how long, and what workload is causing it?

## Host vs guest view

~~~text
Guest sees:
- virtual CPU
- assigned memory
- virtual disk
- virtual NIC

Host sees:
- CPU scheduling
- total host memory
- underlying storage
- physical/virtual networking
- competing VMs
~~~

A guest at 100% CPU does not automatically mean the physical host is saturated.

## Useful tools

- Task Manager;
- Resource Monitor;
- Performance Monitor;
- Get-Counter;
- Hyper-V Manager;
- Hyper-V PowerShell cmdlets.

## Discover Hyper-V counters

~~~powershell
Get-Counter -ListSet *Hyper-V*
~~~

Useful counter families include:

- Hyper-V Hypervisor Logical Processor;
- Hyper-V Hypervisor Virtual Processor;
- Hyper-V Dynamic Memory VM;
- Hyper-V Virtual Storage Device;
- Hyper-V Virtual Network Adapter.

Exact counters available can vary by configuration and Windows version.

---

## Basic tuning methodology

Tuning should follow evidence, not guesswork.

Use this sequence:

~~~text
Measure
   |
Identify constrained resource
   |
Change one variable
   |
Measure again
   |
Keep or revert the change
~~~

Useful VM configuration checks include:

~~~powershell
Get-VMProcessor SRV01
Get-VMMemory SRV01
Get-VMHardDiskDrive SRV01
Get-VMNetworkAdapter SRV01
~~~

Possible corrective actions include:

- adjusting vCPU count when CPU evidence supports it;
- adjusting Dynamic Memory limits when memory pressure is demonstrated;
- moving a VHDX to storage with better latency/capacity;
- removing unnecessary checkpoints;
- resolving host storage-capacity pressure;
- correcting virtual-network configuration;
- reducing unnecessary workload contention.

### Tuning principle

More resources are not automatically better.

For example:

- excess vCPU can increase scheduling contention;
- excessive memory allocation can reduce host consolidation capacity;
- unnecessary checkpoints can increase storage complexity;
- moving a VM to faster storage helps only when storage is actually the bottleneck.

---

# Lab 3.8 — Build a basic performance baseline

## Objective

Record normal host and guest behavior before Day 4 introduces deliberate performance faults.

## Step 1 — Record VM state

~~~powershell
Get-VM | Select-Object Name,State,CPUUsage,MemoryAssigned,Uptime
~~~

## Step 2 — Inspect host CPU

~~~powershell
Get-Counter '\Processor(_Total)\% Processor Time' -SampleInterval 2 -MaxSamples 5
~~~

## Step 3 — Inspect host memory

~~~powershell
Get-Counter '\Memory\Available MBytes' -SampleInterval 2 -MaxSamples 5
~~~

## Step 4 — Inspect disk activity

~~~powershell
Get-Counter '\PhysicalDisk(_Total)\Avg. Disk sec/Read','\PhysicalDisk(_Total)\Avg. Disk sec/Write' -SampleInterval 2 -MaxSamples 5
~~~

Nested virtualization means these values reflect virtualized storage, so treat them as training observations rather than production hardware benchmarks.

## Step 5 — Inspect guest behavior

Inside SRV01:

~~~powershell
Get-Counter '\Processor(_Total)\% Processor Time','\Memory\Available MBytes' -SampleInterval 2 -MaxSamples 5
~~~

Also observe disk and network activity with Task Manager or Resource Monitor.

## Step 6 — Record the baseline

~~~text
HV01 CPU:
HV01 available memory:
HV01 disk read latency:
HV01 disk write latency:

SRV01 CPU:
SRV01 available memory:
Observations:
~~~

This baseline becomes evidence during Day 4 troubleshooting.

---

# Lab 3.9 — Observe a controlled CPU workload

## Objective

Compare host and guest counters before, during and after a short, bounded workload.

This is an observation exercise, not a stress test.

## Step 1 — Capture a short guest baseline

Inside SRV01:

~~~powershell
Get-Counter '\Processor(_Total)\% Processor Time' -SampleInterval 1 -MaxSamples 5
~~~

Record the approximate values.

## Step 2 — Start a bounded CPU workload

Run:

~~~powershell
1..200000 | ForEach-Object { [math]::Sqrt($_) } | Out-Null
~~~

If the workload completes too quickly to observe, repeat it a few times while watching Task Manager.

Do not use an infinite loop.

## Step 3 — Observe the guest

While the workload runs, inspect:

- Task Manager CPU;
- Processor counter values;
- running processes.

Use:

~~~powershell
Get-Counter '\Processor(_Total)\% Processor Time' -SampleInterval 1 -MaxSamples 5
~~~

## Step 4 — Observe HV01

On HV01:

~~~powershell
Get-VM SRV01 | Select-Object Name,CPUUsage,MemoryAssigned
~~~

Also observe host CPU in Task Manager or Performance Monitor.

## Step 5 — Compare guest and host views

Discuss:

- Was guest CPU noticeably higher?
- Did host CPU increase by the same amount?
- Were other VMs competing for CPU?
- Did the workload affect memory or disk significantly?
- Did values return toward baseline afterward?

## Step 6 — Return to baseline

After the command completes, repeat the same counter checks.

The system should return close to the earlier baseline.

## Validation checkpoint

- [ ] Baseline captured.
- [ ] Controlled workload executed.
- [ ] Guest CPU change observed.
- [ ] Host CPU response observed.
- [ ] Guest and host perspectives compared.
- [ ] Counters returned toward baseline.

---

# Module 9 — Bare-metal deployment and PXE concepts

Bare-metal deployment provisions an operating system onto a machine that does not already have a usable OS.

~~~text
Physical/Virtual machine
        |
UEFI / PXE firmware
        |
DHCP / network configuration
        |
PXE boot server
        |
WinPE or deployment environment
        |
OS image / task sequence
        |
Installed server
~~~

## PXE

PXE allows a machine to obtain boot information over the network.

A typical deployment environment requires coordination between:

- DHCP;
- routing/IP helpers when crossing subnets;
- PXE responder;
- boot image;
- installation image or task sequence;
- drivers;
- firmware mode.

## UEFI considerations

Modern Windows Server systems normally use UEFI.

Deployment infrastructure must provide compatible boot files and understand whether the target boots through UEFI or legacy BIOS.

## Windows Deployment Services

Windows Server 2025 still includes Windows Deployment Services, but Microsoft has partially deprecated WDS installation-media workflows and has announced broader WDS deprecation beginning with the Windows Server release after Windows Server 2025.

Microsoft references:

https://learn.microsoft.com/windows/deployment/wds-boot-support

https://learn.microsoft.com/windows-server/get-started/removed-deprecated-features-windows-server-2025

For new deployment designs, administrators should evaluate current Microsoft-supported deployment alternatives rather than assuming legacy WDS workflows are the long-term default.

## Course scope

PXE/bare-metal deployment is discussed conceptually rather than implemented as a full lab because:

- deployment infrastructure can consume significant class time;
- PXE behavior depends heavily on network design;
- the primary course objective is Hyper-V administration.

---

# Break/Fix 3 — Hyper-V Replica health

## Scenario

SRV01 replication was healthy, but Hyper-V now reports a replication problem.

The instructor injects one fault.

Possible faults include:

- HTTPS firewall rule disabled;
- name resolution broken;
- certificate trust or identity issue;
- Replica server disabled;
- insufficient target storage;
- authorization/storage path changed.

Students are not told which fault was introduced.

## Step 1 — Inspect replication

~~~powershell
Get-VMReplication SRV01
Measure-VMReplication SRV01
~~~

## Step 2 — Verify name resolution and network path

~~~powershell
Resolve-DnsName hv02.lab.local
Test-NetConnection hv02.lab.local -Port 443
~~~

## Step 3 — Test Replica protocol connectivity

~~~powershell
Test-VMReplicationConnection -ReplicaServerName "hv02.lab.local" -ReplicaServerPort 443 -AuthenticationType Certificate -CertificateThumbprint "<HV01 certificate thumbprint>"
~~~

## Step 4 — Check HV02 configuration

On HV02:

~~~powershell
Get-Service vmms
Get-NetFirewallRule | Where-Object DisplayName -like "*Replica*"
Get-ChildItem Cert:\LocalMachine\My | Select-Object Subject,Thumbprint,NotAfter,HasPrivateKey
~~~

Also verify Replica configuration in Hyper-V Manager.

## Step 5 — Check target storage

~~~powershell
Get-Volume
Get-ChildItem "D:\Hyper-V\Replica"
~~~

## Student conclusion

Report:

- symptom;
- replication state/health;
- network evidence;
- certificate/authentication evidence;
- storage evidence;
- root cause;
- corrective action;
- validation result.

## Validation after correction

~~~powershell
Get-VMReplication SRV01
Measure-VMReplication SRV01
~~~

Replication should return to a normal/healthy state.

---

# Day 3 review questions

Students should be able to answer:

1. What is the difference between a Standard and Production checkpoint?
2. Why is a checkpoint not a backup?
3. What happens to virtual disk writes after a checkpoint is created?
4. Why can long-lived AVHDX chains become an operational risk?
5. What is the difference between backup, Replica and Failover Clustering?
6. What do RPO and RTO represent?
7. When can Kerberos/HTTP be used for Hyper-V Replica?
8. Why does this course use certificate/HTTPS authentication for Replica?
9. What certificate properties are required for standalone-host Replica?
10. What is the purpose of Test Failover?
11. How does Planned Failover differ from Unplanned Failover?
12. Why is Planned Failover not equivalent to high availability?
13. What is a CSV?
14. Why can local Hyper-V storage be simple but unsuitable for clustered HA?
15. Why should performance troubleshooting start with a baseline?
16. Why can 100% CPU inside a guest mean something different from 100% host CPU?
17. Why should tuning begin with measurement rather than resource increases?
18. What role do Hyper-V Integration Services play?
19. How do Live Migration, Storage Migration and Hyper-V Replica differ?
20. What role do DHCP and PXE play in bare-metal provisioning?
21. Why should new deployment designs be cautious about depending on legacy WDS workflows?

---

# End-of-day validation checklist

- [ ] Production vs Standard checkpoints understood.
- [ ] Checkpoint lifecycle completed.
- [ ] AVHDX behavior observed.
- [ ] Checkpoint vs backup distinction explained.
- [ ] Export/recovery mechanics reviewed.
- [ ] Backup/restore validation concept understood.
- [ ] HV02 running Hyper-V.
- [ ] HV01 and HV02 management connectivity verified.
- [ ] HV02 data disk initialized and Hyper-V storage paths created.
- [ ] HV02 vSW-Lab and LabNAT created.
- [ ] Standalone-host Replica authentication model understood.
- [ ] Lab root and HV01/HV02 Replica certificates created and imported.
- [ ] Lab-only certificate revocation setting configured on HV01 and HV02.
- [ ] HV02 enabled as Replica server.
- [ ] Replica connectivity tested.
- [ ] SRV01 initial replication completed.
- [ ] Replica health inspected.
- [ ] Test Failover completed.
- [ ] Planned vs Unplanned Failover explained.
- [ ] HA vs DR distinction understood.
- [ ] Local, SMB, SAN, CSV and S2D storage concepts discussed.
- [ ] Host and guest performance baseline recorded.
- [ ] Controlled performance workload observed.
- [ ] Basic evidence-driven tuning method understood.
- [ ] Integration Services inspected.
- [ ] Live Migration, Storage Migration and Replica differences understood.
- [ ] Bare-metal/PXE deployment flow understood.
- [ ] Current WDS direction/deprecation discussed.
- [ ] Replica break/fix exercise completed using evidence.

---

# Microsoft references

- Hyper-V checkpoints: https://learn.microsoft.com/windows-server/virtualization/hyper-v/checkpoints
- Enable Hyper-V Replica on a single host: https://learn.microsoft.com/windows-server/virtualization/hyper-v/configure-replication-single-host
- Replicate a virtual machine: https://learn.microsoft.com/windows-server/virtualization/hyper-v/replication-virtual-machines
- Hyper-V Replica failover: https://learn.microsoft.com/windows-server/virtualization/hyper-v/replication-failover
- Hyper-V documentation: https://learn.microsoft.com/windows-server/virtualization/hyper-v/
- Windows Deployment Services boot support: https://learn.microsoft.com/windows/deployment/wds-boot-support
- Deprecated Windows Server features: https://learn.microsoft.com/windows-server/get-started/removed-deprecated-features-windows-server-2025
