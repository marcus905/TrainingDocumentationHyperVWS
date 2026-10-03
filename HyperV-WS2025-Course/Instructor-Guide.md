# Instructor Guide

## Purpose
Instructor-only guidance for the five-day Windows Server 2025 and Hyper-V course. It includes fault-injection ideas, expected outcomes and troubleshooting prompts.

## Delivery model
Remote training with nested virtualization.

~~~text
Physical Hyper-V host
|
+-- HV01
|   +-- DC01
|   +-- SRV01
|   +-- CLIENT01
|
+-- HV02
    +-- replica workloads
~~~

## Suggested teaching rhythm
1. Explain the concept.
2. Draw the architecture.
3. Demonstrate configuration.
4. Students reproduce it.
5. Validate.
6. Inject one controlled fault.
7. Students investigate.
8. Review evidence/root cause.
9. Show PowerShell equivalent.
10. Summarize best practices.

## Day 0 checks
Confirm:
- Hyper-V enabled on physical host;
- HV01/HV02 boot;
- nested virtualization exposed;
- Windows Server 2025 ISO available;
- enough storage and RAM;
- administrator access.

Common failures:
- virtualization disabled in UEFI;
- unsupported Windows edition;
- insufficient RAM;
- nested extensions not exposed;
- endpoint security interference;
- VPN/network software interfering with virtual networking.

## Day 1 notes
Teach networking as separate layers:
~~~text
Link
IP addressing
Routing
DNS
Application/service
~~~

### Fault injection — DNS
Configure invalid DNS. Expected evidence:
- IP may work;
- hostname resolution fails;
- Resolve-DnsName isolates the problem.

## Day 2 notes
### External switch warning
Remote students can disconnect themselves by changing the wrong NIC. Prefer instructor demonstration for External switches and student use of Internal/NAT networking.

Fault choices:
- wrong vSwitch;
- disconnected vNIC;
- wrong subnet;
- wrong DNS;
- duplicate IP.

Inject only one fault in the first exercise.

## Day 3 notes
### Checkpoints
Required message: **Checkpoints are state-management tools, not backups.**

Show AVHDX creation and merge behavior.

### Replica
Use Test Failover before disruptive recovery actions. Isolate test networking to avoid address conflicts.

Fault choices:
- blocked firewall;
- DNS failure;
- Replica not enabled;
- wrong authentication;
- insufficient target storage.

## Day 4 notes
When students propose a fix, ask:
1. What evidence supports the hypothesis?
2. What would disprove it?
3. What is the safest test?
4. How will you verify success?

Suggested progression:
1. DNS
2. wrong switch
3. CPU load
4. memory pressure
5. checkpoint growth
6. storage contention
7. Replica failure
8. combined issue

## Day 5 notes
Do not disclose how many faults exist.

Possible solution set:
- APP01: virtual network configuration;
- DB01: I/O contention or disk placement;
- FILE01: unmanaged checkpoint chain;
- WEB01: Replica connectivity/authentication;
- HV02: excessive memory allocation.

Change these between deliveries.

## VMware discussion
Use mappings only as a bridge. Explain architectural differences rather than presenting concepts as perfect equivalents.

## Assessment dimensions
### Technical execution
- correct configuration;
- verification;
- safe changes.

### Troubleshooting
- evidence;
- scope;
- hypotheses;
- root cause;
- validation.

### Operational maturity
- documentation;
- repeatability;
- PowerShell use;
- separation of remediation and prevention.

## Reference policy
Use Microsoft Learn as primary source.

- https://learn.microsoft.com/windows-server/get-started/overview
- https://learn.microsoft.com/windows-server/get-started/hardware-requirements
- https://learn.microsoft.com/windows-server/get-started/getting-started-with-server-core
- https://learn.microsoft.com/windows-server/virtualization/hyper-v/host-hardware-requirements
- https://learn.microsoft.com/windows-server/virtualization/hyper-v/enable-nested-virtualization
- https://learn.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-hyper-v
- https://learn.microsoft.com/windows-server/virtualization/hyper-v/configure-replication-single-host

Review current documentation before delivery.
