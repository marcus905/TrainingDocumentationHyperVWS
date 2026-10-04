# Module 04 — Replica Authentication

## Introduction

Standalone Hyper-V hosts cannot rely on domain Kerberos for Replica, so HTTPS certificate identity becomes part of the recovery design. This module builds a lab-only PKI so every student can reproduce that trust model locally.

## Goal

Create lab-only certificates for HTTPS Hyper-V Replica between standalone hosts.

Production environments should use an organizational trusted PKI. This course uses an isolated lab-only root CA.


## Concepts to keep in mind

Certificate-based Replica depends on name resolution, matching CN/SAN identity, a private key, appropriate EKUs, trusted issuing root, and usable certificate validation. The self-signed CA here is for isolated training only.

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


## What you will do

Create the lab root and host certificates on the physical workstation, export/copy/import them, configure name mappings, verify certificate properties, and apply the documented lab-only revocation setting.


## What you should observe

Each host should trust the same lab root but hold only its own private host certificate in LocalMachine\My. The certificate DNS name should match the FQDN used for Replica.


## Validation checkpoint

Verify name resolution, private key presence, expiry, Client/Server Authentication EKUs, trusted root, recorded thumbprints, and the lab-only revocation setting.


## Expected end state

HV01 and HV02 have mutually trusted certificate identities ready for HTTPS Replica.
