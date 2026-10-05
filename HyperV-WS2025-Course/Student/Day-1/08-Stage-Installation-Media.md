# Module 07 — Stage Installation Media

## Introduction

The Windows Server ISO exists on the physical workstation, but Day 2 needs that media inside HV01 to install nested VMs. This module makes that handoff explicit instead of relying on an undocumented copy step.

## Goal

Copy the Windows Server ISO from the physical host into HV01 for Day 2.

## Concepts to keep in mind

`Copy-VMFile` transfers files through the Hyper-V Guest Service Interface, avoiding the need to configure a temporary SMB share just for course media.

## What you will do

Enable Guest Service Interface on HV01, copy the ISO from the physical host, and compare source/destination SHA-256 hashes.

## Hands-on / detailed content

On the physical Windows 11 host:

~~~powershell
Enable-VMIntegrationService -VMName "HV01" -Name "Guest Service Interface"
Copy-VMFile -Name "HV01" -SourcePath "C:\HyperV-Course\ISO\WS2025-EVAL-x64-EN.iso" -DestinationPath "D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso" -FileSource Host -CreateFullPath
~~~

Inside HV01:

~~~powershell
Get-Item "D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso"
Get-FileHash "D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso" -Algorithm SHA256
~~~

Compare the hash with the source ISO.

## What you should observe

The ISO should appear inside HV01 at D:\Hyper-V\ISO and its hash should remain identical to the physical-host copy.

## Validation checkpoint

Verify integration service state, file presence, file size, and SHA-256 equality.

## Expected end state

HV01 contains trusted installation media ready for SRV01 and DC01 creation.
