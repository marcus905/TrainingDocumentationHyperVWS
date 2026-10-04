# Day 0 — Readiness Check


## Concepts to keep in mind

Readiness means more than 'the VMs boot.' Networking, storage, installation media, host resources, and nested virtualization all need to be known-good dependencies.

## Introduction

This final check is the handoff from lab preparation into the actual course. Treat every failed item as a blocker rather than something to fix later, because all subsequent labs assume this baseline exists.

- [ ] Windows 11 Pro/Enterprise host.
- [ ] Hyper-V enabled.
- [ ] 24 GB+ RAM.
- [ ] 250 GB+ free SSD/NVMe storage.
- [ ] Windows Server 2025 ISO verified.
- [ ] vSW-Course exists.
- [ ] CourseNAT exists.
- [ ] HV01 boots at 192.168.240.11/24.
- [ ] HV02 boots at 192.168.240.12/24.
- [ ] Both outer hosts can reach 192.168.240.1.
- [ ] 200 GB raw data disk attached to each.
- [ ] Outer DVD drives removed.
- [ ] Nested virtualization exposed.
- [ ] Hyper-V is not installed inside HV01/HV02.

## What you will do

Walk through every checkbox and resolve any failed item before Day 1.


## What you should observe

A clean Day 0 lab should be reproducible: the same names, addresses, disk layout, and virtualization settings should exist on every student's machine.


## Validation checkpoint

Explain the difference between the physical host, the outer management network, HV01/HV02, and the nested layer that will be created later.


## Expected end state

The lab is deterministic, documented, and ready for Day 1 without hidden preparation work.
