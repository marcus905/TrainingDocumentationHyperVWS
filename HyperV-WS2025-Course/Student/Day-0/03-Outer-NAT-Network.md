# Module 03 — Outer NAT Network

## Goal

Create the deterministic management network used by HV01 and HV02.

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