# Module 01 — Host Prerequisites

## Goal

Confirm that the physical workstation can run the complete nested Hyper-V lab.

## Required platform

- Windows 11 Pro or Enterprise, 64-bit;
- Hyper-V available;
- hardware virtualization and SLAT;
- local administrator rights.

## Resource baseline

| Resource | Minimum | Recommended |
|---|---:|---:|
| CPU | 4 cores / 8 logical processors | 8+ cores |
| RAM | 24 GB | 32 GB+ |
| Free SSD/NVMe storage | 250 GB | 350–500 GB |

## Verify

~~~powershell
systeminfo.exe
Get-ComputerInfo
Get-CimInstance Win32_Processor | Select-Object Name,NumberOfCores,NumberOfLogicalProcessors
~~~

Install Hyper-V if needed:

~~~powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
~~~

## Validation

- [ ] Windows 11 Pro/Enterprise confirmed.
- [ ] Hyper-V enabled.
- [ ] At least 24 GB RAM.
- [ ] At least 250 GB free SSD/NVMe storage.
- [ ] Elevated PowerShell available.