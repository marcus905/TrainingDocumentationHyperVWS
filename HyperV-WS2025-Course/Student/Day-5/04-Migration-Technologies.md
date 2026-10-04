# Module 04 — Migration Technologies

## Introduction

Live Migration, Storage Migration, and Hyper-V Replica can all involve moving or copying VM-related state, but they serve different operational goals. This module makes that distinction explicit before discussing real migration designs.

## Goal

Distinguish Live Migration, Storage Migration and Replica.

### Live Migration

Moves running workload execution between compatible Hyper-V hosts with minimal perceived downtime.

Typical use: host maintenance and planned infrastructure work.

### Storage Migration

Moves VM files between storage locations.

Typical use: storage maintenance, capacity balancing and storage refresh.

### Hyper-V Replica

Maintains an asynchronous recovery copy for disaster recovery.


## Concepts to keep in mind

Live Migration moves running compute ownership, Storage Migration relocates VM files, and Replica maintains an asynchronous recovery copy. Authentication, networking, compatibility, storage, and downtime expectations differ between them.

## Optional inspection

~~~powershell
Get-VMHost | Select-Object VirtualMachineMigrationEnabled,VirtualMachineMigrationAuthenticationType
~~~

Review Hyper-V Settings > Live Migrations.

Authentication/delegation choices matter. Use current Microsoft guidance for Windows Server 2025 deployments.

Hands-on Live Migration remains optional in the nested remote lab.


## What you will do

Inspect the current host migration settings, compare the three technologies against maintenance/refresh/DR scenarios, and discuss which authentication and network paths would need to be designed in production.


## What you should observe

Enabling a migration feature is not the same as proving the environment is ready for it; host compatibility, permissions, authentication, and network design remain dependencies.


## Validation checkpoint

For a host-maintenance, storage-refresh, and disaster-recovery requirement, select the appropriate technology and explain why the others do not directly solve it.


## Expected end state

You can distinguish the migration technologies and identify the prerequisites that would need validation before production use.
