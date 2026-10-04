# Module 09 — Bare Metal and PXE

## Introduction

Not every Hyper-V environment starts with manually installed hosts. This module introduces the deployment chain behind PXE and bare-metal provisioning so you can recognize where a network-boot failure might occur even though the course does not build a full deployment service.

## Goal

Understand the deployment flow without building a full deployment infrastructure.

## Concepts to keep in mind

PXE boot crosses several dependencies: firmware/UEFI, DHCP, network reachability, a PXE responder, boot files, WinPE, and the deployment/image workflow.

## What you will do

Walk through the boot chain and identify which component is responsible for address assignment, network boot response, preinstallation environment, and operating-system deployment.

## Hands-on / detailed content

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

## What you should observe

A failure before WinPE and a failure during image deployment occur at different layers and require different evidence.

## Validation checkpoint

Given a 'PXE boot failed' symptom, list the first three dependencies you would verify and why.

## Expected end state

You understand the bare-metal/PXE architecture well enough to discuss and triage it without pretending the course built a full WDS infrastructure.
