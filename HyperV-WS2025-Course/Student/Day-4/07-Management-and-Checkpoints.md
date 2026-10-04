# Module 07 — Management and Checkpoint Break/Fix

## Introduction

Management failures are useful because they force you to separate the Hyper-V control plane from guest behavior. This module also revisits checkpoint growth from the operational side: a VM can be running while its storage dependencies or checkpoint chain are becoming unhealthy.

## Concepts to keep in mind

A VM start operation depends on valid configuration, reachable storage, VMMS, and sufficient host resources. A checkpoint can be technically valid while still creating operational risk through age, growth, or free-space pressure.

## What you will do

Create the disposable missing-storage dependency fault, investigate without restarting VMMS as a first response, cleanly reset it, then review the separate checkpoint-growth scenario.

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

## What you should observe

BROKEN01 should exist in Hyper-V but fail to start because the configured disk path no longer resolves. In the checkpoint scenario, the VM can remain online while AVHDX/free-space behavior changes.

## Validation checkpoint

For each case, identify the management/storage evidence that proves the root cause and distinguish service failure from dependency failure.

## Expected end state

The disposable VM and files are removed, no training checkpoint remains, and VMMS is left in its normal running state.
