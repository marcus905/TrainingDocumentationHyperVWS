# Module 07 — Management and Checkpoint Break/Fix

## Missing storage dependency

Create a disposable VM and real disk:

~~~powershell
New-VM -Name "BROKEN01" -Generation 2 -MemoryStartupBytes 1GB -Path "D:\Hyper-V\VMs" -NoVHD
New-VHD -Path "D:\Hyper-V\VHDX\BROKEN01-DISK.vhdx" -SizeBytes 2GB -Dynamic
Add-VMHardDiskDrive -VMName "BROKEN01" -Path "D:\Hyper-V\VHDX\BROKEN01-DISK.vhdx"
Move-Item "D:\Hyper-V\VHDX\BROKEN01-DISK.vhdx" "D:\Hyper-V\VHDX\BROKEN01-DISK.moved"
Start-VM BROKEN01
~~~

Investigate VMMS, Hyper-V events, VM configuration, storage paths and free space. Do not restart VMMS first.

Reset by moving the disk back, removing BROKEN01, then deleting the temporary VHDX.

## Checkpoint growth

Create a checkpoint, generate controlled guest writes, observe AVHDX/free-space behavior, identify whether the issue is checkpoint age/growth, capacity, workload writes or underlying storage, then remove the checkpoint and generated files.
