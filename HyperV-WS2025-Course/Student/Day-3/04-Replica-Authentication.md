# Module 04 — Replica Authentication

## Goal

Create lab-only certificates for HTTPS Hyper-V Replica between standalone hosts.

Production environments should use an organizational trusted PKI. This course uses an isolated lab-only root CA.

## Name resolution

HV01 must resolve hv02.lab.local to 192.168.240.12. HV02 must resolve hv01.lab.local to 192.168.240.11. Use hosts-file entries if required.

## Certificate workflow

On the physical Windows 11 host:

1. Create the lab root CA.
2. Create HV01 and HV02 certificates with matching DNS names.
3. Include Client Authentication and Server Authentication EKUs.
4. Export the root as CER.
5. Export each host certificate as PFX.
6. Enable Guest Service Interface on HV01/HV02.
7. Copy the root and appropriate PFX into each VM.

On HV01/HV02, import the root into LocalMachine\Root and the local host PFX into LocalMachine\My.

Verify:

~~~powershell
Get-ChildItem Cert:\LocalMachine\My |
    Where-Object Subject -like "*lab.local*" |
    Select-Object Subject,DnsNameList,Thumbprint,NotAfter,HasPrivateKey,EnhancedKeyUsageList
~~~

Record both thumbprints.

## Lab-only revocation setting

The self-signed course CA has no reachable CRL. Configure the documented lab-only DisableCertRevocationCheck setting on both hosts.

This weakens validation and is **not** a production recommendation.

Use the canonical Day 3 guide for the complete certificate-creation commands.
