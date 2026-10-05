# Module 10 — Optional Windows 11 CLIENT01 and vTPM Readiness

## Introduction

CLIENT01 is optional in the core five-day course, but it is useful when you want a Windows 11 guest for management, client/server testing, or future Active Directory exercises.

Windows 11 has stricter VM requirements than the Windows Server guests used elsewhere in the lab. If those requirements are configured before installation, Windows Setup should proceed normally without registry hacks or requirement-bypass procedures.

## Goal

Prepare a supported Hyper-V Generation 2 VM that satisfies the Windows 11 guest requirements, with particular attention to Secure Boot and virtual TPM.

## Concepts to keep in mind

For Windows 11 on Hyper-V, Microsoft documents these VM requirements:

- Generation 2;
- Secure Boot enabled;
- virtual TPM enabled;
- at least 4 GB RAM;
- at least 64 GB storage;
- at least 2 virtual processors.

The Hyper-V host processor must also meet the applicable Windows 11 processor requirements.

A useful nested-lab detail is that the virtual TPM is emulated for the guest independently of the Hyper-V host TPM presence or version.

That means a TPM-related Windows 11 guest-installation failure should first be investigated as a VM configuration problem, not automatically as a missing physical TPM in HV01.

## What you will do

If CLIENT01 is required, create it on HV01 using the following baseline:

~~~text
Name:          CLIENT01
Generation:    2
vCPU:          2
Startup RAM:   4 GB
VHDX:          64 GB or larger
Switch:        vSW-Lab
Guest IP:      172.22.0.100/24
Gateway:       172.22.0.1
Secure Boot:   Enabled
vTPM:          Enabled
~~~

Because CLIENT01 consumes additional nested memory, only run it when the physical workstation has sufficient RAM.

## Create the VM

~~~powershell
New-VM -Name "CLIENT01" -Generation 2 -MemoryStartupBytes 4GB -Path "D:\Hyper-V\VMs" -NewVHDPath "D:\Hyper-V\VHDX\CLIENT01.vhdx" -NewVHDSizeBytes 64GB -SwitchName "vSW-Lab"
Set-VMProcessor -VMName "CLIENT01" -Count 2
~~~

Keep CLIENT01 powered off while configuring its security settings.

## Verify Secure Boot

~~~powershell
Get-VMFirmware -VMName "CLIENT01" | Select-Object SecureBoot
~~~

If Secure Boot is not enabled:

~~~powershell
Set-VMFirmware -VMName "CLIENT01" -EnableSecureBoot On
~~~

## Configure a local key protector

~~~powershell
Set-VMKeyProtector -VMName "CLIENT01" -NewLocalKeyProtector
~~~

## Enable virtual TPM

~~~powershell
Enable-VMTPM -VMName "CLIENT01"
Get-VMSecurity -VMName "CLIENT01"
~~~

## Attach Windows 11 installation media

If Windows 11 media was staged for the optional client lab:

~~~powershell
Add-VMDvdDrive -VMName "CLIENT01" -Path "D:\Hyper-V\ISO\<Windows-11-ISO-name>.iso"
~~~

Set the virtual DVD first in the boot order if required.

## What you should observe

Before Windows 11 Setup begins, CLIENT01 should already have Generation 2 UEFI firmware, Secure Boot, two vCPUs, at least 4 GB RAM, a 64 GB or larger VHDX, and vTPM enabled.

Inside an installed Windows 11 guest, inspect TPM with:

~~~powershell
Get-Tpm
~~~

or open tpm.msc.

## If Windows 11 Setup reports that requirements are not met

Do not immediately look for a bypass. Check the VM configuration in this order:

1. Confirm CLIENT01 is Generation 2.
2. Confirm at least 2 vCPUs.
3. Confirm at least 4 GB RAM.
4. Confirm the boot disk is 64 GB or larger.
5. Confirm Secure Boot is enabled.
6. Confirm the VM has a local key protector.
7. Confirm vTPM is enabled.
8. Confirm the Windows 11 ISO is a supported installation source.
9. Confirm the underlying host CPU meets Windows 11 processor requirements.

Useful commands:

~~~powershell
Get-VM CLIENT01
Get-VMProcessor CLIENT01
Get-VMMemory CLIENT01
Get-VMHardDiskDrive CLIENT01
Get-VMFirmware CLIENT01
Get-VMSecurity CLIENT01
~~~

## Why the course does not use requirement bypasses

Bypassing Windows 11 setup checks would hide the exact Hyper-V security concepts this optional module is intended to teach.

The supported learning objective is to configure the virtual hardware correctly and understand why Windows 11 expects UEFI/Secure Boot and TPM-backed security.

## Validation checkpoint

- [ ] CLIENT01 is Generation 2.
- [ ] Two or more vCPUs configured.
- [ ] At least 4 GB RAM configured.
- [ ] Boot VHDX is at least 64 GB.
- [ ] Secure Boot enabled.
- [ ] Local key protector configured.
- [ ] vTPM enabled.
- [ ] CLIENT01 attached to vSW-Lab.
- [ ] Windows 11 Setup passes the hardware/security requirement stage.

## Expected end state

If the optional module is used, CLIENT01 can install Windows 11 without requirement-bypass modifications and provides a clean example of Generation 2, Secure Boot, and vTPM security configuration.

## Microsoft references

- Windows 11 requirements for virtual machines: https://learn.microsoft.com/windows/whats-new/windows-11-requirements
- Hyper-V Generation 2 security features: https://learn.microsoft.com/windows-server/virtualization/hyper-v/generation-2-virtual-machine-security-features
- Enable-VMTPM: https://learn.microsoft.com/powershell/module/hyper-v/enable-vmtpm
- Set-VMKeyProtector: https://learn.microsoft.com/powershell/module/hyper-v/set-vmkeyprotector