# Module 06 — Network Break/Fix

## Guided fault — wrong switch

On HV01:

~~~powershell
Connect-VMNetworkAdapter -VMName SRV01 -SwitchName "vSW-Private"
~~~

## Symptom

SRV01 has lost normal lab-network connectivity.

## Investigate

Hyper-V layer:

~~~powershell
Get-VMSwitch
Get-VMNetworkAdapter -VMName SRV01
~~~

HV01 lab network:

~~~powershell
Get-NetIPAddress -InterfaceAlias "vEthernet (vSW-Lab)" -AddressFamily IPv4
Get-NetNat
~~~

Inside SRV01:

~~~powershell
Get-NetIPConfiguration
Get-NetRoute -AddressFamily IPv4
Get-DnsClientServerAddress -AddressFamily IPv4
Test-NetConnection 172.22.0.1
Test-NetConnection 1.1.1.1
Resolve-DnsName microsoft.com
~~~

Troubleshoot adapter -> IP -> local subnet -> gateway -> NAT -> DNS -> application port.

## Reset

~~~powershell
Connect-VMNetworkAdapter -VMName SRV01 -SwitchName "vSW-Lab"
~~~
