# Module 03 — Initial Server Configuration

## Introduction

A newly installed server should be inspected before it is changed. The purpose of this module is to build the habit of establishing a known baseline for identity, time, networking, and core management services.

## Goal

Inspect and validate a newly deployed Windows Server host.

~~~powershell
Get-ComputerInfo
hostname.exe
Get-Date
Get-TimeZone
Get-NetAdapter
Get-NetIPConfiguration
Get-DnsClientServerAddress
Get-NetRoute -AddressFamily IPv4
Get-Service EventLog,WinRM,W32Time
~~~

Use Server Manager, Computer Management, Services and Event Viewer to locate the same information through the GUI.


## Concepts to keep in mind

Configuration work is safer when you can describe the current state first. Server identity, time, DNS, routing, WinRM, and Event Log availability affect many later technologies.

## Validation

- [ ] Host identity confirmed.
- [ ] Time/time zone reviewed.
- [ ] Adapter, IP, DNS and default route understood.
- [ ] EventLog, WinRM and W32Time inspected.

## What you should observe

Compare the same information in PowerShell and GUI tools and notice which view is faster for inspection versus exploration.


## Validation checkpoint

Confirm host name, time/time zone, network configuration, default route, DNS, and the state of EventLog, WinRM, and W32Time.


## Expected end state

HV01 has a documented basic operating-system baseline before roles or storage are changed.
