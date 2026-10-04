# Module 03 — Prepare HV02

## Introduction

Replica only becomes meaningful when the recovery host is prepared as deliberately as the primary host. This module repeats the Day 1/Day 2 patterns on HV02 so recovery does not depend on hidden infrastructure.

## Goal

Turn HV02 into the recovery-side Hyper-V host.


## Concepts to keep in mind

A recovery host needs compute, storage, management connectivity, and a workload network. Matching switch names simplify recovery, but HV01 and HV02 still have separate isolated nested networks.

## Initialize the training disk

~~~powershell
Get-Disk
~~~

Identify the approximately 200 GB raw disk. Do not assume the disk number.

~~~powershell
Initialize-Disk -Number <DiskNumber> -PartitionStyle GPT
New-Partition -DiskNumber <DiskNumber> -UseMaximumSize -DriveLetter D
Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel "Hyper-V Data" -Confirm:$false
~~~

## Install Hyper-V

~~~powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
~~~

Create D:\Hyper-V\VMs, VHDX, Replica and Export, then set the default VM/VHDX paths.

## Recovery-side workload network

~~~powershell
New-VMSwitch -Name "vSW-Lab" -SwitchType Internal
New-NetIPAddress -InterfaceAlias "vEthernet (vSW-Lab)" -IPAddress 172.22.0.1 -PrefixLength 24
New-NetNat -Name "LabNAT" -InternalIPInterfaceAddressPrefix "172.22.0.0/24"
~~~

HV01 and HV02 each have separate isolated 172.22.0.0/24 networks.

Verify HV01 ↔ HV02 management connectivity using 192.168.240.11 and 192.168.240.12.


## What you will do

Initialize HV02's data disk, install Hyper-V, create the course storage paths, create recovery-side vSW-Lab/LabNAT, and verify outer host-to-host connectivity.


## What you should observe

HV02 should mirror the relevant storage and switch conventions from HV01 while remaining a separate Hyper-V host on 192.168.240.12.


## Validation checkpoint

Verify D:, Hyper-V role/VMMS, default paths, vSW-Lab, LabNAT, and bidirectional 192.168.240.11/.12 reachability.


## Expected end state

HV02 is a clean recovery-side Hyper-V host ready for Replica authentication.
