# Module 09 — Break/Fix: Virtual Network

## Break

On HV01:
~~~powershell
Connect-VMNetworkAdapter -VMName SRV01 -SwitchName "vSW-Private"
~~~

## Symptom

SRV01 loses normal lab-network connectivity.

## Investigate

~~~powershell
Get-VM SRV01
Get-VMNetworkAdapter SRV01
Get-VMSwitch
Get-NetIPAddress -InterfaceAlias "vEthernet (vSW-Lab)" -AddressFamily IPv4
Get-NetNat
~~~

Inside SRV01 inspect IP configuration and test 172.22.0.1, 1.1.1.1 and DNS.

State root cause before fixing.

## Reset

~~~powershell
Connect-VMNetworkAdapter -VMName SRV01 -SwitchName "vSW-Lab"
~~~