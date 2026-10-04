# Day 1 — Validation Check

## Introduction

This checklist is the handoff from Windows Server fundamentals into Hyper-V. Each item represents a dependency used directly on Day 2, so unresolved gaps should be fixed now rather than carried forward.

- [ ] PowerShell fundamentals demonstrated.
- [ ] Windows Server architecture understood.
- [ ] Roles/features explored.
- [ ] Administration tools compared.
- [ ] Outer network validated.
- [ ] 200 GB training disk initialized as D:.
- [ ] D:\Hyper-V structure exists.
- [ ] Windows Server ISO staged inside HV01.
- [ ] DNS break/fix completed.
- [ ] HV01 returned to known-good baseline.

## Concepts to keep in mind

A known-good baseline is part of troubleshooting. If tomorrow's Hyper-V lab fails, today's validated network, storage, media, and operating-system state give you something reliable to compare against.


## What you will do

Review every checkbox and re-run the relevant validation command for anything uncertain.


## What you should observe

A clean Day 1 environment has stable outer networking, an initialized D: volume, the complete Hyper-V folder tree, and the Windows Server ISO already inside HV01.


## Validation checkpoint

Do not proceed until all validation items are complete and the DNS break/fix has been reset.


## Expected end state

HV01 is a known-good Windows Server platform with the storage, networking, media, and PowerShell foundations required for Day 2.
