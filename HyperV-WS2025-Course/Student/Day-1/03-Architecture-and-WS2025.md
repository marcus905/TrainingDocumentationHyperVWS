# Module 02 — Architecture and Windows Server 2025

## Introduction

Before configuring a server, it helps to know which layer you are changing. This architecture model becomes especially important on Day 4, when a visible application or VM symptom may actually originate in storage, networking, a service, or the host.

## Goal

Understand the layers that later troubleshooting depends on.

## Concepts to keep in mind

Think in layers: hardware/firmware, kernel and drivers, networking/storage subsystems, services, roles/features, workloads, and management tools.

## What you will do

Relate the Windows Server 2025 topics from the instructor explanation to this architecture instead of treating them as a disconnected feature list.

## Hands-on / detailed content

~~~text
Hardware / firmware
      |
Windows kernel
      |
Drivers / networking / storage
      |
Windows services
      |
Roles and features
      |
Applications/workloads
~~~

Discuss Standard vs Datacenter, Server Core vs Desktop Experience, roles, role services, features, services and management surfaces.

Course-relevant Windows Server 2025 topics include security changes, SMB, storage/NVMe, Storage Replica, ReFS-related improvements and Hotpatch concepts.

## Validation

Students can explain role vs feature, Core vs Desktop Experience, and why symptoms can originate at different layers.

## What you should observe

As Windows Server 2025 features are discussed, place each feature into an architectural or operational category rather than memorizing a list.

## Validation checkpoint

Be able to distinguish Server Core from Desktop Experience and a role from a feature or service.

## Expected end state

You have a practical mental model of Windows Server that can be reused during configuration and troubleshooting.
