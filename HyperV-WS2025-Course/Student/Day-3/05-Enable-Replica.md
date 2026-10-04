# Module 05 — Enable Hyper-V Replica

## Goal

Configure HV02 as Replica server and replicate SRV01.

On HV02, open Hyper-V Settings > Replication Configuration:

- enable this computer as a Replica server;
- select certificate-based authentication (HTTPS);
- choose the HV02 certificate;
- authorize HV01;
- use the course Replica path.

Find and enable the HTTPS Replica firewall rule:

~~~powershell
Get-NetFirewallRule | Where-Object DisplayName -like "*Replica*"
~~~

From HV01 test the connection:

~~~powershell
Test-VMReplicationConnection -ReplicaServerName "hv02.lab.local" -ReplicaServerPort 443 -AuthenticationType Certificate -CertificateThumbprint "<HV01 certificate thumbprint>"
~~~

Enable replication for SRV01 through Hyper-V Manager. Review disk selection, compression, frequency, recovery history and initial replication.

Validate:

~~~powershell
Get-VMReplication -VMName SRV01
Measure-VMReplication -VMName SRV01
~~~
