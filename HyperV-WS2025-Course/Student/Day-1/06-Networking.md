# Module 05 — Basic Networking

## Introduction

The outer management network was created on Day 0; today the goal is to understand it rather than simply accept that it works. You will inspect each dependency from the adapter outward so later network failures can be isolated systematically.

## Goal

Validate the outer management network built on Day 0.

## Concepts to keep in mind

Treat connectivity as a chain: adapter -> IP -> local subnet -> gateway -> routing/NAT -> DNS -> application port.

## What you will do

Inspect HV01's adapter, IPv4 configuration, DNS, and routes, then test each dependency from nearest to farthest.

## Hands-on / detailed content

~~~text
Network: 192.168.240.0/24
Gateway: 192.168.240.1
HV01: 192.168.240.11
HV02: 192.168.240.12
DNS: 1.1.1.1 or instructor-approved resolver
~~~

~~~powershell
Get-NetAdapter
Get-NetIPConfiguration
Get-NetRoute -AddressFamily IPv4
Get-DnsClientServerAddress -AddressFamily IPv4
Test-NetConnection 192.168.240.1
Test-NetConnection 1.1.1.1
Resolve-DnsName microsoft.com
Test-NetConnection microsoft.com -Port 443
~~~

Troubleshoot in order: adapter -> IP -> subnet -> gateway -> routing/NAT -> DNS -> application port.

## What you should observe

A successful test to 1.1.1.1 proves basic routed connectivity but does not prove DNS. Each test should answer one specific question.

## Validation checkpoint

Validate HV01's address, gateway, DNS, default route, external IP reachability, name resolution, and HTTPS connectivity.

## Expected end state

HV01 networking is functional and understandable enough to troubleshoot without trial-and-error.
