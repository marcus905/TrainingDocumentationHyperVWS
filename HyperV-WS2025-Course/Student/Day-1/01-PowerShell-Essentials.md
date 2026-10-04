# Module 01 — PowerShell Essentials

## Goal

Learn only the PowerShell habits used throughout the course.

~~~powershell
$PSVersionTable | Select-Object PSVersion,PSEdition
Get-Command *NetAdapter*
Get-Help Get-NetAdapter -Examples
~~~

## Pipeline

~~~powershell
Get-Service | Where-Object Status -eq "Running" | Sort-Object Name | Select-Object Name,Status
~~~

Key ideas: objects, pipeline, `Where-Object`, `Select-Object`, variables, tab completion, elevated shells, `-WhatIf`, and treating errors as evidence.

## Validation

Explain powershell.exe vs pwsh.exe, Verb-Noun naming, Get-Command, Get-Help, pipeline filtering and WhatIf.