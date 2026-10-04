# Day 5 — Advanced Scenarios, Migration and Best Practices

## Learning objectives

By the end of Day 5, students should be able to:

- troubleshoot multi-layer Hyper-V failures with limited guidance;
- correlate application, guest, VM, host, storage and network evidence;
- distinguish root cause from contributing factors;
- map common VMware concepts to Hyper-V terminology and architecture;
- identify migration-planning risks and dependencies;
- explain Live Migration, Storage Migration and workload conversion at a high level;
- apply operational standardization to Hyper-V environments;
- produce useful VM and host documentation;
- apply baseline Hyper-V hardening principles;
- validate Secure Boot and vTPM-related VM security settings;
- complete an end-to-end incident scenario and produce an operational report.

---

# Module 1 — Advanced multi-layer troubleshooting

Day 4 introduced a structured troubleshooting workflow. Day 5 applies it across several layers at once.

~~~text
Application
   |
Guest OS
   |
Virtual CPU / Memory / Disk / NIC
   |
Hyper-V VM configuration
   |
Hyper-V host
   |
Windows networking / storage
   |
Physical or outer virtual infrastructure
   |
External services
~~~

A guest-visible symptom does not necessarily originate in the guest.

Likewise, a host warning is not automatically the cause of a guest application problem.

## Layer-isolation questions

When investigating a complex incident, ask:

- Is the symptom reproducible?
- Does it affect one VM or several?
- Does it follow the VM or stay with the host?
- Is the problem tied to one virtual switch?
- Is storage pressure local to one VM or shared by the host?
- Is the guest healthy while the host is constrained?
- Did the issue begin after a migration, checkpoint, storage move or configuration change?

## Correlation matrix

| Layer | Typical evidence |
|---|---|
| Application | Service logs, application errors, response time |
| Guest OS | Event logs, CPU, memory, disk, network |
| VM configuration | vCPU, memory, VHDX, vNIC, firmware |
| Hyper-V | VMMS, VMWorker, Hyper-V event logs |
| Host | CPU, RAM, storage, network, free capacity |
| External infrastructure | DNS, routing, storage target, authentication |

## Prioritization

In a multi-symptom incident, handle issues by impact and risk.

Typical order:

1. data-loss risk;
2. total service outage;
3. degraded resilience or replication;
4. severe performance degradation;
5. warning-only conditions;
6. optimization opportunities.

Do not spend ten minutes tuning CPU while the host volume is almost full.

---

# Module 2 — VMware to Hyper-V conceptual mapping

This section helps administrators translate familiar virtualization concepts.

The mappings are conceptual, not guaranteed one-to-one equivalents.

| VMware concept | Hyper-V / Microsoft concept |
|---|---|
| ESXi host | Hyper-V host |
| VM | Virtual machine |
| vCPU | Virtual processor |
| VMDK | VHDX |
| vSwitch | Hyper-V Virtual Switch |
| Snapshot | Checkpoint |
| vMotion | Live Migration |
| Storage vMotion | Storage Migration |
| VMware HA | Failover Clustering |
| VMware Tools | Hyper-V Integration Services |
| VM replication | Hyper-V Replica |
| Datastore | Local storage / CSV / SMB / SAN / S2D design |
| vCenter | Windows Admin Center / Failover Cluster Manager / PowerShell / SCVMM depending on design |

## Important differences

Similar names do not imply identical architecture.

Examples:

- a Hyper-V checkpoint is not necessarily operationally identical to a VMware snapshot;
- Failover Clustering and VMware HA solve similar availability problems but use different management and storage models;
- Hyper-V Replica is designed for asynchronous DR and is separate from Live Migration;
- Hyper-V management is deeply integrated with Windows Server, PowerShell and Windows security.

---

# Module 3 — Migration planning

A successful migration starts with discovery rather than conversion.

## Inventory first

For every source VM, record:

- VM name;
- owner;
- purpose;
- guest OS;
- CPU;
- memory;
- disks;
- disk format;
- disk size and used capacity;
- firmware / boot mode;
- network adapters;
- VLANs;
- IP addressing;
- DNS dependencies;
- application dependencies;
- backup;
- monitoring;
- security agents;
- RPO/RTO;
- acceptable downtime.

## Compatibility assessment

Before migration, determine:

- whether the guest OS is supported on Hyper-V;
- whether UEFI/BIOS conversion is required;
- whether source disks need format conversion;
- whether application licensing depends on virtual hardware;
- whether static MAC addresses are significant;
- whether VLAN and IP settings can be recreated;
- whether tools/drivers from the source hypervisor must be removed;
- whether Secure Boot can be enabled;
- whether time synchronization behavior changes.

## Dependency mapping

A VM rarely exists alone.

~~~text
APP01
 |
+-- DNS
+-- Database
+-- File share
+-- Certificate service
+-- Firewall rules
+-- Monitoring
+-- Backup
~~~

Migration planning should include both the VM and its dependencies.

## Cutover planning

A migration plan should define:

~~~text
Pre-checks
   |
Source shutdown / quiesce
   |
Disk / VM conversion
   |
Hyper-V configuration
   |
Network cutover
   |
Application validation
   |
Rollback decision point
~~~

## Rollback

A rollback plan should state:

- when rollback is allowed;
- what triggers rollback;
- how source consistency is preserved;
- how DNS/IP changes are reversed;
- how users are notified;
- who makes the go/no-go decision.

## Migration validation

A migration is not complete because the VM boots.

Validate:

- OS boot;
- network connectivity;
- DNS;
- application service;
- authentication;
- disk visibility;
- time;
- backup;
- monitoring;
- security tooling;
- performance;
- recovery configuration.

---

# Module 4 — Live Migration and Storage Migration

Microsoft references:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/manage/live-migration-overview

https://learn.microsoft.com/windows-server/virtualization/hyper-v/deploy/set-up-hosts-for-live-migration-without-failover-clustering

https://learn.microsoft.com/windows-server/virtualization/hyper-v/manage/use-live-migration-without-failover-clustering-to-move-a-virtual-machine

## Live Migration

Live Migration moves a running VM from one compatible Hyper-V host to another with minimal perceived downtime.

Typical use cases:

- host maintenance;
- hardware servicing;
- load redistribution;
- planned infrastructure work.

Requirements include compatible hosts, appropriate permissions, networking and authentication.

## Storage Migration

Storage Migration moves VM files between storage locations.

Typical use cases:

- evacuating old storage;
- balancing capacity;
- moving VHDX files to faster storage;
- reorganizing VM storage.

## Security and authentication

Authentication and delegation choices matter for Live Migration.

Windows Server 2025 security defaults can affect older migration assumptions, so administrators should follow current Microsoft guidance when configuring CredSSP, Kerberos constrained delegation or workgroup-cluster scenarios.

## Course scope

Because this is a nested remote lab, Live Migration is optional.

The concept is required; the hands-on demonstration depends on available RAM, storage and network stability.

---

# Optional Lab 5.1 — Inspect Live Migration configuration

On HV01:

~~~powershell
Get-VMHost | Select-Object VirtualMachineMigrationEnabled,VirtualMachineMigrationAuthenticationType
~~~

Review migration-related settings in Hyper-V Manager:

~~~text
Hyper-V Settings
  -> Live Migrations
~~~

Do not enable or change migration settings unless the instructor confirms that the local nested environment is suitable.

## Discussion

Students should explain:

- which host receives the VM;
- whether storage also moves;
- what authentication is used;
- which network carries migration traffic;
- how the workload is validated after migration.

---

# Module 5 — Operational best practices

Stable Hyper-V environments depend on consistency.

## Standardize naming

Document conventions for:

- Hyper-V hosts;
- VMs;
- virtual switches;
- storage folders;
- VHDX files;
- Replica relationships.

Example:

~~~text
Hosts:
HV01
HV02

VMs:
DC01
SRV01
APP01

Switches:
vSW-Lab
vSW-Management
vSW-RecoveryTest
~~~

## Standardize paths

Example:

~~~text
D:\Hyper-V\VMs
D:\Hyper-V\VHDX
D:\Hyper-V\ISO
D:\Hyper-V\Replica
D:\Hyper-V\Export
~~~

## Standardize configuration

Define defaults for:

- VM generation;
- Secure Boot;
- Dynamic Memory;
- vCPU sizing;
- checkpoint policy;
- storage placement;
- switch naming;
- backup expectations;
- monitoring;
- Replica where required.

## Standardize change control

Before a production change, record:

- reason;
- owner;
- expected impact;
- maintenance window;
- rollback;
- validation steps.

Avoid changes whose rollback plan is simply:

~~~text
We will see what happens.
~~~

---

# Lab 5.2 — Build an operational inventory

## Objective

Create a concise inventory of the current Hyper-V environment.

On HV01:

~~~powershell
Get-VM |
    Select-Object Name,State,CPUUsage,MemoryAssigned,Uptime
~~~

VM CPU:

~~~powershell
Get-VMProcessor * |
    Select-Object VMName,Count
~~~

VM memory:

~~~powershell
Get-VMMemory * |
    Select-Object VMName,DynamicMemoryEnabled,Startup,Minimum,Maximum
~~~

Networking:

~~~powershell
Get-VMNetworkAdapter * |
    Select-Object VMName,SwitchName,MacAddress,Status
~~~

Storage:

~~~powershell
Get-VMHardDiskDrive * |
    Select-Object VMName,Path,ControllerType,ControllerNumber,ControllerLocation
~~~

Checkpoints:

~~~powershell
Get-VMSnapshot *
~~~

## Student output

Create an inventory table containing at least:

- VM;
- state;
- vCPU;
- memory model;
- switch;
- VHDX paths;
- checkpoint status;
- recovery/Replica status.

---

# Module 6 — Documentation and standardization

For every production VM, document:

~~~text
VM name:
Owner:
Purpose:
Guest OS:
Generation:
vCPU:
Memory:
OS disk:
Data disks:
Virtual switch:
VLAN:
IPv4:
DNS:
Backup:
Replica:
RPO:
RTO:
Monitoring:
Dependencies:
Maintenance window:
Notes:
~~~

## Why documentation matters

Documentation supports:

- incident response;
- migration;
- recovery;
- auditing;
- lifecycle planning;
- onboarding;
- capacity planning.

A configuration that exists only in one administrator's memory is an operational risk.

---

# Module 7 — Baseline Hyper-V security

Microsoft reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/plan/plan-hyper-v-security-in-windows-server

## Secure the host

Baseline practices include:

- keep the host patched;
- minimize unnecessary software;
- use least privilege;
- restrict administrative access;
- use Windows Firewall;
- protect management interfaces;
- monitor security events;
- protect VM and VHDX storage;
- avoid mounting untrusted VHD/VHDX files;
- enable nested virtualization only where required.

Nested virtualization is necessary for this course, but that does not make it a default production recommendation.

## Secure the guest

For supported workloads:

- use Generation 2;
- enable Secure Boot;
- patch the guest OS;
- maintain guest security software;
- restrict unnecessary services;
- use role-appropriate Windows security baselines;
- secure virtual network placement.

## Secure Boot

Generation 2 VMs support Secure Boot.

Microsoft reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/learn-more/Generation-2-virtual-machine-security-settings-for-Hyper-V

Inspect:

~~~powershell
Get-VMFirmware SRV01 | Select-Object SecureBoot
~~~

## Virtual TPM

Generation 2 VMs can use a virtual TPM where the security design supports it.

Inspect security settings:

~~~powershell
Get-VMSecurity SRV01
~~~

vTPM can support guest features that rely on TPM-backed security.

Do not enable security features blindly. Confirm guest support and recovery implications first.

## Backup security

Backups are security-sensitive assets.

Protect:

- backup credentials;
- backup repositories;
- encryption keys;
- recovery documentation;
- administrative access.

A ransomware-resistant design should assume that production credentials may become compromised.

---

# Lab 5.3 — Security baseline inspection

## Step 1 — Inspect firmware

~~~powershell
Get-VMFirmware SRV01
~~~

Verify:

- Generation 2 behavior;
- Secure Boot state;
- boot order.

## Step 2 — Inspect VM security

~~~powershell
Get-VMSecurity SRV01
~~~

Discuss:

- Secure Boot;
- encryption-support concepts;
- vTPM;
- shielding/guarded fabric as an advanced design.

## Step 3 — Inspect host update/security posture

Review:

- current patch level;
- Windows Defender/EDR status;
- firewall state;
- Hyper-V administrators;
- unexpected installed software.

The exact enterprise tooling can differ by organization.

## Validation

Students should be able to identify at least three host-side and three VM-side security controls.

---

# Module 8 — Operational readiness checklist

Before declaring a Hyper-V workload production-ready, validate:

## Host

- [ ] host patched;
- [ ] storage capacity healthy;
- [ ] management network working;
- [ ] monitoring configured;
- [ ] backup configured;
- [ ] security controls active;
- [ ] recovery design documented.

## VM

- [ ] correct generation;
- [ ] correct CPU/memory;
- [ ] expected VHDX files;
- [ ] expected switch/VLAN;
- [ ] Secure Boot appropriate;
- [ ] guest patched;
- [ ] backup tested;
- [ ] monitoring enabled;
- [ ] dependencies documented.

## Recovery

- [ ] backup restore tested;
- [ ] Replica healthy if used;
- [ ] RPO/RTO documented;
- [ ] recovery ownership defined;
- [ ] test-failover process documented.

---

# Final end-to-end scenario — Local capstone incident

The final scenario uses the same remote/local principle as Day 4.

Each student creates a controlled set of faults locally, then troubleshoots and restores the environment.

## Capstone topology

Use the course environment rather than creating four additional large VMs.

~~~text
HV01
+-- SRV01
+-- DC01

HV02
+-- SRV01 Replica
~~~

The instructor assigns each student **three capstone codes**.

Prefer codes from different layers.

## Capstone fault pool

### Code A — Wrong SRV01 switch

#### Break

On HV01:

~~~powershell
Connect-VMNetworkAdapter -VMName SRV01 -SwitchName "vSW-Private"
~~~

#### Symptom

> SRV01 has lost normal lab-network connectivity.

#### Reset

~~~powershell
Connect-VMNetworkAdapter -VMName SRV01 -SwitchName "vSW-Lab"
~~~

---

### Code B — Bad DNS

#### Break

Inside SRV01:

~~~powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 172.22.0.254
~~~

#### Symptom

> SRV01 can reach IP addresses but hostname-based access fails.

#### Reset

~~~powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 1.1.1.1
~~~

---

### Code C — Constrained memory

#### Break

On HV01:

~~~powershell
Stop-VM SRV01
Set-VMMemory -VMName SRV01 -DynamicMemoryEnabled $true -MinimumBytes 512MB -StartupBytes 1GB -MaximumBytes 1GB
Start-VM SRV01
~~~

#### Symptom

> SRV01 starts but becomes sluggish under normal activity.

#### Reset

~~~powershell
Stop-VM SRV01
Set-VMMemory -VMName SRV01 -DynamicMemoryEnabled $true -MinimumBytes 1GB -StartupBytes 2GB -MaximumBytes 4GB
Start-VM SRV01
~~~

---

### Code D — Checkpoint growth

#### Break

On HV01:

~~~powershell
Checkpoint-VM -VMName "SRV01" -SnapshotName "Capstone-Checkpoint"
~~~

Inside SRV01:

~~~powershell
New-Item -ItemType Directory -Path "C:\CapstoneGrowth" -Force
1..3000 | ForEach-Object {
    "Capstone data $_" | Out-File "C:\CapstoneGrowth\file-$_.txt"
}
~~~

#### Symptom

> Free space on the Hyper-V data volume is decreasing while SRV01 remains online.

#### Reset

On HV01:

~~~powershell
Remove-VMSnapshot -VMName "SRV01" -Name "Capstone-Checkpoint"
~~~

Inside SRV01:

~~~powershell
Remove-Item "C:\CapstoneGrowth" -Recurse -Force
~~~

---

### Code E — Replica HTTPS blocked

#### Break

On HV02:

~~~powershell
Get-NetFirewallRule | Where-Object DisplayName -like "*Replica*"
~~~

Disable the identified HTTPS Replica listener rule:

~~~powershell
Disable-NetFirewallRule -Name "<rule name>"
~~~

#### Symptom

> SRV01 Replica health is degraded and Replica connectivity fails.

#### Reset

~~~powershell
Enable-NetFirewallRule -Name "<rule name>"
~~~

Then validate:

~~~powershell
Get-VMReplication SRV01
Measure-VMReplication SRV01
~~~

---

### Code F — Missing VM storage dependency

#### Break

On HV01:

~~~powershell
New-VM -Name "CAPBROKEN01" -Generation 2 -MemoryStartupBytes 1GB -Path "D:\Hyper-V\VMs" -NoVHD
Add-VMHardDiskDrive -VMName "CAPBROKEN01" -Path "D:\Hyper-V\VHDX\MISSING-CAPSTONE.vhdx"
Start-VM CAPBROKEN01
~~~

#### Symptom

> CAPBROKEN01 appears in Hyper-V Manager but cannot start.

#### Reset

~~~powershell
Remove-VM -Name "CAPBROKEN01" -Force
~~~

---

## Suggested three-fault combinations

Examples:

~~~text
A + D + E
B + C + F
A + C + E
B + D + F
A + B + D
C + D + E
~~~

Avoid assigning too many faults that create the same symptom.

## Student mission

1. Inventory the environment.
2. Write separate problem statements.
3. Establish scope and impact.
4. Prioritize the faults.
5. Collect evidence.
6. Build hypotheses.
7. Prove root causes.
8. Apply the minimum safe corrections.
9. Validate every corrected layer.
10. Confirm the lab has returned to the expected baseline.
11. Produce the final report.

## Instructor role

The instructor should coach by asking questions rather than revealing the fault.

Useful prompts:

- What layer have you proven healthy?
- What evidence supports that hypothesis?
- Which issue has the highest operational risk?
- What would you check next?
- What evidence proves the correction worked?
- What preventive control would stop recurrence?

---

# Final operational report

Each student produces:

~~~text
Environment summary:

VM inventory:

Incident 1
Symptom:
Scope:
Impact:
Evidence:
Root cause:
Correction:
Validation:
Prevention:

Incident 2
Symptom:
Scope:
Impact:
Evidence:
Root cause:
Correction:
Validation:
Prevention:

Incident 3
Symptom:
Scope:
Impact:
Evidence:
Root cause:
Correction:
Validation:
Prevention:

Migration considerations discovered:

Security observations:

Operational standardization recommendations:

Final environment status:
~~~

---

# Day 5 review questions

1. Why should multi-layer incidents be prioritized by impact and risk?
2. Why is VMware-to-Hyper-V mapping conceptual rather than one-to-one?
3. What information belongs in a migration inventory?
4. Why must application dependencies be mapped before migration?
5. Why is a rollback plan required before cutover?
6. How does Live Migration differ from Storage Migration and Replica?
7. Why should VM naming and storage paths be standardized?
8. What information should be documented for every production VM?
9. Why is Generation 2 preferred for modern supported Windows guests?
10. What security benefit does Secure Boot provide?
11. What is the purpose of a virtual TPM?
12. Why should nested virtualization not automatically be enabled in production?
13. Why should backup repositories be treated as security-sensitive systems?
14. Why does a successful VM boot not prove a migration succeeded?
15. What evidence proves that an incident has actually been resolved?

---

# End-of-course validation checklist

- [ ] Multi-layer troubleshooting process demonstrated.
- [ ] VM/host/storage/network evidence correlated.
- [ ] VMware concepts mapped to Hyper-V equivalents.
- [ ] Migration inventory requirements understood.
- [ ] Cutover and rollback concepts understood.
- [ ] Live Migration vs Storage Migration vs Replica explained.
- [ ] Operational inventory created.
- [ ] Naming/path/configuration standards understood.
- [ ] Production VM documentation template understood.
- [ ] Hyper-V host hardening principles reviewed.
- [ ] Secure Boot inspected.
- [ ] VM security/vTPM concepts reviewed.
- [ ] Operational readiness checklist completed.
- [ ] Three-fault capstone completed.
- [ ] Final operational report delivered.
- [ ] Student can explain how the Day 1–5 environment evolved.

---

# Microsoft references

- Hyper-V security planning: https://learn.microsoft.com/windows-server/virtualization/hyper-v/plan/plan-hyper-v-security-in-windows-server
- Generation 2 VM security: https://learn.microsoft.com/windows-server/virtualization/hyper-v/learn-more/Generation-2-virtual-machine-security-settings-for-Hyper-V
- Live Migration overview: https://learn.microsoft.com/windows-server/virtualization/hyper-v/manage/live-migration-overview
- Configure nonclustered Live Migration: https://learn.microsoft.com/windows-server/virtualization/hyper-v/deploy/set-up-hosts-for-live-migration-without-failover-clustering
- Use nonclustered Live Migration: https://learn.microsoft.com/windows-server/virtualization/hyper-v/manage/use-live-migration-without-failover-clustering-to-move-a-virtual-machine
- Hyper-V documentation: https://learn.microsoft.com/windows-server/virtualization/hyper-v/
