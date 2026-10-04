# Module 05 — Storage Break/Fix

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
