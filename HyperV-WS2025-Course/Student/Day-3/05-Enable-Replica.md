# Module 05 — Enable Hyper-V Replica

## Introduction

With the recovery host and certificate trust in place, you can now configure the actual Replica relationship. The important part is not only completing the wizard, but understanding what is replicated, how often, where it lands, and how health is measured.

## Goal

Configure HV02 as Replica server and replicate SRV01.

## Concepts to keep in mind

Replica is asynchronous. Replication frequency influences potential data loss, while disk selection and target storage determine what the recovery VM can actually reconstruct.

## What you will do

Enable HV02 as an HTTPS Replica server, enable the correct firewall rule, test protocol connectivity from HV01, then enable replication for SRV01 and start initial replication.

## Hands-on / detailed content

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

## What you should observe

`Test-VMReplicationConnection` should succeed before enabling SRV01 replication. During initial replication, health/state values will change until the target copy is synchronized.

## Validation checkpoint

Verify protocol connectivity, selected disks, target storage path, replication frequency, initial replication completion, and healthy `Get/Measure-VMReplication` output.

## Expected end state

SRV01 has a healthy asynchronous Replica relationship from HV01 to HV02.
