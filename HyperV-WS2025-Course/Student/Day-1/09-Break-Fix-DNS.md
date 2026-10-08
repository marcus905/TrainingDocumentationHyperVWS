# Module 09 — Break/Fix: DNS

## Introduction

This is the first controlled incident in the course. The exercise is intentionally simple so you can focus on the troubleshooting method: prove that lower-layer IP connectivity remains healthy while name resolution fails.

## Concepts to keep in mind

DNS is layered above basic IP routing. A good investigation proves the adapter, address, gateway, and routed connectivity before concluding that DNS is the failing dependency.

## What you will do

Apply the invalid DNS setting, stop looking at the break command, test the network path layer by layer, write the root-cause statement, then restore the known-good resolver.

## Break

Inside HV01:

~~~powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 192.168.240.254
~~~

## Symptom

IP connectivity works, but hostname-based access fails.

## Investigate

~~~powershell
Get-NetIPConfiguration
Test-NetConnection 192.168.240.1
Test-NetConnection 1.1.1.1
Resolve-DnsName microsoft.com
Get-DnsClientServerAddress -InterfaceAlias "Ethernet"
~~~

State the root cause before fixing.

## Reset

~~~powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 1.1.1.1
Resolve-DnsName microsoft.com
~~~

## What you should observe

Gateway and external IP tests should continue to work while `Resolve-DnsName` fails or times out.

## Validation checkpoint

Before correcting the fault, state what evidence proves DNS is the failed layer rather than the gateway or NAT.

## Expected end state

HV01 is back on the known-good DNS server and both name resolution and HTTPS connectivity succeed.
