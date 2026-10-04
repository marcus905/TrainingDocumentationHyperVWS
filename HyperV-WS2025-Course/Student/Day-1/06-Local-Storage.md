# Module 06 — Local Storage

## Introduction

Hyper-V will create many files during the week, so HV01 needs a dedicated data volume rather than placing everything on the operating-system disk. This module also introduces the discipline of positively identifying a disk before any destructive storage command.

## Goal

Initialize the dedicated 200 GB Day 0 training disk and prepare Hyper-V folders.

## Concepts to keep in mind

Disk, partition, volume, file system, and folder are different storage layers. Initialization and formatting should only follow positive identification of the intended training disk.

## What you will do

Identify the approximately 200 GB raw disk, initialize it as GPT, create and format D:, then build the course Hyper-V folder structure.

## Hands-on / detailed content

~~~powershell
Get-Disk
~~~

Identify the approximately 200 GB raw disk. Do not assume the disk number.

~~~powershell
Initialize-Disk -Number <DiskNumber> -PartitionStyle GPT
New-Partition -DiskNumber <DiskNumber> -UseMaximumSize -DriveLetter D
Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel "Hyper-V Data" -Confirm:$false
New-Item -ItemType Directory -Path "D:\Hyper-V\VMs" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\VHDX" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\ISO" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\Replica" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\Export" -Force
~~~

## Validation

~~~powershell
Get-Volume -DriveLetter D
Get-ChildItem D:\Hyper-V
~~~

## What you should observe

The raw training disk becomes an NTFS volume while the OS disk remains untouched. The predictable folder structure will be reused throughout the week.

## Validation checkpoint

Confirm the correct disk was selected, D: is healthy NTFS, and VMs/VHDX/ISO/Replica/Export folders exist.

## Expected end state

HV01 has a dedicated, known-good Hyper-V data volume ready for Day 2.
