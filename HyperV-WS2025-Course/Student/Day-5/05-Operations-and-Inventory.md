# Module 05 — Operations, Standardization and Inventory

## Goal

Turn a working lab into an operationally manageable environment.

Standardize:

- host/VM naming;
- vSwitch names;
- VHDX paths;
- ISO locations;
- VLAN/IP documentation;
- checkpoint policy;
- backup expectations;
- monitoring;
- maintenance windows;
- change/rollback plans.

## Build inventory

~~~powershell
Get-VM | Select-Object Name,State,CPUUsage,MemoryAssigned,Uptime
Get-VMProcessor * | Select-Object VMName,Count
Get-VMMemory * | Select-Object VMName,DynamicMemoryEnabled,Startup,Minimum,Maximum
Get-VMNetworkAdapter * | Select-Object VMName,SwitchName,MacAddress,Status
Get-VMHardDiskDrive * | Select-Object VMName,Path,ControllerType
Get-VMSnapshot *
~~~

## Per-VM documentation

Record owner, purpose, OS, generation, CPU, memory, disks, network/VLAN/IP/DNS, backup, Replica, RPO/RTO, monitoring, dependencies and maintenance window.

Configuration that exists only in one administrator's memory is an operational risk.
