# Module 07 — Install and Network SRV01

Attach `D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso`, boot SRV01 and install Windows Server 2025.

Add an optional 10 GB data disk:
~~~powershell
New-VHD -Path "D:\Hyper-V\VHDX\SRV01-DATA.vhdx" -SizeBytes 10GB -Dynamic
Add-VMHardDiskDrive -VMName SRV01 -Path "D:\Hyper-V\VHDX\SRV01-DATA.vhdx"
~~~

Inside SRV01 configure:
~~~powershell
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 172.22.0.20 -PrefixLength 24 -DefaultGateway 172.22.0.1
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 1.1.1.1
~~~

Validate gateway, Internet IP, DNS and HTTPS connectivity.