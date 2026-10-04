# Module 02 — VMware to Hyper-V Mapping

## Goal

Translate familiar concepts without assuming identical architectures.

| VMware | Hyper-V / Microsoft |
|---|---|
| ESXi host | Hyper-V host |
| VMDK | VHDX |
| vSwitch | Hyper-V Virtual Switch |
| Snapshot | Checkpoint |
| vMotion | Live Migration |
| Storage vMotion | Storage Migration |
| HA | Failover Clustering |
| VMware Tools | Integration Services |
| VM replication | Hyper-V Replica |
| Datastore | Local/CSV/SMB/SAN/S2D design |
| vCenter | WAC / Failover Cluster Manager / PowerShell / SCVMM depending on design |

These are conceptual bridges, not one-to-one implementations.

Be especially careful with checkpoints/snapshots, HA architecture, Replica vs Live Migration, storage models and management/security integration.
