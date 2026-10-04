# Module 04 — Create HV01 and HV02

## Introduction

HV01 and HV02 form the outer server layer for the whole week. HV01 becomes the main nested Hyper-V host, while HV02 later becomes the recovery and Replica host, so creating them with a consistent baseline now simplifies every later comparison.

## Goal

Create the two outer Windows Server VMs used throughout the course.

## Concepts to keep in mind

The outer hosts use fixed memory for predictable nested-host behavior. Their 200 GB data disks are dynamically expanding files on the physical machine but will initially appear as raw disks inside Windows Server.

## What you will do

Create both Generation 2 outer VMs with the agreed CPU, static memory, OS/data disks, ISO attachment, and vSW-Course network adapter, then inspect the resulting virtual hardware.

## Specification

| Setting | HV01 | HV02 |
|---|---:|---:|
| Generation | 2 | 2 |
| vCPU | 4 | 4 |
| RAM | 8 GB static | 8 GB static |
| OS disk | 100 GB dynamic | 100 GB dynamic |
| Data disk | 200 GB dynamic | 200 GB dynamic |
| Network | vSW-Course | vSW-Course |

Use Hyper-V Manager or PowerShell. Attach the Windows Server 2025 ISO and configure the virtual DVD as first boot device.

After creation verify:

~~~powershell
Get-VM HV01,HV02 | Select-Object Name,State,Generation,MemoryStartup
Get-VMProcessor HV01,HV02 | Select-Object VMName,Count
Get-VMHardDiskDrive HV01,HV02
Get-VMDvdDrive HV01,HV02
~~~

## What you should observe

Each VM should show one OS disk, one data disk, one virtual DVD with the ISO, one vNIC on vSW-Course, four vCPUs, and static startup memory.

## Validation checkpoint

Inspect VM, processor, hard-disk, DVD, and network-adapter configuration before starting installation.

## Expected end state

HV01 and HV02 exist with matching outer architecture and are ready for Windows Server installation.
