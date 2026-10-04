# Module 04 — Migration Technologies

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

## Optional inspection

~~~powershell
Get-VMHost | Select-Object VirtualMachineMigrationEnabled,VirtualMachineMigrationAuthenticationType
~~~

Review Hyper-V Settings > Live Migrations.

Authentication/delegation choices matter. Use current Microsoft guidance for Windows Server 2025 deployments.

Hands-on Live Migration remains optional in the nested remote lab.
