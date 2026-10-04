# Module 03 — Performance Tools

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

## Key question

Do not ask only "is utilization high?"

Ask:

> Which resource is constrained, for how long, at which layer, and what workload is responsible?

Use the Day 3 baseline when possible.
