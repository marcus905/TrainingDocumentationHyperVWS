# Module 02 — Recovery Concepts

## Introduction

Several Hyper-V features can look similar because they involve copies of VM data, but their operational purpose is different. This module gives you a decision framework before the Replica lab begins.

## Goal

Separate technologies that solve different recovery problems.

## Concepts to keep in mind

Checkpoint = short rollback, backup = independent recoverable copy/history, Replica = asynchronous DR copy, clustering = high availability. Export is a portability/recovery mechanism, not a complete backup strategy.

## What you will do

Export SRV01, inspect the resulting files, discuss import modes, and compare what would happen if the primary VM, host, or application data were lost.

## Hands-on / detailed content

| Technology | Primary purpose |
|---|---|
| Checkpoint | Short rollback |
| Backup | Independent historical recovery |
| Hyper-V Replica | Asynchronous disaster recovery |
| Failover Clustering | High availability |

Replica is not backup because unwanted changes or corruption can also replicate.

## Export exercise

~~~powershell
New-Item -ItemType Directory -Path "D:\Hyper-V\Export" -Force
Export-VM -Name SRV01 -Path "D:\Hyper-V\Export"
Get-ChildItem "D:\Hyper-V\Export" -Recurse
~~~

Discuss import choices: register in place, restore, or copy/new ID. A successful export does not prove recoverability until recovery is tested.

## What you should observe

An export contains VM configuration and disks, but it does not by itself prove that recovery objectives, retention, or application consistency are met.

## Validation checkpoint

Given a failure scenario, choose checkpoint, backup, Replica, or HA and justify why the other options are insufficient.

## Expected end state

You can distinguish the recovery technologies before configuring Hyper-V Replica.
