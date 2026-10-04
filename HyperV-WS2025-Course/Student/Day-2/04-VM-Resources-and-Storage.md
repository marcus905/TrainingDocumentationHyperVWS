# Module 04 — VM Resources and Storage

## Introduction

A VM is a collection of resource decisions, not just a name and an operating system. CPU, memory, disks, and firmware choices all affect behavior, performance, recoverability, and later troubleshooting.

## Goal

Understand virtual CPU, Dynamic Memory and VHDX choices.

Dynamic Memory concepts: Startup, Minimum, Maximum, Buffer and Weight.

Storage concepts: VHD vs VHDX, dynamically expanding, fixed and differencing disks.

Course storage standard:
~~~text
D:\Hyper-V\VMs
D:\Hyper-V\VHDX
D:\Hyper-V\ISO
D:\Hyper-V\Replica
D:\Hyper-V\Export
~~~


## Concepts to keep in mind

Virtual processors are scheduled onto host CPU resources; Dynamic Memory changes assigned memory within configured boundaries; VHDX files represent virtual block devices whose type and placement affect capacity and performance.

## Validation

Explain why adding vCPU or RAM without evidence is not automatically a performance improvement.

## What you will do

Review the resource models and compare dynamic, fixed, and differencing disks before applying the course defaults to SRV01.


## What you should observe

The VM's configured limits are not the same as physical guarantees. A VM can be correctly configured and still suffer when the host is constrained.


## Validation checkpoint

Explain Startup/Minimum/Maximum RAM and the operational differences between dynamic, fixed, and differencing virtual disks.


## Expected end state

You can justify the course VM resource choices and identify which settings would be investigated during a resource problem.
