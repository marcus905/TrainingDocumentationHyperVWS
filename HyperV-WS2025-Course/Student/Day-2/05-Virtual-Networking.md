# Module 05 — Hyper-V Virtual Networking

## Goal

Create the nested workload network used for the rest of the course.

Switch types: External, Internal, Private.

Course design:
~~~text
vSW-Lab: Internal
Network: 172.22.0.0/24
HV01 vEthernet gateway: 172.22.0.1
DC01: 172.22.0.10
SRV01: 172.22.0.20
~~~

~~~powershell
New-VMSwitch -Name "vSW-Lab" -SwitchType Internal
New-NetIPAddress -InterfaceAlias "vEthernet (vSW-Lab)" -IPAddress 172.22.0.1 -PrefixLength 24
New-NetNat -Name "LabNAT" -InternalIPInterfaceAddressPrefix "172.22.0.0/24"
New-VMSwitch -Name "vSW-Private" -SwitchType Private
~~~

Validate with `Get-VMSwitch`, `Get-NetIPAddress` and `Get-NetNat`.