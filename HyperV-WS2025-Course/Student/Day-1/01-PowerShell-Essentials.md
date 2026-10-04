# Module 01 — PowerShell Essentials

## Introduction

PowerShell is used throughout the course as both an administration tool and a troubleshooting interface. This module is intentionally small: the aim is to learn the patterns you will reuse, not to become a PowerShell developer.

## Goal

Learn only the PowerShell habits used throughout the course.

~~~powershell
$PSVersionTable | Select-Object PSVersion,PSEdition
Get-Command *NetAdapter*
Get-Help Get-NetAdapter -Examples
~~~


## Concepts to keep in mind

Focus on command discovery, object-based output, the pipeline, filtering, selecting properties, variables, help, elevation, and reading errors as evidence.

## Pipeline

~~~powershell
Get-Service | Where-Object Status -eq "Running" | Sort-Object Name | Select-Object Name,Status
~~~

Key ideas: objects, pipeline, `Where-Object`, `Select-Object`, variables, tab completion, elevated shells, `-WhatIf`, and treating errors as evidence.

## Validation

Explain powershell.exe vs pwsh.exe, Verb-Noun naming, Get-Command, Get-Help, pipeline filtering and WhatIf.

## What you should observe

Notice that commands such as `Get-Service` return objects with properties that can be filtered and selected rather than only formatted text.


## Validation checkpoint

Explain what each stage of a simple pipeline does and identify the current PowerShell edition.


## Expected end state

You can discover an unfamiliar command, read its help, filter its output, and interpret a basic error without guessing.
