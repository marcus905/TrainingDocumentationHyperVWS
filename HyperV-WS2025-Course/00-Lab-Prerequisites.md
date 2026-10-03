# Day 0 — Lab Prerequisites and Nested Virtualization Setup

## Objective
Before Day 1, each student should have two Windows Server 2025 virtual machines available on a physical Hyper-V host: **HV01** and **HV02**. Hyper-V inside these VMs is installed during the course.

## Recommended physical workstation
| Resource | Minimum | Recommended |
|---|---:|---:|
| CPU | 4 cores / 8 logical processors | 8+ cores |
| RAM | 16 GB | 32 GB+ |
| Free storage | 150 GB | 250-500 GB SSD/NVMe |
| Network | Stable connection | 1 GbE or stable Wi-Fi |
| Hardware virtualization | Required | Required |
| SLAT | Required | Required |

Reference: https://learn.microsoft.com/windows-server/virtualization/hyper-v/host-hardware-requirements

## Exercise 0.1 — Verify hardware support
~~~powershell
systeminfo.exe
Get-ComputerInfo
Get-CimInstance Win32_Processor | Select-Object Name,Manufacturer,NumberOfCores,NumberOfLogicalProcessors
~~~

Review the Hyper-V Requirements section from systeminfo.

## Exercise 0.2 — Install Hyper-V on Windows 11
~~~powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
~~~

Restart and verify:

~~~powershell
Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V
~~~

Expected state: Enabled.

## Exercise 0.3 — Prepare local course folders
~~~text
C:\HyperV-Course
|
+-- ISO
+-- VMs
+-- VHDX
+-- Export
+-- Scripts
+-- Logs
~~~

## Exercise 0.4 — Create HV01 and HV02
| Setting | HV01 | HV02 |
|---|---:|---:|
| Generation | 2 | 2 |
| vCPU | 4 | 4 |
| Startup RAM | 8 GB | 6-8 GB |
| VHDX | 100 GB | 100 GB |
| OS | Windows Server 2025 Desktop Experience | Windows Server 2025 Desktop Experience |

Reference: https://learn.microsoft.com/windows-server/get-started/getting-started-with-server-core

## Exercise 0.5 — Enable nested virtualization
Power off both VMs first.

~~~powershell
Set-VMProcessor -VMName "HV01" -ExposeVirtualizationExtensions $true
Set-VMProcessor -VMName "HV02" -ExposeVirtualizationExtensions $true
Get-VMProcessor HV01,HV02 | Select-Object VMName,ExposeVirtualizationExtensions
~~~

Expected result: ExposeVirtualizationExtensions = True.

Reference: https://learn.microsoft.com/windows-server/virtualization/hyper-v/enable-nested-virtualization

## Networking approach
Use a controlled Internal/NAT design for the remote lab rather than depending on the student's corporate or home LAN.

~~~text
Internet / Physical LAN
        |
Physical Hyper-V Host
        |
   NAT / Internal network
        |
   +----+----+
   |         |
  HV01      HV02
   |
Nested Hyper-V networking
   |
+--+-------+
|          |
DC01      SRV01
~~~

## Readiness checklist
- [ ] Hyper-V installed on physical workstation.
- [ ] Windows Server 2025 ISO available.
- [ ] HV01 and HV02 boot successfully.
- [ ] Nested virtualization enabled on both.
- [ ] Adequate free disk space remains.
- [ ] Local administrator rights available.
- [ ] Elevated PowerShell can be opened.

## Instructor note
Do not ask students to install Hyper-V inside HV01/HV02 before the course. That is part of Day 2.
