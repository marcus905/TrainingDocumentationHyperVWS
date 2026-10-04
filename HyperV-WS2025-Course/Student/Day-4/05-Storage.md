# Module 05 — Storage Break/Fix


## Concepts to keep in mind

A checkpoint redirects guest writes into an AVHDX differencing layer. That can increase storage consumption and I/O complexity without the base VHDX itself being 'broken.'

## Introduction

Storage symptoms often cross several layers: guest file activity, VHDX/AVHDX chains, host free space, and the physical storage underneath the nested lab. This incident gives you a controlled way to trace that chain.

## Break

On HV01:

~~~powershell
Checkpoint-VM -VMName "SRV01" -SnapshotName "Day4-Storage-Test"
~~~

Inside SRV01 generate controlled writes:

~~~powershell
New-Item -ItemType Directory -Path "C:\LabIO" -Force
1..2000 | ForEach-Object {
    "Day4 test data $_" | Out-File "C:\LabIO\file-$_.txt"
}
~~~

## Symptom

SRV01 storage activity increases and file operations feel slower.

## Investigate

On HV01:

~~~powershell
Get-Volume
Get-VMHardDiskDrive SRV01
Get-VMSnapshot SRV01
Get-ChildItem "D:\Hyper-V\VHDX" | Select-Object Name,Length,LastWriteTime
~~~

Sample disk latency and compare guest vs host behavior.

## Reset

~~~powershell
Remove-VMSnapshot -VMName "SRV01" -Name "Day4-Storage-Test"
~~~

Inside SRV01:

~~~powershell
Remove-Item "C:\LabIO" -Recurse -Force
~~~

Wait for merge completion before the next storage exercise.


## What you will do

Create the temporary checkpoint, generate controlled guest writes, inspect free capacity, VM disk paths, checkpoint state, and latency, then remove the checkpoint and generated data.


## What you should observe

The checkpoint should create AVHDX activity and the controlled writes should change file sizes/timestamps while SRV01 remains online.


## Validation checkpoint

Explain whether the evidence points to guest workload, checkpoint chain, host capacity, or underlying storage and what additional evidence would distinguish them.


## Expected end state

The temporary checkpoint is removed, merge completes, generated files are deleted, and storage returns to the known baseline.
