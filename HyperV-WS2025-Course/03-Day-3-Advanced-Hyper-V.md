# Day 3 — Advanced Hyper-V and Real-World Scenarios

## Learning objectives
Students should be able to:
- manage VM checkpoints correctly;
- explain checkpoint vs backup;
- configure a second Hyper-V host;
- understand Hyper-V Replica;
- perform test and recovery exercises;
- compare local and shared storage concepts;
- inspect host and guest performance;
- discuss bare-metal and PXE provisioning.

## Module 1 — Checkpoints
Topics:
- Production checkpoints
- Standard checkpoints
- AVHDX files
- checkpoint chains
- merge behavior
- operational risks

Reference: https://learn.microsoft.com/windows-server/virtualization/hyper-v/checkpoints

## Lab 3.1 — Create and restore a checkpoint
~~~powershell
Checkpoint-VM -VMName "SRV01" -SnapshotName "Pre-Application-Install"
Get-VMSnapshot -VMName SRV01
~~~

Tasks:
1. Make a controlled guest change.
2. Restore the checkpoint.
3. Validate rollback.
4. Inspect VHDX/AVHDX files.
5. Remove the checkpoint.
6. Observe merge behavior.

Key message: **a checkpoint is not a backup**.

## Lab 3.2 — Prepare HV02
~~~powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
Get-WindowsFeature Hyper-V
Get-Service vmms
~~~

Verify HV01/HV02 connectivity and name resolution.

## Module 2 — Hyper-V Replica
~~~text
HV01                         HV02
SRV01
  |
  +------ asynchronous -----> SRV01 replica
~~~

Topics:
- primary and replica server;
- authentication;
- initial replication;
- replication health;
- test failover;
- planned failover;
- recovery.

Reference: https://learn.microsoft.com/windows-server/virtualization/hyper-v/configure-replication-single-host

## Lab 3.3 — Configure Replica
1. Enable HV02 as Replica server.
2. Allow required replication traffic.
3. Enable replication for SRV01.
4. Perform initial replication.
5. Inspect state and health.

Useful commands:
~~~powershell
Get-VMReplication
Measure-VMReplication
~~~

## Lab 3.4 — Test failover
Perform a **Test Failover** first.

Validate:
- test VM starts;
- production replication continues;
- test networking does not create IP conflicts.

Then discuss Planned Failover and unexpected primary-host loss.

## Module 3 — Hyper-V storage
Discuss:
- local VHDX;
- fixed and dynamically expanding disks;
- differencing disks;
- SMB-based storage;
- CSV concepts;
- SAN concepts;
- latency, throughput and queueing;
- oversubscription.

## Lab 3.5 — Performance baseline
Collect baseline data before creating load.

Host tools:
- Task Manager
- Resource Monitor
- Performance Monitor
- Hyper-V counters

Useful commands:
~~~powershell
Get-Counter
Get-VM
Get-VMHost
~~~

Guest observations:
- CPU utilization
- available memory
- disk latency/throughput
- network throughput

A guest showing high CPU does not automatically prove physical host CPU saturation.

## Module 4 — Bare metal and PXE concepts
Discuss:
- UEFI boot;
- PXE;
- DHCP interaction;
- deployment services;
- installation images;
- unattended deployment;
- driver/firmware dependencies.

## Break/Fix 3 — Replica health
Possible faults:
- firewall blocked;
- DNS failure;
- authentication mismatch;
- insufficient target storage;
- Replica configuration problem.

Students must identify, prove, correct and revalidate the issue.

## End-of-day validation
- [ ] Checkpoint lifecycle understood.
- [ ] Checkpoint vs backup explained.
- [ ] HV02 operational.
- [ ] Replica configured.
- [ ] Test failover completed.
- [ ] Replication health inspectable.
- [ ] Storage tradeoffs discussed.
- [ ] Host/guest performance baseline collected.

## Microsoft references
- https://learn.microsoft.com/windows-server/virtualization/hyper-v/checkpoints
- https://learn.microsoft.com/windows-server/virtualization/hyper-v/configure-replication-single-host
- https://learn.microsoft.com/windows-server/virtualization/hyper-v/
