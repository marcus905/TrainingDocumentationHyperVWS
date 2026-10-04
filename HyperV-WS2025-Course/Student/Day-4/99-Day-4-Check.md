# Day 4 — Validation Check

## Introduction

This checklist closes the troubleshooting day by verifying both technical recovery and troubleshooting discipline. The goal is not merely to have working VMs again, but to know why the environment is working.

- [ ] Structured troubleshooting workflow reproduced.
- [ ] Symptoms separated from assumptions.
- [ ] Scope/timeline/impact defined.
- [ ] System and Hyper-V events queried.
- [ ] Performance tools used.
- [ ] Guest and host CPU compared.
- [ ] Memory pressure investigated.
- [ ] Storage/checkpoint behavior investigated.
- [ ] Network fault isolated layer by layer.
- [ ] Management/storage dependency fault investigated.
- [ ] Security/EDR approach discussed safely.
- [ ] Two-fault incident drill completed.
- [ ] Root-cause report delivered.
- [ ] Environment returned to baseline.

## Concepts to keep in mind

A resolved incident requires validation evidence and a restored baseline. A guessed fix that happens to work is not equivalent to a proven root cause.

## What you will do

Review the day's incidents, confirm all temporary faults/checkpoints/files are removed, and make sure your root-cause report can be explained without referring to the break recipe.

## What you should observe

The final environment should look like the known-good Day 3 baseline: healthy Replica, normal SRV01 memory/networking, and no disposable fault artifacts.

## Validation checkpoint

Confirm the two-fault drill is fully reset and identify one troubleshooting habit you would carry into a real customer incident.

## Expected end state

The environment and your troubleshooting process are both ready for the Day 5 capstone.
