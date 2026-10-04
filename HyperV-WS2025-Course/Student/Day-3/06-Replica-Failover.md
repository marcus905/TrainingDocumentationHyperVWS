# Module 06 — Replica Failover Testing

## Goal

Understand Test Failover, Planned Failover and Unplanned Failover.

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
