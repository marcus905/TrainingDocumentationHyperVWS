# Module 06 — Local Storage

## Goal

Initialize the dedicated 200 GB Day 0 training disk and prepare Hyper-V folders.

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