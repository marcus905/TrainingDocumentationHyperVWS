# Module 01 — Virtualization and Hyper-V Architecture

## Goal

Understand the architecture before configuring it.

Hyper-V is a Type-1 hypervisor. Key concepts: root partition, child partitions, VMBus, VSP and VSC.

~~~text
Hardware
  |
Hyper-V hypervisor
  |
Root partition <-> VMBus <-> Child partitions
~~~

Discuss VM Generation 1 vs Generation 2. The course uses Generation 2.

## Validation

Explain why virtualization does not remove physical CPU, RAM, storage or network limits.