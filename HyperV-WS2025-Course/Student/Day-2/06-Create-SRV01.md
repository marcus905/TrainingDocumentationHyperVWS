# Module 06 — Create SRV01

## Specification

- Generation 2
- 2 vCPU
- Dynamic Memory: 2 GB startup, 1 GB min, 4 GB max
- 50 GB OS VHDX
- vSW-Lab
- Guest IP later: 172.22.0.20/24

~~~powershell
New-VM -Name "SRV01" -Generation 2 -MemoryStartupBytes 2GB -Path "D:\Hyper-V\VMs" -NewVHDPath "D:\Hyper-V\VHDX\SRV01.vhdx" -NewVHDSizeBytes 50GB -SwitchName "vSW-Lab"
Set-VMProcessor SRV01 -Count 2
Set-VMMemory SRV01 -DynamicMemoryEnabled $true -MinimumBytes 1GB -StartupBytes 2GB -MaximumBytes 4GB
~~~

Inspect with `Get-VM`, `Get-VMProcessor`, `Get-VMMemory`, `Get-VMNetworkAdapter`, `Get-VMHardDiskDrive` and `Get-VMFirmware`.