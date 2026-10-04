# Module 06 — Replica Failover Testing

## Introduction

Replication is only useful if recovery can be exercised safely. This module moves from 'Replica is healthy' to 'can I actually start and validate a recovery copy without disrupting the primary workload?'

## Goal

Understand Test Failover, Planned Failover and Unplanned Failover.

## Concepts to keep in mind

Test Failover is a validation mechanism, Planned Failover is a coordinated transition while the primary is available, and Unplanned Failover is a recovery action when the primary is unavailable.

## What you will do

Create an isolated recovery-test switch, perform Test Failover for SRV01, validate the temporary recovery VM without connecting it to the normal workload network, then stop the test failover cleanly.

## Test Failover

On HV02 create an isolated Private switch:

~~~powershell
New-VMSwitch -Name "vSW-RecoveryTest" -SwitchType Private
~~~

Use Test Failover for SRV01. Connect the temporary test VM only to the isolated recovery-test network, boot it, validate it, then stop the test failover.

## Concepts

- Test Failover: validation without interrupting normal replication.
- Planned Failover: primary available; coordinated transition.
- Unplanned Failover: primary unavailable; choose an available recovery point and accept possible data loss.

## Validation

Explain why Test Failover should be isolated and why Replica is DR rather than HA.

## What you should observe

The temporary recovery VM should start from replicated data while normal replication remains conceptually separate from the test workload. Isolation prevents duplicate IP/name conflicts.

## Validation checkpoint

Explain the difference between Test, Planned, and Unplanned Failover and identify which one changes production ownership.

## Expected end state

Replica remains healthy and no temporary test-failover VM or unintended network conflict remains.
