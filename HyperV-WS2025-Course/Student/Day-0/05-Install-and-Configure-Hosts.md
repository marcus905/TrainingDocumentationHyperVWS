# Module 05 — Install and Configure HV01/HV02

## Introduction

Installing Windows is only part of the task. The two servers also need stable management identities and addressing because later Replica, troubleshooting, and recovery labs depend on being able to reach the same hosts at the same addresses every time.

## Goal

Install Windows Server 2025 and configure deterministic management addresses.

Install Windows Server 2025 Datacenter with Desktop Experience on each 100 GB OS disk.


## Concepts to keep in mind

The outer management network is a course dependency, not just an example. HV01 and HV02 must remain reachable on 192.168.240.11 and .12 throughout the week.

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

## What you should observe

Both servers should reach 192.168.240.1, external IP connectivity should work when allowed, and DNS queries should resolve after configuration.


## Validation checkpoint

Validate IP address, prefix, gateway, DNS, and removal of the outer virtual DVD so drive D: remains available.


## Expected end state

Both outer hosts boot with deterministic management networking and drive D: is free for the training data disk.


## What you will do

Install Windows Server on both outer VMs, configure their deterministic management addresses, verify gateway/DNS connectivity, then remove the installation DVD devices so D: remains available.
