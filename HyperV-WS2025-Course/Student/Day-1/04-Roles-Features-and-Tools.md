# Module 04 — Roles, Features and Administration Tools

## Goal

Explore how Windows Server capabilities are installed and managed.

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