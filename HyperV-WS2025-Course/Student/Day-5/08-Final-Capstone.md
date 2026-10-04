# Module 08 — Final Capstone

## Introduction

The final capstone simulates taking ownership of an environment with several simultaneous problems. You will create the assigned faults locally, then set the break instructions aside and operate as if another administrator handed you only the symptoms.

## Goal

Diagnose three locally created faults with limited instructor guidance.

## Concepts to keep in mind

Multiple symptoms may have independent root causes. Prioritize by operational risk, separate problem statements, avoid broad changes, and prove every correction with post-change validation.

## What you will do

Apply the three assigned fault codes, inventory the environment, write separate problem statements, prioritize, gather evidence, prove root causes, correct each issue, restore the baseline, and produce the final report.

## Hands-on / detailed content

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

## What you should observe

Fixing one fault may leave the others unchanged. That is useful evidence about scope and dependency rather than a sign that troubleshooting failed.

## Validation checkpoint

For each incident, be prepared to defend the evidence, rejected hypotheses, root cause, smallest safe correction, validation, and preventive recommendation.

## Expected end state

All assigned faults are removed, the environment is back at the expected baseline, and the final operational report demonstrates independent troubleshooting and operational ownership.
