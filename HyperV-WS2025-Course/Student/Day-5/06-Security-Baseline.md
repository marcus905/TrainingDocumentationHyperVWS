# Module 06 — Security Baseline

## Introduction

Hyper-V security is not a separate product bolted onto virtualization; it is a set of host, guest, identity, network, storage, and recovery controls that must fit the workload. This module focuses on a practical baseline rather than trying to cover every Windows security feature.

## Goal

Inspect practical Hyper-V host and VM security without turning the course into a security specialization.


## Concepts to keep in mind

Security controls should be appropriate, supportable, and recoverable. Generation 2, Secure Boot, vTPM, least privilege, patching, firewalling, protected management paths, and backup security all solve different risks.

## Host

Review:

- patching;
- least privilege;
- administrative access;
- Windows Firewall;
- management interfaces;
- event monitoring;
- VM/VHDX storage protection;
- unnecessary software;
- security-tool compatibility.

Nested virtualization is required for this lab, not a default production recommendation.

## VM

Use Generation 2 for supported modern workloads and review Secure Boot.

~~~powershell
Get-VMFirmware SRV01 | Select-Object SecureBoot
Get-VMSecurity SRV01
~~~

Discuss virtual TPM, encryption support and advanced guarded/shielded concepts.

Do not enable security features blindly; understand guest support and recovery implications.

## Backup security

Treat backup credentials, repositories, encryption keys and recovery documentation as security-sensitive assets.


## What you will do

Inspect SRV01 firmware/security settings, review host-side security posture, and identify which controls are active, optional, or inappropriate for the current lab.


## What you should observe

Some security features are visible in VM configuration while others depend on host policy, guest OS support, organizational tooling, or recovery design.


## Validation checkpoint

Identify at least three host-side controls and three VM-side controls and explain what risk each reduces.


## Expected end state

You can describe a reasonable Hyper-V security baseline without blindly enabling features or disabling security tooling to solve unrelated problems.
