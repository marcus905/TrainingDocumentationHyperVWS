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

## Exercise 0.3 — Obtain the installation media

Use official Microsoft installation media only.

### Required media

| Media | Required? | Recommended source | Course use |
|---|---|---|---|
| Windows Server 2025 x64 ISO | Yes | Microsoft Evaluation Center or licensed organizational media | HV01, HV02 and nested server VMs |
| Windows 11 Enterprise x64 ISO | Optional | Microsoft Evaluation Center or licensed organizational media | Optional CLIENT01 |
| Windows Server 2025 Languages and Optional Features ISO | Optional | Microsoft Evaluation Center | Optional language/FOD exercises |

### Windows Server 2025 Evaluation ISO

Official Microsoft Evaluation Center:

https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2025

For this course, use the English (United States) x64 ISO whenever possible so menus, Event Viewer entries and screenshots match the training material.

The Evaluation Center currently provides Standard and Datacenter evaluation editions and both Server Core and Desktop Experience installation options. The evaluation is valid for 180 days. Microsoft states that it must be activated over the Internet within the first 10 days to avoid automatic shutdown behavior.

If your organization provides licensed Windows Server 2025 media, that media can be used instead. Do not redistribute Microsoft ISO files through this Git repository.

### Recommended edition

For HV01 and HV02 select:

**Windows Server 2025 Datacenter Evaluation (Desktop Experience)**

Datacenter is preferred for the training hosts so feature availability does not become an unnecessary variable during advanced Hyper-V exercises.

### Optional Windows 11 ISO

If CLIENT01 is used, Windows 11 Enterprise Evaluation is available from:

https://www.microsoft.com/en-us/evalcenter/evaluate-windows-11-enterprise

### Normalize local filenames

Copy downloaded media into:

~~~text
C:\HyperV-Course\ISO
~~~

Suggested filenames:

~~~text
C:\HyperV-Course\ISO\WS2025-EVAL-x64-EN.iso
C:\HyperV-Course\ISO\WIN11-ENT-EVAL-x64-EN.iso
~~~

The Microsoft download filename may differ. Renaming the local copy is only for lab consistency.

### Verify the ISO

~~~powershell
Get-FileHash "C:\HyperV-Course\ISO\WS2025-EVAL-x64-EN.iso" -Algorithm SHA256
~~~

The instructor should publish the SHA-256 value of the exact ISO used to prepare the class so students can compare their copy.

### Licensing note

Evaluation media is for evaluation and lab use. Production use requires appropriate licensing.

Microsoft documents supported evaluation-to-retail conversion paths here:

https://learn.microsoft.com/windows-server/get-started/upgrade-conversion-options

---

## Exercise 0.4 — Prepare local course folders

~~~powershell
$Root = "C:\HyperV-Course"
$Folders = @($Root,"$Root\ISO","$Root\VMs","$Root\VHDX","$Root\Export","$Root\Scripts","$Root\Logs")
$Folders | ForEach-Object { New-Item -ItemType Directory -Path $_ -Force | Out-Null }
Get-ChildItem C:\HyperV-Course
~~~

Expected structure:

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

Copy the Windows Server 2025 ISO into the ISO folder before continuing.

---

## Exercise 0.5 — Create HV01 and HV02

### Recommended configuration

| Setting | HV01 | HV02 |
|---|---:|---:|
| Generation | 2 | 2 |
| vCPU | 4 | 4 |
| Startup RAM | 8 GB | 6-8 GB |
| Dynamic Memory | Disabled | Disabled |
| OS disk | 100 GB dynamically expanding VHDX | 100 GB dynamically expanding VHDX |
| OS | Windows Server 2025 Datacenter Evaluation, Desktop Experience | Windows Server 2025 Datacenter Evaluation, Desktop Experience |
| Firmware | UEFI / Generation 2 | UEFI / Generation 2 |

Static memory is recommended for the nested Hyper-V hosts so resource availability is predictable during training.

### Option A — Hyper-V Manager

For each VM:

1. Open Hyper-V Manager.
2. Select the physical host.
3. Choose New > Virtual Machine.
4. Enter HV01 or HV02.
5. Store VM configuration below C:\HyperV-Course\VMs.
6. Select Generation 2.
7. Assign the startup memory shown above.
8. Disable Dynamic Memory.
9. Connect to the course uplink/NAT switch if already available; otherwise leave the VM temporarily disconnected.
10. Create a 100 GB dynamically expanding VHDX.
11. Select installation from a bootable image file.
12. Select C:\HyperV-Course\ISO\WS2025-EVAL-x64-EN.iso.
13. Finish the wizard.
14. Open VM Settings > Processor and configure 4 virtual processors.

### Option B — PowerShell

~~~powershell
$ISO = "C:\HyperV-Course\ISO\WS2025-EVAL-x64-EN.iso"

New-VM -Name "HV01" -Generation 2 -MemoryStartupBytes 8GB -Path "C:\HyperV-Course\VMs" -NewVHDPath "C:\HyperV-Course\VHDX\HV01-OS.vhdx" -NewVHDSizeBytes 100GB
Set-VMProcessor -VMName "HV01" -Count 4
Set-VMMemory -VMName "HV01" -DynamicMemoryEnabled $false
Add-VMDvdDrive -VMName "HV01" -Path $ISO

New-VM -Name "HV02" -Generation 2 -MemoryStartupBytes 8GB -Path "C:\HyperV-Course\VMs" -NewVHDPath "C:\HyperV-Course\VHDX\HV02-OS.vhdx" -NewVHDSizeBytes 100GB
Set-VMProcessor -VMName "HV02" -Count 4
Set-VMMemory -VMName "HV02" -DynamicMemoryEnabled $false
Add-VMDvdDrive -VMName "HV02" -Path $ISO
~~~

Confirm ISO attachment:

~~~powershell
Get-VMDvdDrive -VMName HV01,HV02
~~~

Configure DVD as first boot device:

~~~powershell
$DVD = Get-VMDvdDrive -VMName "HV01"
Set-VMFirmware -VMName "HV01" -FirstBootDevice $DVD
$DVD = Get-VMDvdDrive -VMName "HV02"
Set-VMFirmware -VMName "HV02" -FirstBootDevice $DVD
~~~

Verify:

~~~powershell
Get-VM HV01,HV02 | Select-Object Name,State,Generation,MemoryStartup
Get-VMProcessor HV01,HV02 | Select-Object VMName,Count
Get-VMHardDiskDrive HV01,HV02
Get-VMDvdDrive HV01,HV02
~~~

---

## Exercise 0.6 — Install Windows Server 2025 on HV01 and HV02

Start HV01 and open its console:

~~~powershell
Start-VM HV01
vmconnect.exe localhost HV01
~~~

Boot from the virtual DVD. During Windows Setup select:

**Windows Server 2025 Datacenter Evaluation (Desktop Experience)**

Use a custom installation and install Windows on the 100 GB virtual disk. Repeat for HV02.

After setup:

1. Set the local Administrator password.
2. Sign in.
3. Verify both servers boot normally.
4. Install available Windows updates when practical.
5. Confirm Internet connectivity when the course uplink is available.

Verify the installed edition:

~~~powershell
DISM /online /Get-CurrentEdition
~~~

Reference:

https://learn.microsoft.com/windows-server/get-started/upgrade-conversion-options

---

## Exercise 0.7 — Enable nested virtualization
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
