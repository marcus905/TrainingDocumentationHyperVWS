# Module 04 — Roles, Features and Administration Tools

## Introduction

Windows Server functionality is assembled from roles, role services, features, and services. This module connects those concepts to the tools administrators actually use to inspect and change them.

## Goal

Explore how Windows Server capabilities are installed and managed.

## Concepts to keep in mind

A role provides a server workload, a feature adds supporting capability, and Windows services are runtime components. Management tools expose these layers through different interfaces.

## What you will do

Inspect installed components, preview a change with `-WhatIf`, install/remove the training feature, and compare PowerShell with a GUI management tool.

## Hands-on / detailed content

~~~powershell
Get-WindowsFeature
Get-WindowsFeature | Where-Object Installed
Get-WindowsFeature *Telnet*
Install-WindowsFeature Telnet-Client -WhatIf
Install-WindowsFeature Telnet-Client
Uninstall-WindowsFeature Telnet-Client
~~~

Know when to use Server Manager, Computer Management, Services, Event Viewer, Task Manager, Resource Monitor, Performance Monitor, SConfig, PowerShell and Windows Admin Center.

## Validation

Explain the difference between a role, role service, feature, Windows service and management tool.

## What you should observe

Use `-WhatIf` to see how PowerShell can preview supported administrative actions before changing the server.

## Validation checkpoint

Install, verify, and remove the training feature, then locate equivalent information in at least one GUI tool.

## Expected end state

You can identify the correct management surface for a role, feature, service, or system-level question.
