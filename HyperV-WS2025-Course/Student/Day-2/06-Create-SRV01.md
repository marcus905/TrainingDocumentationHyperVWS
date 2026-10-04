# Module 06 — Create SRV01

## Introduction

SRV01 is the main workload VM for the rest of the course. Rather than accepting wizard defaults, this module deliberately applies the CPU, Dynamic Memory, storage, firmware, and network decisions discussed earlier.


## Goal

Create SRV01 with the agreed Generation 2, CPU, Dynamic Memory, storage, firmware, and virtual-network configuration.

## Concepts to keep in mind

VM configuration and guest configuration are separate layers. Hyper-V defines virtual hardware; Windows inside the VM later configures its own IP addressing, disks, services, and applications.

## What you will do

Create SRV01 as Generation 2, set two vCPUs and Dynamic Memory boundaries, connect it to vSW-Lab, and inspect its firmware, disks, memory, and vNIC before installing the OS.

## Specification

- Generation 2
- 2 vCPU
- Dynamic Memory: 2 GB startup, 1 GB min, 4 GB max
- 50 GB OS VHDX
- vSW-Lab
- Guest IP later: 172.22.0.20/24

~~~powershell
New-VM -Name "SRV01" -Generation 2 -MemoryStartupBytes 2GB -Path "D:\Hyper-V\VMs" -NewVHDPath "D:\Hyper-V\VHDX\SRV01.vhdx" -NewVHDSizeBytes 50GB -SwitchName "vSW-Lab"
Set-VMProcessor SRV01 -Count 2
Set-VMMemory SRV01 -DynamicMemoryEnabled $true -MinimumBytes 1GB -StartupBytes 2GB -MaximumBytes 4GB
~~~

Inspect with `Get-VM`, `Get-VMProcessor`, `Get-VMMemory`, `Get-VMNetworkAdapter`, `Get-VMHardDiskDrive` and `Get-VMFirmware`.

## What you should observe

At this point SRV01 exists as virtual hardware but has no installed operating system. Hyper-V can still report all of its configured resources.

## Validation checkpoint

Verify generation, vCPU count, Dynamic Memory values, VHDX path, vSwitch, and Secure Boot state.

## Expected end state

SRV01 has a known and documented virtual-hardware baseline ready for guest installation.
