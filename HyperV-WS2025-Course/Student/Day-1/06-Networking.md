# Module 06 — Basic Networking

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

## Enable ICMP Echo Request for lab diagnostics

Ping is not a complete network test, but allowing ICMP Echo Request can make basic host-to-host diagnostics easier during the course.

### SConfig method

On HV01 or HV02:

1. Open an elevated PowerShell window.
2. Run `SConfig`.
3. Select **4 — Configure remote management**.
4. Select **3 — Enable server response to ping**.
5. Exit SConfig when finished.

### PowerShell method — scoped course rule

For a more explicit and tightly scoped lab rule, create an inbound ICMPv4 Echo Request rule that only allows the outer course subnet:

~~~powershell
New-NetFirewallRule `
    -DisplayName "HyperV Course - ICMPv4 Echo Request" `
    -Direction Inbound `
    -Action Allow `
    -Protocol ICMPv4 `
    -IcmpType 8 `
    -RemoteAddress 192.168.240.0/24
~~~

Verify:

~~~powershell
Get-NetFirewallRule -DisplayName "HyperV Course - ICMPv4 Echo Request"
~~~

Test from the peer host:

~~~powershell
Test-Connection 192.168.240.12 -Count 2
~~~

or from HV02 to HV01:

~~~powershell
Test-Connection 192.168.240.11 -Count 2
~~~

### Security note

ICMP is useful evidence, but successful ping does **not** prove that DNS, TCP 443, Hyper-V Replica, WinRM, or another application protocol is working.

Prefer a rule scoped to the required management subnet rather than opening ICMP broadly.

## What you should observe

A successful test to 1.1.1.1 proves basic routed connectivity but does not prove DNS. Each test should answer one specific question.

## Validation checkpoint

Validate HV01's address, gateway, DNS, default route, external IP reachability, name resolution, and HTTPS connectivity.

## Expected end state

HV01 networking is functional and understandable enough to troubleshoot without trial-and-error.
