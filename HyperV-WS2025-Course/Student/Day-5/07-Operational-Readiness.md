# Module 07 — Operational Readiness

## Introduction

Before a workload is called production-ready, configuration, monitoring, backup, recovery, ownership, and security all need to be considered together. This module turns the week's technical work into an operational readiness decision.

## Goal

Decide whether a workload is ready to be operated and recovered.


## Concepts to keep in mind

Readiness is broader than 'the VM starts.' A workload can be technically functional but operationally weak if recovery is untested, dependencies are undocumented, monitoring is absent, or storage capacity is unhealthy.

## Host

- [ ] patched;
- [ ] healthy storage capacity;
- [ ] management network working;
- [ ] monitoring defined;
- [ ] backup/recovery design defined;
- [ ] security controls active.

## VM

- [ ] correct generation;
- [ ] justified CPU/memory;
- [ ] expected VHDX files;
- [ ] expected switch/VLAN;
- [ ] appropriate Secure Boot;
- [ ] guest maintained;
- [ ] backup/monitoring defined;
- [ ] dependencies documented.

## Recovery

- [ ] restore tested;
- [ ] Replica healthy if used;
- [ ] RPO/RTO documented;
- [ ] recovery ownership defined;
- [ ] Test Failover procedure documented.

Operational readiness requires both configuration and recovery confidence.


## What you will do

Use the host, VM, and recovery checklists against the current lab and mark which items are validated, assumed, or intentionally out of scope.


## What you should observe

Several readiness items require evidence from earlier days—Replica health, storage capacity, Secure Boot, baseline configuration—while others require documentation or organizational process.


## Validation checkpoint

For any unchecked item, state whether it is a blocker, an accepted lab limitation, or a production follow-up action.


## Expected end state

You can make and defend an operational-readiness decision rather than treating successful boot as sufficient evidence.
