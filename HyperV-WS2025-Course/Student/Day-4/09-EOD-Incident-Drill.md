# Module 09 — End-of-Day Incident Drill

## Introduction

The end-of-day drill combines two faults so you must prioritize and separate independent symptoms. You will create the faults yourself, but once they are applied, work only from the incident symptoms and evidence as if you inherited the environment from another administrator.

## Goal

Diagnose two locally created faults using evidence, not memory.

## Concepts to keep in mind

Two simultaneous faults do not necessarily share one root cause. Treat each symptom as a separate problem statement until evidence shows a relationship.

## What you will do

Apply the two assigned scenario codes, stop reading the break recipes, inventory the environment, investigate, prove both root causes, correct them in risk order, and produce the required report.

## Hands-on / detailed content

The instructor assigns two scenario codes.

### A — DNS failure
Set SRV01 DNS to 172.22.0.254.

### B — Wrong virtual switch
Connect SRV01 to vSW-Private.

### C — Constrained memory
Set SRV01 Dynamic Memory maximum to 1 GB using the Day 4 memory recipe.

### D — Checkpoint growth
Create an EOD checkpoint and controlled guest writes.

### E — Replica HTTPS blocked
Disable the HV02 HTTPS Replica firewall rule.

### F — Missing VM storage dependency
Use the BROKEN01 move-the-attached-VHDX recipe.

## Student mission

1. Apply only the assigned break recipes.
2. Stop looking at them.
3. Write problem statements.
4. Collect evidence.
5. Prove both root causes.
6. Apply minimum safe corrections.
7. Validate the lab.
8. Present the result.

## Deliverable

For each incident record symptom, scope, impact, evidence, hypotheses, root cause, correction, validation and prevention.

Use the canonical Day 4 guide for exact reset commands.

## What you should observe

One correction may restore one symptom while the second remains. That is useful evidence that the incidents are independent rather than one cascading failure.

## Validation checkpoint

Your report should contain separate scope, evidence, root cause, correction, validation, and prevention for both assigned faults.

## Expected end state

Both faults are corrected, the full lab baseline is restored, and you can defend the troubleshooting path orally.
