# Day 1 — Windows Server 2025 Fundamentals

## Learning objectives
Students should be able to:
- explain basic Windows Server architecture;
- distinguish Server Core and Desktop Experience;
- perform initial server configuration;
- manage roles and features;
- use Server Manager, SConfig and PowerShell;
- configure IPv4, DNS and basic routing;
- inspect and configure local storage;
- validate a newly deployed server;
- troubleshoot basic post-installation issues.

## Module 1 — Architecture and installation options
Topics include Standard/Datacenter concepts, roles, features, Server Core, Desktop Experience, services, events, networking, storage, remote management and PowerShell.

Reference: https://learn.microsoft.com/windows-server/get-started/getting-started-with-server-core

## Lab 1.1 — Initial configuration
Tasks:
1. Verify edition/build.
2. Rename the server to HV01.
3. Set time zone.
4. Review Windows Update state.
5. Inspect adapters.
6. Configure IPv4 and DNS.
7. Inspect routing.
8. Verify connectivity and name resolution.

~~~powershell
Get-ComputerInfo
hostname
Get-NetAdapter
Get-NetIPAddress
Get-NetIPConfiguration
Get-DnsClientServerAddress
Get-NetRoute
Rename-Computer -NewName "HV01"
Test-NetConnection
Resolve-DnsName microsoft.com
~~~

Key distinction: IP connectivity, routing and DNS resolution are separate layers.

## Module 2 — Roles and features
~~~powershell
Get-WindowsFeature
Get-WindowsFeature | Where-Object Installed
Install-WindowsFeature Telnet-Client
Get-WindowsFeature Telnet-Client
Remove-WindowsFeature Telnet-Client
~~~

The Telnet Client example is used only to demonstrate feature-management workflow.

## Module 3 — Administration tools
Introduce Server Manager, Computer Management, Services, Event Viewer, Task Manager, Resource Monitor, Performance Monitor, SConfig, PowerShell and Windows Admin Center conceptually.

## Module 4 — Basic networking
~~~powershell
ipconfig /all
Get-NetAdapter
Get-NetIPAddress
Get-NetIPConfiguration
Get-DnsClientServerAddress
Get-NetRoute
Test-NetConnection
Resolve-DnsName
~~~

### Lab 1.2 — Static IPv4 pattern
~~~powershell
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 10.10.10.11 -PrefixLength 24 -DefaultGateway 10.10.10.1
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 10.10.10.10
~~~

Adapt addresses to the actual class topology.

## Module 5 — Local storage
~~~powershell
Get-Disk
Get-Partition
Get-Volume
~~~

### Lab 1.3 — Add Hyper-V data storage
Attach a 20-40 GB VHDX to HV01, initialize it as GPT, create a D: volume and prepare:

~~~text
D:\Hyper-V
|
+-- VMs
+-- VHDX
+-- ISO
+-- Replica
~~~

## Break/Fix 1 — DNS failure
Symptom: IP connectivity works, but hostnames cannot be resolved.

Use:

~~~powershell
ipconfig /all
Get-DnsClientServerAddress
Resolve-DnsName microsoft.com
Get-NetRoute
~~~

Learning outcome: prove whether the problem is DNS or general network connectivity.

## End-of-day validation
- [ ] Server name correct.
- [ ] IPv4/DNS documented.
- [ ] Default route understood.
- [ ] Roles/features queried.
- [ ] Additional storage prepared.
- [ ] Event Viewer reviewed.
- [ ] Basic connectivity tests understood.

## Microsoft references
- https://learn.microsoft.com/windows-server/get-started/overview
- https://learn.microsoft.com/windows-server/get-started/install-windows-server
- https://learn.microsoft.com/windows-server/get-started/hardware-requirements
- https://learn.microsoft.com/windows-server/get-started/getting-started-with-server-core
