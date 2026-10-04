# Module 06 — Enable Nested Virtualization

## Goal

Expose virtualization extensions to HV01 and HV02.

Power off both outer VMs, then on the physical host run:

~~~powershell
Set-VMProcessor -VMName "HV01" -ExposeVirtualizationExtensions $true
Set-VMProcessor -VMName "HV02" -ExposeVirtualizationExtensions $true
Get-VMProcessor HV01,HV02 | Select-Object VMName,ExposeVirtualizationExtensions
~~~

Expected: `ExposeVirtualizationExtensions = True`.

Do not install Hyper-V inside HV01/HV02 yet.