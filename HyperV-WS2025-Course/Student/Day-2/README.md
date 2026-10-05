# Day 2 — Hyper-V Fundamentals

## Introduction

Day 2 turns HV01 from a prepared Windows Server into a working Hyper-V platform. The day moves from architecture to installation, then into the resources, storage, networking, and guest lifecycle that every later recovery and troubleshooting exercise depends on.

## Concepts to keep in mind

The key shift is from managing one Windows Server to managing a virtualization stack: host, hypervisor, virtual hardware, virtual networking, virtual storage, and guest operating systems.

## What you will do

Follow the sequence in order so the nested network exists before guests are created and every VM is validated before the first break/fix.

## Sequence

1. [Virtualization and Hyper-V architecture](01-Architecture.md)
2. [Install Hyper-V](02-Install-Hyper-V.md)
3. [Management tools](03-Management-Tools.md)
4. [CPU, memory and storage](04-VM-Resources-and-Storage.md)
5. [Virtual networking](05-Virtual-Networking.md)
6. [Create SRV01](06-Create-SRV01.md)
7. [Guest install and connectivity](07-Guest-Install-and-Network.md)
8. [Create DC01](08-Create-DC01.md)
9. [Break/Fix — network](09-Break-Fix-Network.md)
10. [Optional Windows 11 CLIENT01 and vTPM readiness](10-Optional-Windows-11-CLIENT01.md)
11. [Day 2 check](99-Day-2-Check.md)

## What you should observe

By the end of the day, SRV01 and DC01 should be ordinary manageable VMs on a predictable nested network rather than isolated wizard-created objects.

## Validation checkpoint

Use the Day 2 validation checklist to confirm Hyper-V, networking, guest storage, and connectivity are all in the expected baseline.

## Expected end state

HV01 is a functional nested Hyper-V host with working virtual networking and reusable guest VMs for Days 3–5.
