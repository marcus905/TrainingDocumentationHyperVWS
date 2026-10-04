# Module 08 — Create DC01

## Introduction

A second VM lets the lab demonstrate multi-VM networking and leaves room for optional directory-services extensions. In the core course, DC01 is simply another Windows Server workload and should not be assumed to provide DNS or Active Directory.

## Goal

Create a second Generation 2 Windows Server VM for the evolving lab.

## Concepts to keep in mind

Names can imply roles that do not yet exist. Always distinguish a VM's name from the services actually installed and validated inside it.

## What you will do

Create the second Generation 2 VM, apply the course resource/network defaults, and confirm its Hyper-V configuration. Install/configure the guest as directed during class.

## Hands-on / detailed content

Specification: 2 vCPU, 2 GB RAM, 40 GB VHDX, vSW-Lab, 172.22.0.10/24, gateway 172.22.0.1.

The name DC01 is reserved for an optional future AD DS/DNS extension. The core five-day course does not require promotion to a domain controller.

Validate with `Get-VM DC01`, `Get-VMNetworkAdapter DC01` and `Get-VMHardDiskDrive DC01`.

## What you should observe

DC01 should appear as a separate VM on vSW-Lab with its own VHDX and the planned 172.22.0.10 address once guest networking is configured.

## Validation checkpoint

Confirm VM state, vNIC switch mapping, VHDX location, and guest addressing if the OS has been installed.

## Expected end state

The nested lab now has two distinct server workloads without silently introducing AD DS/DNS dependencies.
