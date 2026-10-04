# Module 02 — Events and Evidence

## Goal

Use Windows and Hyper-V logs as evidence.

Review:

- System;
- Application;
- Hyper-V-VMMS;
- Hyper-V-Worker;
- Hyper-V-VmSwitch;
- Hyper-V-StorageVSP.

Discover logs:

~~~powershell
Get-WinEvent -ListLog *Hyper-V* |
    Select-Object LogName,RecordCount,IsEnabled
~~~

Recent System events:

~~~powershell
Get-WinEvent -LogName System -MaxEvents 50
~~~

Recent errors:

~~~powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'System'
    Level   = 2
} -MaxEvents 25
~~~

Time-filtered evidence:

~~~powershell
$Start = (Get-Date).AddMinutes(-30)
Get-WinEvent -FilterHashtable @{LogName='System';StartTime=$Start}
~~~

An Error event is not automatically the root cause. Correlate timestamps, providers, repeated patterns and recent changes.
