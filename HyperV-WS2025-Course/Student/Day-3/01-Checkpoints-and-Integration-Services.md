# Module 01 — Checkpoints and Integration Services

## Introduction

Checkpoints are useful because they capture VM state around a change, but they are also easy to misuse. This module connects checkpoint behavior to the Integration Services that help Hyper-V coordinate with the guest.

## Goal

Use checkpoints safely and understand host/guest coordination.

## Concepts to keep in mind

Production checkpoints aim for application-consistent recovery using guest coordination, while standard checkpoints capture VM state differently. AVHDX files are temporary differencing layers that must eventually merge.

## What you will do

Inspect Integration Services, configure Production checkpoints, create a checkpoint, make a controlled guest change, restore it, and then remove the checkpoint.

## Hands-on / detailed content

Production checkpoints are preferred for supported production-like workloads. Checkpoints are temporary rollback tools, not backups.

Inspect Integration Services:

~~~powershell
Get-VMIntegrationService -VMName SRV01
~~~

Important services include Heartbeat, Time Synchronization, Shutdown, Guest Service Interface and backup/VSS integration.

## Checkpoint lab

~~~powershell
Set-VM -Name SRV01 -CheckpointType Production
Checkpoint-VM -VMName SRV01 -SnapshotName "Day3-Test"
Get-VMSnapshot SRV01
~~~

Create a controlled guest change, restore the checkpoint, verify rollback, then remove it:

~~~powershell
Restore-VMSnapshot -VMName SRV01 -Name "Day3-Test" -Confirm:$false
Remove-VMSnapshot -VMName SRV01 -Name "Day3-Test"
~~~

Observe AVHDX creation and merge behavior.

## What you should observe

Creating a checkpoint adds differencing-disk activity; removing it starts a merge. Guest-visible rollback should match the checkpoint semantics discussed in class.

## Validation checkpoint

Confirm the checkpoint disappears after removal and the merge completes. Explain why a checkpoint is not a backup.

## Expected end state

SRV01 is back on its normal disk chain with no leftover training checkpoint.
