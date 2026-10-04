# Module 09 — End-of-Day Incident Drill

## Goal

Diagnose two locally created faults using evidence, not memory.

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
