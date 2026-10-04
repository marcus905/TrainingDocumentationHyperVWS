# Module 07 — Install and Network SRV01

## Introduction

Now the virtual hardware becomes a usable Windows Server workload. This module also reinforces the difference between Hyper-V storage/network configuration and the guest operating system's own disk and TCP/IP configuration.

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

## Concepts to keep in mind

Attaching a VHDX to a VM does not initialize it inside Windows, and connecting a vNIC to a switch does not assign a guest IP. The hypervisor and guest layers must both be configured and verified.

## What you will do

Install Windows Server in SRV01, attach the 10 GB data VHDX, inspect it from the guest, configure 172.22.0.20/24 with gateway 172.22.0.1, and validate layered connectivity.

## What you should observe

The guest should reach the nested gateway first, then external IP connectivity, then DNS/name-based destinations. The extra VHDX should appear as a separate guest disk device.

## Validation checkpoint

Verify guest IP/gateway/DNS, host-to-guest connectivity, external IP reachability, and visibility of the attached data disk.

## Expected end state

SRV01 is a working Windows Server VM with predictable networking and an additional data disk for later storage exercises.
