# Module 08 — Break/Fix: DNS

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