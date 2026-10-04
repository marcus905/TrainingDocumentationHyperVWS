# Module 01 — Host Prerequisites

## Introduction

Before building any virtual machines, confirm that the physical workstation can support the complete five-day nested lab. This avoids discovering halfway through the course that the host edition, memory, storage, or virtualization capabilities cannot support the later exercises.

## Goal

Confirm that the physical workstation can run the complete nested Hyper-V lab.


## Concepts to keep in mind

Nested virtualization consumes resources at two levels: HV01/HV02 are VMs on the physical workstation, and later they will host additional VMs. The physical host therefore needs enough headroom for both layers.

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

## What you should observe

`systeminfo.exe` should show the Hyper-V requirements as satisfied, and Hyper-V should report as enabled after installation.


## Validation checkpoint

Confirm the host edition, available RAM, free storage, hardware virtualization, SLAT, and local administrative access.


## Expected end state

The physical workstation is confirmed suitable for the course and can host HV01 and HV02 without changing the lab design later.
