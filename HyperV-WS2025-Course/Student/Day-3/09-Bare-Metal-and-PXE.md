# Module 09 — Bare Metal and PXE

## Goal

Understand the deployment flow without building a full deployment infrastructure.

~~~text
UEFI/PXE
   |
DHCP
   |
PXE service
   |
WinPE / deployment environment
   |
Image / task sequence
   |
Installed OS
~~~

Discuss DHCP, network boot, UEFI considerations, Windows Deployment Services and the direction toward modern deployment tooling.

The course does not build a complete WDS/PXE lab. The objective is recognizing the components and troubleshooting dependencies.
