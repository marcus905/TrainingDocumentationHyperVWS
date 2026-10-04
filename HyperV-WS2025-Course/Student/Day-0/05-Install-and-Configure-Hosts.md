# Module 05 — Install and Configure HV01/HV02

## Goal

Install Windows Server 2025 and configure deterministic management addresses.

Install Windows Server 2025 Datacenter with Desktop Experience on each 100 GB OS disk.

## Configure HV01

~~~powershell
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.240.11 -PrefixLength 24 -DefaultGateway 192.168.240.1
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 1.1.1.1
~~~

## Configure HV02

~~~powershell
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.240.12 -PrefixLength 24 -DefaultGateway 192.168.240.1
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 1.1.1.1
~~~

Validate with `Get-NetIPConfiguration`, `Test-NetConnection 192.168.240.1`, `Test-NetConnection 1.1.1.1`, and `Resolve-DnsName microsoft.com`.

After installation, remove the outer virtual DVD drives from the physical host so drive D: remains available for the training data disk:

~~~powershell
Get-VMDvdDrive HV01 | Remove-VMDvdDrive
Get-VMDvdDrive HV02 | Remove-VMDvdDrive
~~~