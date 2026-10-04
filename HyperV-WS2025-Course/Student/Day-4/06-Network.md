# Module 06 — Network Break/Fix

## Introduction

A network outage can originate in the guest, the virtual NIC, the virtual switch, the host-side Internal adapter, NAT, routing, DNS, or an application port. This incident practices testing those layers in a fixed order.

## Concepts to keep in mind

Start from the nearest dependency and move outward. If SRV01 cannot reach 172.22.0.1, there is little value in testing public DNS first.

## What you will do

Move SRV01 to vSW-Private, work only from the symptom, inspect the Hyper-V vNIC/switch layer, then the HV01 lab network and guest TCP/IP stack, and finally restore vSW-Lab.

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

## What you should observe

SRV01 keeps its guest IP configuration but loses the Layer-2 path to the host-side 172.22.0.1 gateway.

## Validation checkpoint

State the specific evidence that rules out guest IP configuration and identifies the wrong virtual-switch attachment.

## Expected end state

SRV01 is connected to vSW-Lab and gateway, external IP, DNS, and application-port checks are normal again.
