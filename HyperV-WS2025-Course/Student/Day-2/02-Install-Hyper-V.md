# Module 02 — Install Hyper-V on HV01

## Introduction

This is the point where HV01 becomes a virtualization host. The installation itself is simple; the important part is verifying that the role, management service, host capabilities, and default storage paths are all ready before creating VMs.

## Goal

Install the role and validate that the host is operational.

## Concepts to keep in mind

Installing the role and rebooting changes the boot architecture so the Hyper-V hypervisor loads underneath the management OS. `vmms` then provides core VM management services.

## What you will do

Install Hyper-V, restart HV01, inspect the role/service/host state, and set the default VM and VHDX paths to D:.

## Hands-on / detailed content

~~~powershell
Get-WindowsFeature Hyper-V
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
~~~

After restart:
~~~powershell
Get-WindowsFeature Hyper-V
Get-Service vmms
Get-VMHost
Set-VMHost -VirtualMachinePath "D:\Hyper-V\VMs" -VirtualHardDiskPath "D:\Hyper-V\VHDX"
Get-VMHost | Select-Object VirtualMachinePath,VirtualHardDiskPath
~~~

## Validation

- [ ] Hyper-V installed.
- [ ] VMMS running.
- [ ] Hyper-V Manager opens.
- [ ] Default VM/VHDX paths point to D:.

## What you should observe

After restart, Hyper-V Manager and Hyper-V PowerShell cmdlets should work and `Get-VMHost` should expose the host configuration.

## Validation checkpoint

Verify the role is installed, VMMS is running, Hyper-V Manager opens, and both default storage paths point to D:\Hyper-V.

## Expected end state

HV01 is a validated Hyper-V host ready for switches and VMs.
