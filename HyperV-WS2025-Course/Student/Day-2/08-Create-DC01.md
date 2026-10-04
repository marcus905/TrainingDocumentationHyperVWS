# Module 08 — Create DC01

## Goal

Create a second Generation 2 Windows Server VM for the evolving lab.

Specification: 2 vCPU, 2 GB RAM, 40 GB VHDX, vSW-Lab, 172.22.0.10/24, gateway 172.22.0.1.

The name DC01 is reserved for an optional future AD DS/DNS extension. The core five-day course does not require promotion to a domain controller.

Validate with `Get-VM DC01`, `Get-VMNetworkAdapter DC01` and `Get-VMHardDiskDrive DC01`.