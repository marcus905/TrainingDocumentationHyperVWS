# Module 01 — Virtualization and Hyper-V Architecture

## Introduction

Before installing Hyper-V, connect the virtualization concepts from the instructor explanation to the components you are about to manage. Understanding root and child partitions, VMBus, and virtual devices makes later performance and failure symptoms much easier to interpret.

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


## Concepts to keep in mind

Hyper-V is a Type-1 hypervisor. The management operating system runs in the root partition, while guest VMs run in child partitions and communicate with virtualized services through VMBus and related components.

## Validation

Explain why virtualization does not remove physical CPU, RAM, storage or network limits.

## What you will do

Use the architecture diagram to trace where CPU, memory, storage, and network requests travel from a guest to the underlying host.


## What you should observe

A VM may look like a complete computer from inside the guest, but its resources are ultimately scheduled and serviced by the Hyper-V host.


## Validation checkpoint

Explain root partition, child partition, VMBus, VSP/VSC, and why Generation 2 is the course default.


## Expected end state

You can map a guest-visible resource back to the Hyper-V architecture that provides it.
