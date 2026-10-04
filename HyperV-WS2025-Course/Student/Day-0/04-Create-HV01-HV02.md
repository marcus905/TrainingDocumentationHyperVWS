# Module 04 — Create HV01 and HV02

## Goal

Create the two outer Windows Server VMs used throughout the course.

## Specification

| Setting | HV01 | HV02 |
|---|---:|---:|
| Generation | 2 | 2 |
| vCPU | 4 | 4 |
| RAM | 8 GB static | 8 GB static |
| OS disk | 100 GB dynamic | 100 GB dynamic |
| Data disk | 200 GB dynamic | 200 GB dynamic |
| Network | vSW-Course | vSW-Course |

Use Hyper-V Manager or PowerShell. Attach the Windows Server 2025 ISO and configure the virtual DVD as first boot device.

After creation verify:

~~~powershell
Get-VM HV01,HV02 | Select-Object Name,State,Generation,MemoryStartup
Get-VMProcessor HV01,HV02 | Select-Object VMName,Count
Get-VMHardDiskDrive HV01,HV02
Get-VMDvdDrive HV01,HV02
~~~