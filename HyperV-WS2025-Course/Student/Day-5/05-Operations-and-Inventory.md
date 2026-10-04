# Module 05 — Operations, Standardization and Inventory

## Introduction

A technically correct environment can still be hard to operate if naming, paths, documentation, monitoring, and change practices are inconsistent. This module turns the lab into something another administrator could understand and support.

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


## Concepts to keep in mind

Standardization reduces ambiguity. Inventory describes current state; documentation adds ownership, dependencies, recovery expectations, and operational context; change control defines how state is altered safely.

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


## What you will do

Collect a current Hyper-V inventory, compare it to the course standards, and build a concise per-VM operational record rather than only copying raw command output.


## What you should observe

PowerShell can collect configuration quickly, but commands alone do not reveal owner, business purpose, RPO/RTO, maintenance window, or application dependencies.


## Validation checkpoint

Identify one configuration item that can be discovered automatically and one operational fact that requires documentation outside Hyper-V.


## Expected end state

The environment has a usable inventory and a documentation model that supports troubleshooting, migration, recovery, and handoff.
