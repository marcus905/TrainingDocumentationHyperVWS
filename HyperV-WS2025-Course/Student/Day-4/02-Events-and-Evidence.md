# Module 02 — Events and Evidence


## Concepts to keep in mind

Severity does not equal root cause. Provider, event time, repetition, affected object, and surrounding changes often matter more than the word Error.

## Introduction

Event logs are most useful when they are treated as evidence in a timeline rather than as a list of red icons. This module teaches you to query the logs that matter and correlate them with the time and layer of the reported symptom.

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


## What you will do

Query recent System events, filter errors and a time window, discover Hyper-V logs, and inspect the log family that best matches the symptom.


## What you should observe

Hyper-V separates management, worker, switch, and storage-related events into different logs, which helps narrow the failing layer.


## Validation checkpoint

Choose one event and explain what it proves, what it suggests, and what it does not prove.


## Expected end state

You can collect targeted event evidence without treating every error as the answer.
