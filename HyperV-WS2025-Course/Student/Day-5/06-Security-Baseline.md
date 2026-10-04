# Module 06 — Security Baseline

## Goal

Inspect practical Hyper-V host and VM security without turning the course into a security specialization.

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
