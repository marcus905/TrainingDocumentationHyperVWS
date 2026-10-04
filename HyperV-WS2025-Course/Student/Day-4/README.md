# Day 4 — Structured Troubleshooting

## Introduction

Day 4 changes the role of the lab: instead of building new features, you will deliberately create faults and practice diagnosing them. The emphasis is on method, evidence, and safe correction rather than speed or guessing.

## Concepts to keep in mind

Every incident should move from symptom to scope, evidence, hypotheses, proof, correction, and validation. The fact that you created the fault yourself does not remove the need to demonstrate that reasoning.

## What you will do

Use the modules as incident playbooks: apply the controlled break, stop looking at the break instructions, then work from the symptom and evidence.

## Sequence

1. [Troubleshooting method](01-Troubleshooting-Method.md)
2. [Events and evidence](02-Events-and-Evidence.md)
3. [Performance tools](03-Performance-Tools.md)
4. [CPU and memory break/fix](04-CPU-and-Memory.md)
5. [Storage break/fix](05-Storage.md)
6. [Network break/fix](06-Network.md)
7. [Management and checkpoint break/fix](07-Management-and-Checkpoints.md)
8. [Security and recurring patterns](08-Security-and-Patterns.md)
9. [End-of-day incident drill](09-EOD-Incident-Drill.md)
10. [Day 4 check](99-Day-4-Check.md)

The goal is not to guess what changed. The goal is to prove the failing layer with evidence.

## What you should observe

Different faults can produce similar user complaints, so the useful skill is identifying which layer is actually failing.

## Validation checkpoint

By the end of the day, you should be able to produce a concise root-cause report for a two-fault incident without instructor manipulation of your lab.

## Expected end state

The environment is restored to baseline and you have a repeatable troubleshooting process ready for the Day 5 capstone.
