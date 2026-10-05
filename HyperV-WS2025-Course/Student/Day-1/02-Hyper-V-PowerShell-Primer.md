# Module 02 — Hyper-V PowerShell Primer

## Introduction

You have already used general PowerShell to inspect Windows Server. Before Hyper-V is installed inside HV01, this short orientation introduces the **Hyper-V PowerShell vocabulary** on the physical Windows 11 host, where Hyper-V is already installed and HV01/HV02 already exist.

The objective is discovery and read-only inspection. You are not changing the physical host configuration in this module.

## Goal

Recognize the most important Hyper-V PowerShell command families and use them to inspect the existing outer lab safely.

## Concepts to keep in mind

The same PowerShell habits from Module 01 apply to Hyper-V:

- discover commands instead of guessing;
- read help before changing configuration;
- retrieve objects with `Get-*`;
- select only useful properties;
- distinguish inspection from configuration;
- treat errors as evidence.

Common Hyper-V nouns include:

~~~text
VM
VMHost
VMProcessor
VMMemory
VMNetworkAdapter
VMSwitch
VMHardDiskDrive
VHD
VMFirmware
VMSnapshot
VMReplication
~~~

The verbs tell you the intent:

~~~text
Get-*      inspect
New-*      create
Set-*      change
Add-*      attach/add
Connect-*  connect
Start-*    start
Stop-*     stop
Remove-*   remove
~~~

A command's verb is a useful risk signal, but always read its help before execution.

## What you will do

Run this module on the **physical Windows 11 Hyper-V host**, not inside HV01.

### Discover the Hyper-V module

~~~powershell
Get-Module -ListAvailable Hyper-V
Get-Command -Module Hyper-V
~~~

Count the available Hyper-V commands:

~~~powershell
(Get-Command -Module Hyper-V).Count
~~~

Search by noun:

~~~powershell
Get-Command -Module Hyper-V -Noun VM
Get-Command -Module Hyper-V -Noun VMSwitch
Get-Command -Module Hyper-V -Name "*VHD*"
~~~

### Read command help

~~~powershell
Get-Help Get-VM
Get-Help Get-VM -Examples
Get-Help Get-VMNetworkAdapter -Examples
~~~

### Inspect the outer lab

~~~powershell
Get-VM
~~~

Select useful properties:

~~~powershell
Get-VM |
    Select-Object Name,State,Generation,ProcessorCount,MemoryAssigned
~~~

Inspect HV01 in more detail:

~~~powershell
Get-VM -Name HV01
Get-VMProcessor -VMName HV01
Get-VMMemory -VMName HV01
Get-VMNetworkAdapter -VMName HV01
Get-VMHardDiskDrive -VMName HV01
Get-VMFirmware -VMName HV01
~~~

Inspect the outer virtual networking:

~~~powershell
Get-VMSwitch
Get-VMNetworkAdapter -VMName HV01,HV02 |
    Select-Object VMName,Name,SwitchName,MacAddress,Status
~~~

Inspect host defaults:

~~~powershell
Get-VMHost |
    Select-Object VirtualMachinePath,VirtualHardDiskPath,LogicalProcessorCount
~~~

### Reuse the pipeline model

~~~powershell
Get-VM |
    Where-Object State -eq "Running" |
    Select-Object Name,State,ProcessorCount,MemoryAssigned
~~~

This is the same object-and-pipeline model used with services, networking, and Windows features.

## What you should observe

Hyper-V PowerShell is not a separate scripting language. It uses the same objects, pipeline, help, filtering, and selection model you learned in Module 01.

The main new skill is learning the Hyper-V object names and recognizing which cmdlets inspect the host, VM, virtual network, storage, or firmware layer.

## Safety rule for this primer

Use **read-only inspection commands** on the physical host unless the instructor explicitly directs otherwise.

Do not run `Remove-*`, `Set-*`, `New-*`, `Connect-*`, `Disconnect-*`, `Stop-*`, or other configuration-changing Hyper-V commands merely to experiment.

Day 2 will provide controlled opportunities to use those verbs inside HV01.

## Validation checkpoint

You should be able to answer:

1. Which module provides Hyper-V cmdlets?
2. How do you discover commands related to VHDX files?
3. Which cmdlet lists VMs?
4. Which cmdlet shows a VM's vNIC and switch mapping?
5. Which cmdlet inspects VM memory configuration?
6. What is the difference between `Get-VM` and `Get-VMHost`?
7. Why are `Get-*` commands appropriate for this Day 1 primer?
8. How will these same commands become useful inside HV01 on Day 2?

## Expected end state

You can navigate the Hyper-V PowerShell module safely, inspect HV01/HV02 from the physical host, and recognize the command families that will be used throughout Days 2–5.
