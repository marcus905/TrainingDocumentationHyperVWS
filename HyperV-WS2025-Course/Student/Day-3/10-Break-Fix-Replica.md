# Module 10 — Break/Fix: Replica

## Introduction

This controlled incident tests whether you can troubleshoot a recovery technology across protocol, name resolution, certificates, firewall, service, and storage layers. The break is simple; the diagnostic path should still be evidence-driven.

## Concepts to keep in mind

Replica health depends on more than VM state. A protocol path can fail even when HV01 and HV02 can ping each other, and a healthy TCP port still does not prove certificate identity or authorization.

## What you will do

Disable the assigned HTTPS Replica firewall rule on HV02, stop looking at the break step, inspect replication health and the network/authentication path, state the root cause, then restore the rule.

## Break

On HV02 locate the HTTPS Hyper-V Replica firewall rule:

~~~powershell
Get-NetFirewallRule | Where-Object DisplayName -like "*Replica*"
~~~

Disable the HTTPS listener rule selected for the lab.

## Symptom

SRV01 replication becomes unhealthy and Replica connectivity fails.

## Investigate

~~~powershell
Get-VMReplication SRV01
Measure-VMReplication SRV01
Test-Connection hv02.lab.local -Count 2
Test-NetConnection hv02.lab.local -Port 443
~~~

Also inspect:

- certificates;
- Replica configuration;
- VMMS;
- firewall state;
- D:\Hyper-V\Replica on HV02.

State the root cause before fixing.

## Reset

Re-enable the same firewall rule and verify that replication health returns to normal.

## What you should observe

Replication health should degrade and TCP 443 connectivity from HV01 to hv02.lab.local should fail while basic host reachability may remain intact.

## Validation checkpoint

Show the evidence that isolates the failure to the Replica HTTPS path rather than DNS, certificate identity, VMMS, or target storage.

## Expected end state

The firewall rule is restored and SRV01 replication returns to a normal healthy state.
