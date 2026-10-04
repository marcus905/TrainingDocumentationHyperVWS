# Module 03 — Outer NAT Network

## Introduction

HV01 and HV02 need predictable connectivity without depending on a student's home or corporate LAN. The outer Internal switch and NAT network provide that stable management layer while reducing the risk of disrupting the workstation's real network connection.

## Goal

Create the deterministic management network used by HV01 and HV02.


## Concepts to keep in mind

An Internal Hyper-V switch connects VMs to the host but not directly to the physical LAN. Windows NAT provides outbound connectivity. This outer network is separate from the nested workload network created later inside HV01 and HV02.

## Addressing plan

- Network: 192.168.240.0/24
- Physical host/NAT: 192.168.240.1
- HV01: 192.168.240.11
- HV02: 192.168.240.12

~~~powershell
Get-VMSwitch
Get-NetNat
New-VMSwitch -Name "vSW-Course" -SwitchType Internal
New-NetIPAddress -InterfaceAlias "vEthernet (vSW-Course)" -IPAddress 192.168.240.1 -PrefixLength 24
New-NetNat -Name "CourseNAT" -InternalIPInterfaceAddressPrefix "192.168.240.0/24"
~~~

## Validate

~~~powershell
Get-VMSwitch -Name "vSW-Course"
Get-NetIPAddress -InterfaceAlias "vEthernet (vSW-Course)" -AddressFamily IPv4
Get-NetNat -Name "CourseNAT"
~~~

## What you should observe

Hyper-V creates a host-side adapter named `vEthernet (vSW-Course)`. It should own 192.168.240.1/24 and the NAT object should cover 192.168.240.0/24.


## Validation checkpoint

Confirm switch type, host-side IPv4 address, and CourseNAT prefix before creating HV01/HV02.


## Expected end state

The physical host provides a stable outer management network for both Windows Server VMs.


## What you will do

Inspect existing switches/NAT objects first, then create vSW-Course, assign 192.168.240.1/24 to its host-side adapter, create CourseNAT, and validate each object.
