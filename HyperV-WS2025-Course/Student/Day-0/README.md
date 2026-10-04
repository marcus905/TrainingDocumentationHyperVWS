# Day 0 — Lab Preparation

## Introduction

Day 0 is the foundation for the entire course. The objective is not to teach Hyper-V yet; it is to remove environmental uncertainty before Day 1 by giving everyone the same outer topology, addressing plan, storage layout, and nested-virtualization capability.

## Goal

Build the outer lab that every later day depends on.


## Concepts to keep in mind

Keep the two virtualization layers distinct: the physical Windows 11 Hyper-V host runs HV01/HV02, and those servers will later become nested Hyper-V hosts themselves.

## Sequence

1. [Host prerequisites](01-Host-Prerequisites.md)
2. [Course folders and media](02-Course-Folders-and-Media.md)
3. [Outer NAT network](03-Outer-NAT-Network.md)
4. [Create HV01 and HV02](04-Create-HV01-HV02.md)
5. [Install and configure Windows Server](05-Install-and-Configure-Hosts.md)
6. [Enable nested virtualization](06-Nested-Virtualization.md)
7. [Readiness check](99-Readiness-Check.md)

## Expected end state

Physical Windows 11 Pro/Enterprise host with vSW-Course/CourseNAT (192.168.240.0/24), HV01 at 192.168.240.11 and HV02 at 192.168.240.12. Each outer VM has a 100 GB OS disk, a 200 GB raw data disk, and nested virtualization exposed. Hyper-V inside HV01/HV02 is not installed yet.

## What you will do

Work through the modules in order and stop when a readiness check fails rather than carrying an unresolved prerequisite into the course.


## What you should observe

By the end, HV01 and HV02 should look like predictable Windows Server systems on a dedicated outer management network, not ad-hoc VMs tied to the student's home LAN.


## Validation checkpoint

Use the Day 0 readiness checklist as a hard gate before beginning Day 1.
