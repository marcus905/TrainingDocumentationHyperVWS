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

### PowerShell progression

Day 1 now contains two related primers:

1. **General PowerShell essentials** on HV01 — objects, pipeline, discovery, help, filtering and safe administration.
2. **Hyper-V PowerShell orientation** on the physical Windows 11 host — read-only inspection of HV01/HV02 using the Hyper-V module.

The Hyper-V orientation must remain read-only unless specifically directed. The reason for running it on the physical host is that Hyper-V is not installed inside HV01 until Day 2.

On Day 2, explicitly reconnect the primer to the nested host by showing that the same `Get-VM`, `Get-VMProcessor`, `Get-VMMemory`, `Get-VMNetworkAdapter`, `Get-VMHardDiskDrive` and `Get-VMFirmware` cmdlets now operate inside HV01.

### ICMP Echo Request

Show both supported lab approaches:

- SConfig -> 4 Configure remote management -> 3 Enable server response to ping;
- PowerShell with a custom inbound ICMPv4 Echo Request rule scoped to 192.168.240.0/24.

Use this to reinforce that ping is only basic path evidence. It does not validate DNS, HTTPS, WinRM, Replica, or another application protocol.

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

### Optional Windows 11 CLIENT01 / TPM

CLIENT01 remains optional because it increases nested-memory pressure.

If the optional Windows 11 client is used, configure the supported prerequisites before installation:

- Generation 2;
- Secure Boot enabled;
- vTPM enabled;
- 2+ vCPUs;
- 4 GB+ RAM;
- 64 GB+ boot disk.

Use a local key protector plus `Enable-VMTPM` on the standalone training host.

A useful teaching point is that Hyper-V emulates the guest vTPM independently of the host TPM presence/version. If Windows 11 Setup reports a TPM failure, inspect the VM security configuration before blaming the physical TPM.

Do not teach Windows 11 setup-check bypasses. The objective is correct virtual-hardware/security configuration.

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



## Student module/file alignment

Use these filenames when directing students during class or mapping future slide sections.

### Day 0

- `Day-0/01-Host-Prerequisites.md`
- `Day-0/02-Course-Folders-and-Media.md`
- `Day-0/03-Outer-NAT-Network.md`
- `Day-0/04-Create-HV01-HV02.md`
- `Day-0/05-Install-and-Configure-Hosts.md`
- `Day-0/06-Nested-Virtualization.md`
- `Day-0/99-Readiness-Check.md`

### Day 1

- `Day-1/01-PowerShell-Essentials.md`
- `Day-1/02-Hyper-V-PowerShell-Primer.md`
- `Day-1/03-Architecture-and-WS2025.md`
- `Day-1/04-Initial-Configuration.md`
- `Day-1/05-Roles-Features-and-Tools.md`
- `Day-1/06-Networking.md`
- `Day-1/07-Local-Storage.md`
- `Day-1/08-Stage-Installation-Media.md`
- `Day-1/09-Break-Fix-DNS.md`
- `Day-1/99-Day-1-Check.md`

### Day 2

- `Day-2/01-Architecture.md`
- `Day-2/02-Install-Hyper-V.md`
- `Day-2/03-Management-Tools.md`
- `Day-2/04-VM-Resources-and-Storage.md`
- `Day-2/05-Virtual-Networking.md`
- `Day-2/06-Create-SRV01.md`
- `Day-2/07-Guest-Install-and-Network.md`
- `Day-2/08-Create-DC01.md`
- `Day-2/09-Break-Fix-Network.md`
- `Day-2/10-Optional-Windows-11-CLIENT01.md`
- `Day-2/99-Day-2-Check.md`

### Day 3

- `Day-3/01-Checkpoints-and-Integration-Services.md`
- `Day-3/02-Recovery-Concepts.md`
- `Day-3/03-Prepare-HV02.md`
- `Day-3/04-Replica-Authentication.md`
- `Day-3/05-Enable-Replica.md`
- `Day-3/06-Replica-Failover.md`
- `Day-3/07-HA-and-Storage.md`
- `Day-3/08-Performance-and-Tuning.md`
- `Day-3/09-Bare-Metal-and-PXE.md`
- `Day-3/10-Break-Fix-Replica.md`
- `Day-3/99-Day-3-Check.md`

### Day 4

- `Day-4/01-Troubleshooting-Method.md`
- `Day-4/02-Events-and-Evidence.md`
- `Day-4/03-Performance-Tools.md`
- `Day-4/04-CPU-and-Memory.md`
- `Day-4/05-Storage.md`
- `Day-4/06-Network.md`
- `Day-4/07-Management-and-Checkpoints.md`
- `Day-4/08-Security-and-Patterns.md`
- `Day-4/09-EOD-Incident-Drill.md`
- `Day-4/99-Day-4-Check.md`

### Day 5

- `Day-5/01-Advanced-Troubleshooting.md`
- `Day-5/02-VMware-to-HyperV.md`
- `Day-5/03-Migration-Planning.md`
- `Day-5/04-Migration-Technologies.md`
- `Day-5/05-Operations-and-Inventory.md`
- `Day-5/06-Security-Baseline.md`
- `Day-5/07-Operational-Readiness.md`
- `Day-5/08-Final-Capstone.md`
- `Day-5/99-Course-Wrap-Up.md`

The Day README files are the student navigation pages. The root Day 0–5 files remain the canonical technical guides.

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
