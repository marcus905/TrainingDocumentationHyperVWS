# Module 02 — VMware to Hyper-V Mapping


## Concepts to keep in mind

Map intent first—compute movement, storage movement, HA, DR, virtual networking, guest integration—then examine how Hyper-V implements that intent.

## Introduction

Administrators moving from VMware often recognize familiar concepts in Hyper-V, but similar purposes do not imply identical architecture or behavior. This module uses conceptual mapping as a bridge while highlighting where assumptions can become migration risks.

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


## What you will do

Walk through the mapping table and, for each important pair, state one similarity and one architectural or operational difference.


## What you should observe

Terms such as snapshot/checkpoint, vMotion/Live Migration, HA/clustering, and datastore/storage design are useful translations but not direct one-to-one implementations.


## Validation checkpoint

Choose three mappings and explain what could go wrong if an administrator assumed the VMware behavior was identical in Hyper-V.


## Expected end state

You can communicate across VMware and Hyper-V terminology without carrying unsafe one-to-one assumptions into a migration.
