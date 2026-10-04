# Module 10 — Break/Fix: Replica

## Break

On HV02 locate the HTTPS Hyper-V Replica firewall rule:

~~~powershell
Get-NetFirewallRule | Where-Object DisplayName -like "*Replica*"
~~~

Disable the HTTPS listener rule selected for the lab.

## Symptom

SRV01 replication becomes unhealthy and Replica connectivity fails.

## Investigate

~~~powershell
Get-VMReplication SRV01
Measure-VMReplication SRV01
Resolve-DnsName hv02.lab.local
Test-NetConnection hv02.lab.local -Port 443
~~~

Also inspect:

- certificates;
- Replica configuration;
- VMMS;
- firewall state;
- D:\Hyper-V\Replica on HV02.

State the root cause before fixing.

## Reset

Re-enable the same firewall rule and verify that replication health returns to normal.
