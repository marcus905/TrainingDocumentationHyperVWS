# Day 2 — Validation Check

## Introduction

Day 2 creates the platform that all advanced work depends on. This checklist makes sure the host, virtual network, VM resources, guest installation, and recovery baseline are complete before Replica and checkpoint work begin.

- [ ] Hyper-V installed on HV01.
- [ ] Default VM/VHDX paths set.
- [ ] vSW-Lab Internal switch exists.
- [ ] LabNAT exists.
- [ ] vSW-Private exists.
- [ ] SRV01 created and installed.
- [ ] SRV01 uses 172.22.0.20/24.
- [ ] Extra SRV01 data disk attached.
- [ ] DC01 created.
- [ ] Network break/fix completed.
- [ ] Environment returned to baseline.

## Concepts to keep in mind

A later Day 3 failure is much easier to diagnose if today's switch names, NAT, VM paths, guest addresses, and resource settings are already verified.


## What you will do

Review each item and re-run the relevant host or guest command wherever the state is uncertain.


## What you should observe

A healthy Day 2 lab has SRV01 and DC01 on vSW-Lab, predictable VHDX paths, working NAT, and no leftover network fault.


## Validation checkpoint

Do not proceed if SRV01 cannot reach its gateway, the Internet test path, or if VM storage/switch mappings differ from the course baseline.


## Expected end state

HV01 is a stable nested Hyper-V platform ready for checkpoints, Replica, recovery, storage architecture, and performance work.
