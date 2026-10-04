# Instructor Guide

## Purpose
Instructor-only guidance for the five-day Windows Server 2025 and Hyper-V course. It includes delivery guidance, student-executed break/fix ideas, expected outcomes and troubleshooting prompts.

## Delivery model
Remote training with nested virtualization.

~~~text
Physical Windows 11 Pro/Enterprise Hyper-V host
|
|-- vSW-Course / CourseNAT
|   192.168.240.0/24
|
+-- HV01 - 192.168.240.11
|   +-- vSW-Lab / LabNAT - 172.22.0.0/24
|       +-- DC01 - 172.22.0.10
|       +-- SRV01 - 172.22.0.20
|
+-- HV02 - 192.168.240.12
    +-- recovery-side vSW-Lab
    +-- replica workloads
~~~

## Suggested teaching rhythm
1. Explain the concept.
2. Draw the architecture.
3. Demonstrate configuration.
4. Students reproduce it.
5. Validate.
6. Students apply one controlled break recipe locally.
7. Students stop looking at the break recipe and investigate from symptoms/evidence.
8. Review evidence/root cause.
9. Compare GUI and PowerShell approaches.
10. Reset to the known baseline.
11. Summarize best practices.

## Day 0 checks
Confirm:
- physical host is Windows 11 Pro/Enterprise;
- Hyper-V enabled on physical host;
- at least 24 GB RAM available, 32 GB+ preferred;
- at least 250 GB free SSD/NVMe storage available;
- vSW-Course and CourseNAT exist;
- HV01/HV02 boot with 192.168.240.11/.12;
- dedicated 200 GB data disks are attached;
- outer DVD drives removed so D: is available;
- nested virtualization exposed;
- Windows Server 2025 ISO available;
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

### Student break/fix — DNS
Have students configure invalid DNS themselves, then troubleshoot from the symptom statement. Expected evidence:
- IP may work;
- hostname resolution fails;
- Resolve-DnsName isolates the problem.

## Day 2 notes
### External switch warning
Remote students can disconnect themselves by changing the wrong NIC. Prefer instructor demonstration for External switches and student use of Internal/NAT networking.

Break/fix choices:
- wrong vSwitch;
- disconnected vNIC;
- wrong subnet;
- wrong DNS;
- duplicate IP.

Use only one fault in the first exercise. Students should apply the fault locally and then diagnose it without referring back to the break step.

## Day 3 notes
### Checkpoints
Required message: **Checkpoints are state-management tools, not backups.**

Show AVHDX creation and merge behavior.

### Replica
Use Test Failover before disruptive recovery actions. Isolate test networking to avoid address conflicts.

Before the Replica lab, verify that students have:
- initialized HV02's D: data disk;
- created the recovery-side vSW-Lab and LabNAT;
- created/imported the lab root and host Replica certificates;
- enabled the documented lab-only certificate revocation setting;
- verified hv01.lab.local and hv02.lab.local name mappings.

Break/fix choices:
- blocked HTTPS Replica firewall rule;
- DNS/name-resolution failure;
- Replica not enabled;
- wrong authentication/certificate selection;
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
6. storage dependency
7. Replica failure
8. two-fault incident drill

For the EOD exercise, assign each student two scenario codes from the student guide. Prefer different layers, for example A+D, B+C or E+F.

## Day 5 notes
Use the local capstone model from the student guide.

Assign each student three capstone codes from different layers where possible. Do not remotely modify student environments.

Good combinations include:
- A + D + E;
- B + C + F;
- A + C + E;
- B + D + F.

During the capstone, coach with questions rather than revealing the assigned fault:
- What layer have you proven healthy?
- What evidence supports that?
- Which issue is highest risk?
- What proves the correction worked?

Finish with the collective capstone debrief and compare troubleshooting reasoning rather than speed.

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
