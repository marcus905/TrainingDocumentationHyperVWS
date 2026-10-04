# Module 05 — Basic Networking

## Goal

Validate the outer management network built on Day 0.

~~~text
Network: 192.168.240.0/24
Gateway: 192.168.240.1
HV01: 192.168.240.11
HV02: 192.168.240.12
DNS: 1.1.1.1 or instructor-approved resolver
~~~

~~~powershell
Get-NetAdapter
Get-NetIPConfiguration
Get-NetRoute -AddressFamily IPv4
Get-DnsClientServerAddress -AddressFamily IPv4
Test-NetConnection 192.168.240.1
Test-NetConnection 1.1.1.1
Resolve-DnsName microsoft.com
Test-NetConnection microsoft.com -Port 443
~~~

Troubleshoot in order: adapter -> IP -> subnet -> gateway -> routing/NAT -> DNS -> application port.