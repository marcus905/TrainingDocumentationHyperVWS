# Day 3 — Advanced Hyper-V

## Introduction

Day 3 builds on the working Hyper-V platform by adding state management, recovery, replication, storage architecture, and performance awareness. The focus shifts from 'can I run a VM?' to 'can I protect, recover, measure, and reason about it?'

## Concepts to keep in mind

Keep four ideas separate throughout the day: checkpoints, backup, Replica, and high availability. They can all involve copies or recovery, but they solve different operational problems.

## What you will do

Work through checkpoints first, then prepare HV02, configure Replica, test recovery, and finish with storage/performance concepts and a controlled Replica incident.

## Sequence

1. [Checkpoints and Integration Services](01-Checkpoints-and-Integration-Services.md)
2. [Backup, export, Replica and HA](02-Recovery-Concepts.md)
3. [Prepare HV02](03-Prepare-HV02.md)
4. [Replica authentication](04-Replica-Authentication.md)
5. [Enable Hyper-V Replica](05-Enable-Replica.md)
6. [Replica failover testing](06-Replica-Failover.md)
7. [HA and advanced storage](07-HA-and-Storage.md)
8. [Performance and tuning](08-Performance-and-Tuning.md)
9. [Bare metal and PXE](09-Bare-Metal-and-PXE.md)
10. [Break/Fix — Replica](10-Break-Fix-Replica.md)
11. [Day 3 check](99-Day-3-Check.md)

## What you should observe

By the end of the day, SRV01 should have a healthy recovery relationship to HV02 and you should be able to explain what each recovery technology can and cannot guarantee.

## Validation checkpoint

Use the Day 3 checklist to confirm both hosts, certificate trust, Replica health, and test-failover behavior.

## Expected end state

The lab now includes a recovery host, validated Replica workflow, and a performance baseline for Day 4 troubleshooting.
