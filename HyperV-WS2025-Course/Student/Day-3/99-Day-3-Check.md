# Day 3 — Validation Check

## Introduction

Day 3 adds the most dependencies of the course so far: a second Hyper-V host, certificate trust, replication, recovery testing, and performance baselines. This checklist makes sure those layers are clean before Day 4 deliberately starts breaking them.

- [ ] Production checkpoint lifecycle completed.
- [ ] Integration Services inspected.
- [ ] Checkpoint/backup/Replica/HA differences understood.
- [ ] VM export/recovery concepts practiced.
- [ ] HV02 data disk initialized.
- [ ] Hyper-V installed on HV02.
- [ ] Recovery-side vSW-Lab/LabNAT created.
- [ ] Lab root and host Replica certificates created/imported.
- [ ] HTTPS Replica enabled.
- [ ] SRV01 initial replication completed.
- [ ] Test Failover practiced.
- [ ] HA/storage architectures discussed.
- [ ] Performance baseline recorded.
- [ ] Replica break/fix completed.
- [ ] Environment returned to baseline.

## Concepts to keep in mind

A reliable troubleshooting day needs a reliable starting state. Replica health, no leftover checkpoints, known switch mappings, and a completed recovery test are all part of that baseline.

## What you will do

Review each validation item and repair any incomplete Replica, storage, checkpoint, or recovery-test state before ending the day.

## What you should observe

A healthy environment should have normal SRV01 operation on HV01, healthy Replica to HV02, no temporary failover artifacts, and documented baseline performance observations.

## Validation checkpoint

Confirm there are no unintended checkpoints/test VMs and that `Get-VMReplication` reports the expected healthy relationship.

## Expected end state

The lab is ready for structured troubleshooting with a known-good recovery and performance baseline.
