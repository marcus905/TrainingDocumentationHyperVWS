# Module 08 — Security and Recurring Patterns


## Concepts to keep in mind

Security software can legitimately inspect files, processes, and network traffic, but correlation is required before calling it the cause. Legacy configuration can be supported and healthy; age alone is not evidence of failure.

## Introduction

Some recurring Hyper-V incidents involve software outside the obvious VM configuration: antivirus, EDR, backup agents, filter drivers, old storage layouts, or stale configuration. This module teaches a safe way to reason about those possibilities without using 'disable security' as a troubleshooting shortcut.

## Security software

Antivirus, EDR, backup agents, file-system filters and network inspection can affect Hyper-V behavior.

Do not blindly disable security controls.

Use:

1. evidence;
2. current Microsoft/vendor guidance;
3. the smallest safe change;
4. retest;
5. document security impact.

## Recurring patterns

### VM will not start

Check VM state, VMMS, VHDX availability, free storage, memory, switch references and Hyper-V events.

### VM is slow

Check guest process/workload, vCPU, memory pressure, disk latency, checkpoint chain and host contention.

### VM has no network

Check vNIC, switch, IP, gateway, NAT/routing, DNS, firewall and application port.

### Replica unhealthy

Check replication state, name resolution, HTTPS port, certificates, target capacity and authorization.

Legacy configuration is not automatically wrong. First prove whether it contributes to the symptom.


## What you will do

Review the recurring symptom patterns and, for each one, identify the minimum evidence you would collect before changing security software, storage layout, VM configuration, or Replica settings.


## What you should observe

The same symptom category—such as 'VM won't start' or 'VM is slow'—can have several root causes across different layers.


## Validation checkpoint

Explain how you would test a security-tool hypothesis safely and how you would separate root cause from contributing factor.


## Expected end state

You have a reusable checklist for common host/VM patterns without relying on unsafe blanket exclusions or random remediation.
