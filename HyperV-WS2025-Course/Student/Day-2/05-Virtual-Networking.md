# Module 05 — Hyper-V Virtual Networking

## Introduction

Virtual networking is where the nested lab becomes a real multi-machine environment. This module builds the workload network that SRV01 and DC01 will use for the rest of the course, while keeping it isolated from the student's physical LAN.

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

## Concepts to keep in mind

External, Internal, and Private switches solve different connectivity problems. The course uses an Internal switch plus Windows NAT so guests can reach the nested host and outbound networks without depending on the physical LAN.


## What you will do

Create vSW-Lab, assign 172.22.0.1/24 to the host-side adapter, create LabNAT, and create vSW-Private for later comparison and break/fix work.


## What you should observe

Creating an Internal switch adds a host-side `vEthernet` adapter. Creating a Private switch does not create a host-side path.


## Validation checkpoint

Confirm switch types, host-side address, LabNAT prefix, and the existence of vSW-Private.


## Expected end state

HV01 provides both a functional nested workload network and an isolated Private switch for later troubleshooting scenarios.
