# Module 08 — Final Capstone

## Goal

Diagnose three locally created faults with limited instructor guidance.

The instructor assigns three codes, preferably from different layers.

### A — Wrong SRV01 switch
Connect SRV01 to vSW-Private.

### B — Bad DNS
Set SRV01 DNS to 172.22.0.254.

### C — Constrained memory
Use the 1 GB maximum-memory recipe from Day 4.

### D — Checkpoint growth
Create a Capstone checkpoint and controlled guest writes.

### E — Replica HTTPS blocked
Disable the HV02 HTTPS Replica firewall rule.

### F — Missing VM storage dependency
Create CAPBROKEN01 with a valid attached VHDX, move the VHDX, then attempt VM start.

## Mission

1. Apply only assigned break recipes.
2. Stop looking at the recipes.
3. Inventory the environment.
4. Write separate problem statements.
5. Establish scope/impact.
6. Prioritize.
7. Collect evidence.
8. Prove root causes.
9. Apply minimum safe corrections.
10. Validate every corrected layer.
11. Return the lab to baseline.
12. Produce the final report.

## Final report

For each incident provide symptom, scope, impact, evidence, hypotheses, root cause, correction, validation and prevention.

Also include migration considerations, security observations and operational-standardization recommendations.

Use the canonical Day 5 guide for the exact break/reset commands.
