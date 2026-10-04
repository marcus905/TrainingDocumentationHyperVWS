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

## Step 2 — Record the current disk chain

~~~powershell
Get-VMHardDiskDrive SRV01
Get-ChildItem "D:\Hyper-V\VHDX" | Select-Object Name,Length,LastWriteTime
~~~

## Step 3 — Create the checkpoint

~~~powershell
Checkpoint-VM -VMName "SRV01" -SnapshotName "Pre-Application-Change"
Get-VMSnapshot -VMName SRV01
~~~

Inspect storage again:

~~~powershell
Get-ChildItem "D:\Hyper-V\VHDX" | Select-Object Name,Length,LastWriteTime
~~~

Look for AVHDX files.

## Step 4 — Make a controlled guest change

Inside SRV01:

~~~powershell
New-Item -ItemType Directory -Path "C:\Lab" -Force
"Created after checkpoint" | Set-Content "C:\Lab\Checkpoint-Test.txt"
~~~

Confirm that C:\Lab\Checkpoint-Test.txt exists.

## Step 5 — Restore the checkpoint

~~~powershell
Restore-VMSnapshot -VMName "SRV01" -Name "Pre-Application-Change" -Confirm:$false
~~~

Start SRV01 if required and verify that the post-checkpoint change has been rolled back.

## Step 6 — Remove the checkpoint

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

# Lab 3.3 — Install and validate Hyper-V on HV02

On HV02:

~~~powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
~~~

After restart:

~~~powershell
Get-WindowsFeature Hyper-V
Get-Service vmms
Get-VMHost
~~~

Create or confirm storage paths:

~~~powershell
New-Item -ItemType Directory -Path "D:\Hyper-V\VMs" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\VHDX" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\Replica" -Force
Set-VMHost -VirtualMachinePath "D:\Hyper-V\VMs" -VirtualHardDiskPath "D:\Hyper-V\VHDX"
~~~

## Verify host-to-host connectivity

HV01 and HV02 must be able to reach each other on their outer management network.

Record the actual management addresses used in your lab:

~~~text
HV01 management IP: __________________
HV02 management IP: __________________
~~~

Verify from each host:

~~~powershell
Test-NetConnection <other-host-IP>
~~~

For Replica, name resolution must match the authentication design.

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

Our course hosts are standalone, so the lab uses the **certificate/HTTPS design**.

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

Prepare trusted host identities for HTTPS-based Hyper-V Replica.

This lab assumes the instructor provides or prepares valid lab certificates before Replica is enabled.

Recommended lab names:

~~~text
hv01.lab.local
hv02.lab.local
~~~

Each host certificate must match its host FQDN and meet the Microsoft Replica certificate requirements.

## Step 1 — Verify name resolution

From HV01:

~~~powershell
Resolve-DnsName hv02.lab.local
~~~

From HV02:

~~~powershell
Resolve-DnsName hv01.lab.local
~~~

If the lab does not provide DNS for these names, the instructor may provide temporary hosts-file mappings for the course environment.

## Step 2 — Inspect the computer certificate store

On each host:

~~~powershell
Get-ChildItem Cert:\LocalMachine\My | Select-Object Subject,Thumbprint,NotAfter,HasPrivateKey
~~~

Locate the certificate whose subject/SAN matches the local host FQDN.

## Step 3 — Record certificate thumbprints

~~~text
HV01 certificate thumbprint: ______________________________
HV02 certificate thumbprint: ______________________________
~~~

## Step 4 — Verify trust

The issuing root CA must exist in the Local Computer Trusted Root Certification Authorities store on both hosts.

The instructor should validate the lab certificate chain before students continue.

> Certificate creation is treated as lab preparation rather than the primary learning objective. The Hyper-V objective is to understand why certificate identity and trust are required for standalone-host Replica.

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
17. What role do DHCP and PXE play in bare-metal provisioning?
18. Why should new deployment designs be cautious about depending on legacy WDS workflows?

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
- [ ] Standalone-host Replica authentication model understood.
- [ ] HV02 enabled as Replica server.
- [ ] Replica connectivity tested.
- [ ] SRV01 initial replication completed.
- [ ] Replica health inspected.
- [ ] Test Failover completed.
- [ ] Planned vs Unplanned Failover explained.
- [ ] HA vs DR distinction understood.
- [ ] Local, SMB, SAN, CSV and S2D storage concepts discussed.
- [ ] Host and guest performance baseline recorded.
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
