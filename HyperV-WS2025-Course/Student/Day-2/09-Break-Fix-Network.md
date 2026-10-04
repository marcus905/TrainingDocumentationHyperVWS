# Module 09 — Break/Fix: Virtual Network

## Introduction

This incident takes a working SRV01 and breaks connectivity at the Hyper-V switch layer. The objective is to diagnose from the symptom outward rather than jumping directly to guest IP or DNS changes.

## Concepts to keep in mind

A VM can have perfectly correct guest TCP/IP settings and still be disconnected from the intended Layer-2 virtual network. Troubleshooting must include the Hyper-V vNIC-to-switch mapping.

## What you will do

Move SRV01 to vSW-Private, stop looking at the break command, inspect VM state/switch mapping/NAT/guest TCP-IP, prove the failed layer, then reconnect it to vSW-Lab.

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

## What you should observe

Guest IP settings remain present, but SRV01 should lose access to the 172.22.0.1 gateway because vSW-Private has no host-side adapter or NAT path.

## Validation checkpoint

State which evidence proves the fault is the virtual-switch attachment rather than guest IP, gateway, or DNS.

## Expected end state

SRV01 is reconnected to vSW-Lab and the full connectivity matrix works again.
