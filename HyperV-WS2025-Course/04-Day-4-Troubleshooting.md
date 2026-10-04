# Day 4 — Structured Troubleshooting

## Learning objectives

By the end of Day 4, students should be able to:

- use a repeatable troubleshooting method;
- define symptom, scope, timeline and impact;
- collect evidence before changing configuration;
- distinguish observation from assumption;
- use Event Viewer, Get-WinEvent, Resource Monitor and Performance Monitor;
- use Hyper-V-specific performance counters;
- correlate guest and host behavior;
- differentiate CPU, memory, disk and network bottlenecks;
- recognize common Hyper-V management and configuration failures;
- evaluate security software and filter-driver impact without disabling controls blindly;
- recognize legacy storage and configuration risks;
- identify recurring host/VM fault patterns;
- document root cause, corrective action and prevention.

---

# Module 1 — A structured troubleshooting method

Troubleshooting should be a process, not a sequence of guesses.

Use the following workflow throughout Day 4:

~~~text
1. Identify the symptom
        |
2. Define scope
        |
3. Establish timeline and impact
        |
4. Collect evidence
        |
5. Build hypotheses
        |
6. Test one hypothesis at a time
        |
7. Apply corrective action
        |
8. Validate recovery
        |
9. Document root cause and prevention
~~~

## Symptom

Describe what is actually happening.

Good:

~~~text
SRV01 cannot resolve public DNS names.
~~~

Weak:

~~~text
The network is broken.
~~~

The first statement is observable and testable. The second is an assumption.

## Scope

Determine whether the issue affects:

- one application;
- one VM;
- multiple VMs;
- one virtual switch;
- one Hyper-V host;
- both Hyper-V hosts;
- the entire lab network;
- only management access.

Scope helps determine which layer to investigate first.

## Timeline

Ask:

- When did the problem begin?
- Was it working earlier?
- What changed?
- Was there a reboot?
- Was a checkpoint restored?
- Was software installed?
- Was networking/storage configuration changed?
- Did the problem begin gradually or suddenly?

## Impact

Technical severity and business impact are not the same.

Example:

~~~text
Technical symptom:
One replica link is unhealthy.

Potential business impact:
Recovery capability for a critical workload is degraded.
~~~

## Evidence before action

Examples of useful evidence include:

- Event Viewer logs;
- PowerShell command output;
- performance counters;
- VM configuration;
- network routes;
- DNS results;
- storage capacity;
- replication state;
- timestamps;
- screenshots when appropriate.

Avoid changing multiple settings at once because that makes root-cause confirmation difficult.

## Restore service vs preserve evidence

Sometimes restoring service is the highest priority.

However, actions such as:

- restarting the host;
- rebooting the VM;
- resetting a service;
- deleting a checkpoint;
- changing DNS;
- recreating a switch

can remove useful evidence.

When possible, capture the current state first.

---

# Module 2 — Build a problem statement

A strong problem statement should contain:

~~~text
What:
Where:
When:
Scope:
Impact:
Expected behavior:
Observed behavior:
Recent changes:
~~~

Example:

~~~text
What: SRV01 cannot access resources by hostname.
Where: SRV01 only.
When: Began after a network configuration exercise.
Scope: IP connectivity works; DNS resolution fails.
Impact: Applications using DNS names cannot connect.
Expected: microsoft.com resolves successfully.
Observed: Resolve-DnsName times out.
Recent changes: DNS client configuration modified.
~~~

This format prevents troubleshooting from starting with an unsupported root-cause guess.

---

# Module 3 — Event Viewer and Windows event logs

Windows event logs are one of the most important evidence sources in Windows Server troubleshooting.

Microsoft troubleshooting guidance recommends reviewing both the System log and Hyper-V logs under:

~~~text
Applications and Services Logs
  -> Microsoft
     -> Windows
        -> Hyper-V
~~~

Microsoft reference:

https://learn.microsoft.com/troubleshoot/windows-server/virtualization/virtual-machine-settings-troubleshooting-guidance

## Core logs

Start with:

- System;
- Application;
- Hyper-V-VMMS;
- Hyper-V-Worker;
- Hyper-V-VmSwitch;
- Hyper-V-StorageVSP;
- other Hyper-V logs relevant to the symptom.

Not every problem produces a useful Error event.

Review:

- timestamp;
- provider/source;
- Event ID;
- level;
- message;
- repeated pattern;
- events immediately before and after the failure.

## Severity is not enough

An Error event is not automatically the root cause.

Likewise, an Information event can reveal the configuration change that triggered a later failure.

Always correlate events with:

- symptom time;
- related services;
- VM state;
- configuration changes.

---

# Lab 4.1 — Query events with PowerShell

## Step 1 — Recent System events

~~~powershell
Get-WinEvent -LogName System -MaxEvents 50
~~~

## Step 2 — Recent errors

~~~powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'System'
    Level   = 2
} -MaxEvents 25
~~~

## Step 3 — Events from a time window

~~~powershell
$Start = (Get-Date).AddMinutes(-30)

Get-WinEvent -FilterHashtable @{
    LogName   = 'System'
    StartTime = $Start
}
~~~

## Step 4 — Discover Hyper-V logs

~~~powershell
Get-WinEvent -ListLog *Hyper-V* |
    Select-Object LogName,RecordCount,IsEnabled
~~~

## Step 5 — Read one Hyper-V log

Choose a relevant enabled log from the previous output:

~~~powershell
Get-WinEvent -LogName "Microsoft-Windows-Hyper-V-VMMS-Admin" -MaxEvents 30
~~~

If that exact log name is not present, use the names returned by Get-WinEvent -ListLog.

## Validation

Students should be able to explain:

- why time filtering matters;
- why Event ID alone is insufficient;
- why repeated events may be more meaningful than one isolated event;
- why multiple logs may need correlation.

---

# Module 4 — Performance troubleshooting methodology

Performance troubleshooting uses the same evidence-first process.

The goal is not to ask:

> Is utilization high?

The goal is to ask:

> Which resource is constrained, for how long, at which layer, and what workload is responsible?

Microsoft reference:

https://learn.microsoft.com/troubleshoot/windows-server/support-tools/troubleshoot-issues-performance-monitor/

Hyper-V bottleneck reference:

https://learn.microsoft.com/windows-server/administration/performance-tuning/role/hyper-v-server/detecting-virtualized-environment-bottlenecks

## Baseline first

Day 3 captured baseline observations.

Compare abnormal behavior against that baseline whenever possible.

Without a baseline, the administrator may not know whether a value is unusual for the environment.

## Transient vs sustained load

A short spike is not automatically a bottleneck.

Look for:

- duration;
- repetition;
- workload correlation;
- user-visible impact;
- queueing or waiting behavior.

## Host and guest correlation

~~~text
Guest symptom
    |
Guest counters
    |
VM configuration
    |
Hyper-V counters
    |
Host counters
    |
Physical/outer infrastructure
~~~

In nested virtualization, an additional outer virtualization layer exists below HV01/HV02.

This means observed host performance is still ultimately dependent on the student's physical machine.

---

# Module 5 — Performance Monitor, Resource Monitor and counters

## Performance Monitor

Performance Monitor provides:

- real-time counter viewing;
- Data Collector Sets;
- scheduled collection;
- historical analysis;
- multiple counter correlation.

Open:

~~~text
perfmon
~~~

Microsoft reference:

https://learn.microsoft.com/troubleshoot/windows-server/support-tools/troubleshoot-issues-performance-monitor/

## Resource Monitor

Resource Monitor is useful for rapidly identifying:

- CPU-consuming processes;
- per-process memory;
- disk I/O;
- file activity;
- TCP connections;
- network utilization.

Open:

~~~text
resmon
~~~

## Get-Counter

PowerShell can sample Performance Monitor counters.

Discover Hyper-V counter sets:

~~~powershell
Get-Counter -ListSet *Hyper-V*
~~~

Useful families include:

- Hyper-V Hypervisor Logical Processor;
- Hyper-V Hypervisor Virtual Processor;
- Hyper-V Dynamic Memory VM;
- Hyper-V Virtual Storage Device;
- Hyper-V Virtual Network Adapter.

Exact counter availability can vary with host configuration.

---

# Scenario 4.1 — CPU bottleneck

## Fault model

The instructor creates a controlled CPU-heavy workload inside SRV01.

## Student objective

Determine whether:

- one guest process is CPU-bound;
- SRV01 lacks sufficient vCPU;
- the Hyper-V host is CPU constrained;
- multiple VMs are competing;
- the symptom is only a short workload spike.

## Step 1 — Confirm guest symptom

Inside SRV01:

~~~powershell
Get-Process |
    Sort-Object CPU -Descending |
    Select-Object -First 10 Name,Id,CPU
~~~

Observe Task Manager.

## Step 2 — Sample guest CPU

~~~powershell
Get-Counter '\Processor(_Total)\% Processor Time' -SampleInterval 2 -MaxSamples 10
~~~

## Step 3 — Check VM configuration

On HV01:

~~~powershell
Get-VMProcessor SRV01
Get-VM SRV01 | Select-Object Name,State,CPUUsage
~~~

## Step 4 — Inspect host CPU

~~~powershell
Get-Counter '\Hyper-V Hypervisor Logical Processor(_Total)\% Total Run Time' -SampleInterval 2 -MaxSamples 10
~~~

If the counter is unavailable, discover the local Hyper-V counter set before continuing.

## Step 5 — Compare layers

Students should determine whether the evidence supports:

~~~text
Guest workload problem
VM sizing problem
Host contention
No sustained bottleneck
~~~

## Correction principle

Do not immediately add vCPUs.

First prove that CPU is the constrained resource and identify whether the constraint is inside the guest or at the host.

---

# Scenario 4.2 — Memory pressure

## Fault model

The instructor reduces SRV01's usable memory range or starts additional workloads to create memory pressure.

## Evidence sources

On HV01:

~~~powershell
Get-VMMemory SRV01
Get-VM SRV01 | Select-Object Name,MemoryAssigned,MemoryDemand,MemoryStatus
~~~

Inside SRV01:

~~~powershell
Get-Counter '\Memory\Available MBytes','\Memory\Pages/sec' -SampleInterval 2 -MaxSamples 10
~~~

## Questions

- Is available guest memory consistently low?
- Is paging increasing?
- Is Dynamic Memory enabled?
- What are Startup, Minimum and Maximum RAM?
- Is the host itself under memory pressure?
- Are several VMs competing for host RAM?

## Host view

~~~powershell
Get-Counter '\Memory\Available MBytes' -SampleInterval 2 -MaxSamples 10
~~~

## Correction examples

Depending on evidence:

- increase VM memory limits;
- reduce unnecessary workloads;
- correct an unrealistic Minimum/Maximum configuration;
- move workloads to a host with capacity;
- investigate an application memory leak.

The correction should match the proven bottleneck.

---

# Scenario 4.3 — Storage latency

## Fault model

The instructor creates controlled guest I/O or leaves a checkpoint chain in place while disk activity occurs.

## Layer model

~~~text
Guest filesystem
      |
Guest virtual disk
      |
VHDX / AVHDX chain
      |
Hyper-V storage stack
      |
HV01 volume
      |
Outer virtual disk
      |
Physical student storage
~~~

## Step 1 — Check free capacity

On HV01:

~~~powershell
Get-Volume
~~~

Low free space can create both operational and performance problems.

## Step 2 — Inspect VM disks

~~~powershell
Get-VMHardDiskDrive SRV01
Get-ChildItem "D:\Hyper-V\VHDX" | Select-Object Name,Length,LastWriteTime
~~~

Look for unexpected AVHDX files or disk growth.

## Step 3 — Check checkpoints

~~~powershell
Get-VMSnapshot SRV01
~~~

## Step 4 — Sample disk latency

~~~powershell
Get-Counter '\PhysicalDisk(_Total)\Avg. Disk sec/Read','\PhysicalDisk(_Total)\Avg. Disk sec/Write' -SampleInterval 2 -MaxSamples 10
~~~

## Step 5 — Compare guest and host behavior

Inside SRV01 use Resource Monitor or Performance Monitor to observe disk activity.

Central question:

> Is the delay inside the application/guest, the virtual disk chain, the HV01 storage layer, or the outer physical host?

## Correction examples

Evidence may justify:

- removing an unnecessary checkpoint and allowing merge;
- freeing host storage;
- moving VHDX files;
- reducing competing I/O;
- repairing an incorrect storage layout.

---

# Scenario 4.4 — Virtual network failure

## Possible instructor faults

- wrong virtual switch;
- disconnected vNIC;
- incorrect guest subnet;
- wrong default gateway;
- wrong DNS;
- missing LabNAT;
- duplicate IP;
- incorrect VLAN configuration where VLANs are demonstrated.

## Hyper-V layer

~~~powershell
Get-VMSwitch
Get-VMNetworkAdapter -VMName SRV01 |
    Select-Object VMName,SwitchName,Status,MacAddress
~~~

## HV01 network layer

~~~powershell
Get-NetIPAddress -InterfaceAlias "vEthernet (vSW-Lab)" -AddressFamily IPv4
Get-NetNat
~~~

## Guest layer

~~~powershell
Get-NetIPConfiguration
Get-NetRoute -AddressFamily IPv4
Get-DnsClientServerAddress -AddressFamily IPv4
~~~

## Test from nearest dependency outward

~~~powershell
Test-NetConnection 172.22.0.1
Test-NetConnection 1.1.1.1
Resolve-DnsName microsoft.com
Test-NetConnection microsoft.com -Port 443
~~~

## Troubleshooting principle

A useful sequence is:

~~~text
Adapter
  ->
IP
  ->
Local subnet
  ->
Gateway
  ->
Routing/NAT
  ->
DNS
  ->
Application port
~~~

Do not start with DNS if the VM cannot reach its gateway.

---

# Scenario 4.5 — Hyper-V management problem

## Symptom examples

- Hyper-V Manager cannot enumerate VMs;
- a VM cannot start;
- VM operations remain stuck;
- management works for some VMs but not others;
- PowerShell cmdlets fail.

## Step 1 — Establish scope

~~~powershell
Get-VM
Get-Service vmms
~~~

Ask:

- one VM or all VMs?
- GUI only or PowerShell too?
- management problem or guest problem?
- host-wide or workload-specific?

## Step 2 — Review Hyper-V events

~~~powershell
Get-WinEvent -ListLog *Hyper-V* |
    Select-Object LogName,RecordCount
~~~

Then inspect the logs that match the symptom and timeframe.

## Step 3 — Check storage and configuration dependencies

~~~powershell
Get-VMHost
Get-Volume
Get-VMSwitch
~~~

A VM start failure can originate from:

- missing VHDX;
- inaccessible path;
- insufficient storage;
- invalid switch reference;
- configuration issue;
- security/filter interaction.

## Rule

Do not restart VMMS as the first troubleshooting step unless service restoration has priority and evidence has already been captured.

---

# Scenario 4.6 — Checkpoint growth

## Fault model

A checkpoint is created and guest write activity is generated.

## Evidence

~~~powershell
Get-VMSnapshot SRV01
Get-VMHardDiskDrive SRV01
Get-ChildItem "D:\Hyper-V\VHDX" |
    Select-Object Name,Length,LastWriteTime
Get-Volume
~~~

Students should observe:

- AVHDX presence;
- AVHDX growth;
- reduction in host free space;
- relationship between write activity and checkpoint growth.

## Root-cause question

Is the storage problem:

~~~text
Checkpoint existence
Checkpoint age/growth
Insufficient host capacity
Unexpected workload writes
Underlying slow storage
~~~

A checkpoint by itself is not automatically a fault.

---

# Module 6 — Security software, EDR and filter drivers

Security products can interact with VM files, Hyper-V processes, storage paths and network traffic.

Possible components include:

- antivirus;
- EDR;
- backup agents;
- filesystem filter drivers;
- encryption/filter software;
- network inspection software.

## Windows Defender behavior

Microsoft documents automatic Windows Server exclusions for the Hyper-V role, including common Hyper-V files, folders and processes.

Microsoft reference:

https://learn.microsoft.com/defender-endpoint/microsoft-defender-antivirus-exclusions-windows-server

For third-party antivirus/EDR products, Microsoft also documents Hyper-V paths/processes that may require vendor-specific exclusions.

Reference:

https://learn.microsoft.com/troubleshoot/windows-server/virtualization/antivirus-exclusions-for-hyper-v-hosts

## Troubleshooting rule

Do **not** blindly disable security software.

Use this sequence:

1. collect evidence;
2. identify whether a security/filter component is involved;
3. verify current Microsoft and vendor guidance;
4. confirm exclusions/settings already in place;
5. make the smallest safe change;
6. retest;
7. document any security impact.

Disabling a security control can make the problem disappear while leaving the actual compatibility/configuration issue unresolved.

---

# Module 7 — Legacy storage and configuration problems

Real customer environments often contain systems that evolved over many years.

Examples include:

- old VHD files instead of VHDX;
- deeply nested differencing disks;
- long-lived checkpoints;
- VM files on the OS volume;
- inconsistent VM storage paths;
- undersized volumes;
- stale virtual switches;
- old VM generations;
- unnecessary virtual hardware;
- incorrect Dynamic Memory limits;
- abandoned replica files.

## Troubleshooting principle

Do not assume that an old configuration is wrong only because it is old.

First determine:

- whether it is supported;
- whether it contributes to the symptom;
- whether changing it introduces risk;
- whether remediation should happen during incident response or later maintenance.

---

# Module 8 — Recognizing recurring host/VM patterns

## VM will not start

Check:

- VM state;
- VMMS;
- VHDX availability;
- free host storage;
- memory availability;
- virtual switch references;
- Hyper-V events.

## VM is slow

Check:

- process/workload inside guest;
- vCPU configuration;
- memory pressure;
- storage latency;
- checkpoint chain;
- host contention.

## VM has no network

Check:

- vNIC;
- switch;
- guest IP;
- gateway;
- NAT/routing;
- DNS;
- firewall/application port.

## Hyper-V Manager cannot manage host

Check:

- scope;
- VMMS;
- permissions;
- remote-management path;
- firewall;
- name resolution;
- Hyper-V event logs.

## Replica unhealthy

Check:

- replication state;
- DNS;
- port 443 in this lab;
- certificate identity/trust;
- target capacity;
- Replica authorization.

---

# Lab 4.2 — Build a troubleshooting evidence package

## Objective

Collect a small, reusable evidence set before making changes.

On HV01:

~~~powershell
New-Item -ItemType Directory -Path "C:\Troubleshooting" -Force
~~~

Capture basic system and Hyper-V state:

~~~powershell
Get-Date | Out-File "C:\Troubleshooting\Time.txt"
Get-VM | Format-List * | Out-File "C:\Troubleshooting\VMs.txt"
Get-VMHost | Format-List * | Out-File "C:\Troubleshooting\VMHost.txt"
Get-VMSwitch | Format-List * | Out-File "C:\Troubleshooting\VMSwitches.txt"
Get-Volume | Format-Table -AutoSize | Out-File "C:\Troubleshooting\Volumes.txt"
Get-Service vmms | Format-List * | Out-File "C:\Troubleshooting\VMMS.txt"
~~~

Capture recent System errors:

~~~powershell
Get-WinEvent -FilterHashtable @{
    LogName   = 'System'
    Level     = 2
    StartTime = (Get-Date).AddHours(-1)
} | Format-List * | Out-File "C:\Troubleshooting\SystemErrors.txt"
~~~

### Teaching point

This is not intended to be a complete enterprise diagnostic collector.

The purpose is to build the habit:

~~~text
Capture state first
Change second
~~~

---

# Lab 4.3 — Multi-layer network fault

The instructor injects one fault affecting SRV01 networking.

Students must:

1. write the problem statement;
2. establish scope;
3. verify the virtual NIC and switch;
4. verify guest addressing;
5. test gateway;
6. test external IP;
7. test DNS;
8. test application port;
9. state root cause before fixing;
10. apply one correction;
11. validate.

No hints about the injected fault are provided initially.

---

# Lab 4.4 — Performance fault investigation

The instructor injects one controlled resource problem.

Possible scenarios:

- guest CPU workload;
- constrained VM memory;
- host memory pressure;
- storage activity;
- checkpoint growth.

Students must compare:

~~~text
Guest evidence
VM configuration
Hyper-V evidence
Host evidence
Baseline from Day 3
~~~

The goal is not to identify the highest number. The goal is to prove which layer is responsible for the user-visible symptom.

---

# Root-cause report template

Every troubleshooting scenario should end with a structured report.

~~~text
Problem statement:

Scope:

First observed:

Business/technical impact:

Expected behavior:

Observed behavior:

Recent changes:

Evidence collected:

Hypotheses considered:

Root cause:

Corrective action:

Validation:

Preventive recommendation:
~~~

## Root cause vs contributing factor

Separate the actual root cause from factors that made the problem worse.

Example:

~~~text
Root cause:
SRV01 connected to vSW-Private.

Contributing factor:
No documented network validation checklist after VM changes.
~~~

---

# End-of-day challenge — Independent incident

Students receive an environment containing multiple symptoms.

The instructor should inject two or three faults from different layers.

Example combination:

- wrong SRV01 DNS server;
- low free space on HV01;
- unnecessary checkpoint;
- disconnected VM network adapter;
- constrained VM memory;
- unhealthy Replica connection.

## Student rules

Students should not ask:

> What did you break?

Instead, they should:

1. define each symptom;
2. determine whether symptoms are related;
3. collect evidence;
4. prioritize based on impact;
5. build hypotheses;
6. test one hypothesis at a time;
7. apply corrective actions;
8. revalidate the environment;
9. deliver a short root-cause report.

## Deliverable

Students should present:

- problem statement;
- scope;
- evidence;
- root cause;
- remediation;
- verification;
- preventive recommendation.

---

# Day 4 review questions

1. Why should a troubleshooting statement describe a symptom rather than a suspected root cause?
2. Why is scope important?
3. Why can restarting a host too early damage the investigation?
4. Why should events be correlated by time rather than severity alone?
5. Which Hyper-V log family should be considered alongside the System log?
6. Why is a short CPU spike not automatically a bottleneck?
7. Why must guest and host performance be compared?
8. What evidence can indicate memory pressure?
9. Why can checkpoint growth become a storage problem?
10. What sequence helps isolate virtual-network failures?
11. Why should security software not simply be disabled during diagnosis?
12. What is the difference between a root cause and a contributing factor?
13. Why is a baseline useful?
14. Why should only one corrective variable normally be changed at a time?
15. What should always happen after a corrective action?

---

# End-of-day validation checklist

- [ ] Troubleshooting workflow can be reproduced without notes.
- [ ] Symptoms are separated from assumptions.
- [ ] Scope and timeline can be defined.
- [ ] Evidence is collected before disruptive changes.
- [ ] System and Hyper-V event logs can be queried.
- [ ] Event timestamps can be correlated.
- [ ] Performance Monitor and Resource Monitor understood.
- [ ] Hyper-V counter families can be discovered.
- [ ] Guest vs host CPU behavior compared.
- [ ] Memory-pressure evidence interpreted.
- [ ] Storage latency and checkpoint growth investigated.
- [ ] Virtual network faults isolated layer by layer.
- [ ] VMMS/management problems investigated without immediately restarting services.
- [ ] Security/EDR interaction discussed safely.
- [ ] Legacy/misconfiguration risks reviewed.
- [ ] Evidence package created.
- [ ] Root-cause report completed.
- [ ] End-of-day independent incident completed.

---

# Microsoft references

- Hyper-V VM troubleshooting guidance: https://learn.microsoft.com/troubleshoot/windows-server/virtualization/virtual-machine-settings-troubleshooting-guidance
- Performance Monitor troubleshooting: https://learn.microsoft.com/troubleshoot/windows-server/support-tools/troubleshoot-issues-performance-monitor/
- Hyper-V bottleneck detection: https://learn.microsoft.com/windows-server/administration/performance-tuning/role/hyper-v-server/detecting-virtualized-environment-bottlenecks
- Microsoft Defender exclusions on Windows Server: https://learn.microsoft.com/defender-endpoint/microsoft-defender-antivirus-exclusions-windows-server
- Hyper-V antivirus exclusion guidance: https://learn.microsoft.com/troubleshoot/windows-server/virtualization/antivirus-exclusions-for-hyper-v-hosts
- Hyper-V documentation: https://learn.microsoft.com/windows-server/virtualization/hyper-v/
