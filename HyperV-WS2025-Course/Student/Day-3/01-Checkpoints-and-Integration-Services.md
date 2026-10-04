# Module 01 — Checkpoints and Integration Services

## Goal

Use checkpoints safely and understand host/guest coordination.

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
