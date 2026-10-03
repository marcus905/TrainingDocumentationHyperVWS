# Day 4 — Structured Troubleshooting

## Learning objectives
Students should be able to:
- use a repeatable troubleshooting method;
- define scope and timeline;
- collect evidence before changing configuration;
- use Event Viewer, Resource Monitor and Performance Monitor;
- differentiate CPU, memory, disk and network bottlenecks;
- correlate guest and host behavior;
- identify common Hyper-V configuration faults;
- document root cause and corrective action.

## Troubleshooting workflow
~~~text
1. Identify symptom
2. Define scope
3. Establish timeline
4. Collect evidence
5. Build hypotheses
6. Test one hypothesis at a time
7. Apply corrective action
8. Validate
9. Document
~~~

Do not restart first unless restoration is explicitly the priority or evidence justifies it. Restarting can remove useful evidence.

## Module 1 — Event Viewer
Review:
- System log
- Application log
- Hyper-V logs under Applications and Services Logs
- severity
- timestamps
- event correlation
- repeated vs isolated events

PowerShell:
~~~powershell
Get-WinEvent -LogName System -MaxEvents 50
Get-WinEvent -FilterHashtable @{ LogName='System'; Level=2 }
~~~

## Module 2 — Performance Monitor
Teach:
- counters and instances;
- sampling;
- baselines;
- transient vs sustained peaks;
- correlation between counters.

Areas:
- CPU
- memory
- disk
- network
- Hyper-V hypervisor counters
- Hyper-V VM counters

## Scenario 4.1 — CPU bottleneck
Create controlled CPU load inside SRV01.

Tasks:
1. Confirm the symptom.
2. Identify the process.
3. Inspect host utilization.
4. Compare guest and host CPU.
5. Classify the issue.

## Scenario 4.2 — Memory pressure
Configure insufficient VM memory and investigate:
- available memory;
- paging;
- Dynamic Memory;
- minimum/startup/maximum values;
- host memory pressure.

~~~powershell
Get-VMMemory SRV01
Get-VM SRV01
~~~

## Scenario 4.3 — Storage latency
Generate controlled guest I/O.

Investigate:
- guest disk latency;
- host disk latency;
- free space;
- VHDX placement;
- checkpoint files;
- competing workloads.

Central question: is the bottleneck in the guest, virtual disk layer, or host storage?

## Scenario 4.4 — Virtual network failure
Fault examples:
- wrong switch;
- disconnected vNIC;
- wrong VLAN;
- wrong guest IP;
- wrong DNS;
- wrong gateway.

~~~powershell
Get-VMSwitch
Get-VMNetworkAdapter -VMName SRV01
~~~

Guest:
~~~powershell
Get-NetIPConfiguration
Get-NetRoute
Test-NetConnection
Resolve-DnsName
~~~

## Scenario 4.5 — Hyper-V management problem
~~~powershell
Get-Service vmms
~~~

Use Hyper-V Event Viewer logs and determine scope: one VM, several VMs, management only, or entire host.

## Scenario 4.6 — Checkpoint growth
Create a checkpoint and write activity. Observe:
- AVHDX growth;
- host free space;
- checkpoint chain;
- disk impact.

## Security software discussion
Discuss antivirus, EDR, backup agents, file-system filters, snapshot software and network inspection.

Do not blindly disable security controls. Instead:
1. verify vendor support;
2. collect evidence;
3. validate documented exclusions/settings;
4. test safely;
5. document security impact.

## Root-cause report template
~~~text
Problem statement:
Scope:
First observed:
Impact:
Evidence collected:
Hypotheses:
Root cause:
Corrective action:
Validation:
Preventive recommendation:
~~~

## End-of-day challenge
Students independently investigate a host with multiple symptoms and deliver:
- problem statement;
- evidence;
- root cause;
- remediation;
- verification;
- prevention recommendation.
