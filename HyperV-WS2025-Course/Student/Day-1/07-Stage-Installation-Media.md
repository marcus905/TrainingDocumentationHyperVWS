# Module 07 — Stage Installation Media

## Goal

Copy the Windows Server ISO from the physical host into HV01 for Day 2.

On the physical Windows 11 host:

~~~powershell
Enable-VMIntegrationService -VMName "HV01" -Name "Guest Service Interface"
Copy-VMFile -Name "HV01" -SourcePath "C:\HyperV-Course\ISO\WS2025-EVAL-x64-EN.iso" -DestinationPath "D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso" -FileSource Host -CreateFullPath
~~~

Inside HV01:

~~~powershell
Get-Item "D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso"
Get-FileHash "D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso" -Algorithm SHA256
~~~

Compare the hash with the source ISO.