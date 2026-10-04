# Module 02 — Install Hyper-V on HV01

## Goal

Install the role and validate that the host is operational.

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