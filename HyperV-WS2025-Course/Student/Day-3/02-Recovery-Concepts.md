# Module 02 — Recovery Concepts

## Goal

Separate technologies that solve different recovery problems.

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
