# Module 03 — Migration Planning

## Introduction

A virtualization migration is not mainly a disk-conversion exercise. The hard part is discovering dependencies, defining acceptable downtime and recovery, and knowing how to validate or roll back the workload after the platform changes.

## Goal

Plan migration before touching the source workload.


## Concepts to keep in mind

Inventory, compatibility, dependency mapping, cutover, validation, and rollback are parts of one migration plan. A VM that boots successfully may still have broken application, network, licensing, monitoring, backup, or security dependencies.

## Inventory

Record:

- VM name/owner/purpose;
- guest OS;
- vCPU/memory;
- disks and formats;
- firmware/boot mode;
- network/VLAN/IP/DNS;
- application dependencies;
- backup/monitoring/security agents;
- RPO/RTO;
- acceptable downtime.

## Compatibility

Check guest support, BIOS/UEFI requirements, disk conversion, licensing dependencies, static MAC requirements, VLAN/IP recreation, source hypervisor tools/drivers, Secure Boot and time behavior.

## Dependency map

A VM can depend on DNS, databases, file shares, certificates, firewalls, monitoring and backup.

## Cutover

~~~text
Pre-checks
 -> quiesce/shutdown
 -> convert/move
 -> Hyper-V configuration
 -> network cutover
 -> application validation
 -> rollback decision
~~~

## Validation

A successful boot is not enough. Validate application function, authentication, networking, disk visibility, time, backup, monitoring, security tooling, performance and recovery.


## What you will do

Build a migration inventory for a sample VM, identify compatibility questions, draw its dependencies, then define cutover checkpoints and a rollback trigger.


## What you should observe

The inventory should reveal decisions that cannot be solved by a VHDX conversion alone—for example VLAN mapping, static IP/DNS, certificates, agents, application licensing, or recovery expectations.


## Validation checkpoint

State the exact conditions that would make you continue cutover versus trigger rollback.


## Expected end state

You can describe a migration plan that includes workload dependencies, validation, and recovery rather than only VM conversion.
