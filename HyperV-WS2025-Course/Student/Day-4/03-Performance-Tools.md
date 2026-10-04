# Module 03 — Performance Tools

## Introduction

Performance complaints are difficult because 'slow' is not a resource. The purpose of this module is to turn a subjective symptom into measurable CPU, memory, disk, or network evidence at both guest and host layers.

## Goal

Correlate guest, VM and host performance.

Use:

- Task Manager;
- Resource Monitor;
- Performance Monitor;
- Get-Counter;
- Hyper-V cmdlets.

Discover counter sets:

~~~powershell
Get-Counter -ListSet *Hyper-V*
~~~

Useful families include Hyper-V Hypervisor Logical Processor, Hyper-V Hypervisor Virtual Processor, Hyper-V Dynamic Memory VM, Hyper-V Virtual Storage Device and Hyper-V Virtual Network Adapter.


## Concepts to keep in mind

Baseline, duration, scope, and correlation matter. A short spike is different from sustained contention, and a guest bottleneck is different from an outer-host bottleneck in a nested lab.

## Key question

Do not ask only "is utilization high?"

Ask:

> Which resource is constrained, for how long, at which layer, and what workload is responsible?

Use the Day 3 baseline when possible.


## What you will do

Explore Task Manager, Resource Monitor, Performance Monitor, `Get-Counter`, and Hyper-V counter sets, then decide which tool answers a specific performance question fastest.


## What you should observe

The same workload can look different from the guest, VM, Hyper-V, and outer-host perspectives.


## Validation checkpoint

Given a 'VM is slow' complaint, identify the first guest metric and first host metric you would compare.


## Expected end state

You have a small performance-toolkit and a layered measurement method for the controlled incidents that follow.
