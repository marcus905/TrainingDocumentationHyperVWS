# Day 5 — Advanced Scenarios, Migration and Best Practices

## Learning objectives
Students should be able to:
- troubleshoot multi-layer Hyper-V failures;
- correlate host, VM, network and storage evidence;
- map common VMware concepts to Hyper-V;
- identify migration-planning considerations;
- apply operational standardization;
- discuss baseline hardening;
- complete an end-to-end scenario with limited instructor guidance.

## Module 1 — Multi-layer troubleshooting
~~~text
Application
   |
Guest OS
   |
Virtual CPU / Memory / Disk / NIC
   |
Hyper-V
   |
Windows host
   |
Physical CPU / RAM / Storage / Network
   |
External infrastructure
~~~

A guest-visible symptom does not necessarily originate in the guest.

## Module 2 — VMware to Hyper-V conceptual mapping
| VMware concept | Hyper-V / Microsoft concept |
|---|---|
| ESXi host | Hyper-V host |
| VM | VM |
| vCPU | Virtual processor |
| VMDK | VHDX |
| vSwitch | Hyper-V Virtual Switch |
| Snapshot | Checkpoint |
| vMotion | Live Migration |
| Storage vMotion | Storage Migration |
| HA | Failover Clustering |
| VMware Tools | Hyper-V Integration Services |
| VM replication | Hyper-V Replica |
| Datastore | Local storage / CSV / SMB or other storage design |
| vCenter | Windows Admin Center / Failover Cluster Manager / PowerShell / System Center depending on architecture |

These are conceptual mappings, not guaranteed one-to-one equivalents.

## Migration planning
Assess:
- VM inventory;
- guest OS support;
- CPU/memory sizing;
- disks and formats;
- networking and VLANs;
- firmware/boot mode;
- application dependencies;
- backup and monitoring;
- security tooling;
- downtime constraints;
- DNS/IP dependencies;
- recovery requirements;
- rollback plan.

## Module 3 — Operational best practices
Standardize:
- host and VM naming;
- VHDX paths;
- ISO locations;
- vSwitch names;
- VLAN documentation;
- IP allocation;
- backup/checkpoint policy;
- monitoring baseline;
- maintenance windows.

Document for every production VM:
- owner;
- purpose;
- OS;
- vCPU;
- memory;
- disks;
- networking;
- VLAN;
- backup;
- RTO/RPO or recovery expectations;
- dependencies.

## Module 4 — Baseline security
Discuss:
- least privilege;
- administrative separation;
- patching;
- secure remote management;
- firewall;
- credential hygiene;
- Secure Boot;
- TPM/vTPM concepts;
- logging/auditing;
- backup protection;
- security-tool compatibility.

## Final end-to-end scenario
~~~text
HV01
+-- APP01
+-- DB01
+-- FILE01
+-- WEB01

HV02
+-- replica workloads
~~~

Reported symptoms:
- APP01 has intermittent connectivity.
- DB01 has poor storage performance.
- FILE01 contains old checkpoints.
- WEB01 replication is unhealthy.
- HV02 has unexpected memory pressure.

Student mission:
1. Inventory environment.
2. Identify symptoms.
3. Establish scope.
4. Prioritize.
5. Collect evidence.
6. Identify root cause.
7. Implement safe correction.
8. Validate.
9. Document.

## Optional advanced demonstration — Live Migration
Discuss or demonstrate non-clustered Live Migration if resources permit.

Reference:
https://learn.microsoft.com/windows-server/virtualization/hyper-v/deploy/set-up-hosts-for-live-migration-without-failover-clustering

Because this is a nested remote lab, Live Migration is optional on resource-constrained student machines.

## Final deliverable
Each student produces an operational report with:
- environment summary;
- discovered problems;
- root cause;
- remediation;
- verification;
- preventive actions.
