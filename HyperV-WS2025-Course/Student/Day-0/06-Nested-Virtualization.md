# Module 06 — Enable Nested Virtualization

## Introduction

HV01 and HV02 are still ordinary VMs at this point. Exposing virtualization extensions allows them to become Hyper-V hosts later, which is the key capability that makes the nested course design possible.

## Goal

Expose virtualization extensions to HV01 and HV02.

Power off both outer VMs, then on the physical host run:

~~~powershell
Set-VMProcessor -VMName "HV01" -ExposeVirtualizationExtensions $true
Set-VMProcessor -VMName "HV02" -ExposeVirtualizationExtensions $true
Get-VMProcessor HV01,HV02 | Select-Object VMName,ExposeVirtualizationExtensions
~~~

Expected: `ExposeVirtualizationExtensions = True`.

Do not install Hyper-V inside HV01/HV02 yet.

## Concepts to keep in mind

Nested virtualization does not create another physical CPU. The outer hypervisor exposes virtualization capabilities to the guest so that Hyper-V can run inside that guest VM.


## What you should observe

`ExposeVirtualizationExtensions` should report `True` for both HV01 and HV02.


## Validation checkpoint

Confirm both VMs were powered off for the change and both processor configurations now expose virtualization extensions.


## Expected end state

HV01 and HV02 are capable of hosting nested Hyper-V, but the role remains uninstalled until Day 2/Day 3.
