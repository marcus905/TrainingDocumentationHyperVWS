# Module 03 — Hyper-V Management Tools

## Introduction

Hyper-V can be managed through several interfaces, and choosing the right one matters. This module is about recognizing which tool helps you explore, automate, troubleshoot, or manage larger environments rather than treating every tool as interchangeable.

## Goal

Know which tool fits which administrative task.

Use Hyper-V Manager for visual VM/switch/disk work and the Hyper-V PowerShell module for repeatable administration.

~~~powershell
Get-Command -Module Hyper-V
Get-VMHost
Get-VM
Get-VMSwitch
~~~

Also discuss Windows Admin Center, Failover Cluster Manager and SCVMM at a high level.


## Concepts to keep in mind

Hyper-V Manager is strong for local visual administration, PowerShell for repeatability and automation, Windows Admin Center for browser-based management, Failover Cluster Manager for clustered workloads, and SCVMM for broader datacenter management.

## Validation

Choose an appropriate tool for local VM creation, remote administration, clustered VM management and automation.

## What you will do

Explore the local Hyper-V Manager surface, discover Hyper-V cmdlets, and retrieve the same basic host/VM/switch information with PowerShell.


## What you should observe

GUI and PowerShell expose the same underlying objects in different ways; one is not inherently 'more correct' than the other.


## Validation checkpoint

Choose the most appropriate tool for local VM work, repeatable automation, clustered administration, and enterprise-scale management.


## Expected end state

You can switch between GUI and PowerShell without losing track of the Hyper-V objects being managed.
