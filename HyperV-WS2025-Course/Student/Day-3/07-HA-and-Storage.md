# Module 07 — High Availability and Advanced Storage

## Introduction

Recovery and availability depend heavily on the storage architecture underneath Hyper-V. This module broadens the view from local VHDX files to shared and enterprise designs, and separates migration, replication, and clustering roles.

## Goal

Compare DR, migration, HA and storage architectures.

## Concepts to keep in mind

Live Migration moves compute, Storage Migration moves files, Replica maintains a recovery copy, and Failover Clustering provides coordinated high availability. Local, SMB, SAN, CSV, and S2D designs have different failure domains and operational models.

## What you will do

Compare each technology against maintenance, DR, HA, performance, and storage-failure scenarios and map where VM configuration and disks live in each design.

## Hands-on / detailed content

| Technology | Typical use |
|---|---|
| Live Migration | Move running compute |
| Storage Migration | Move VM files |
| Hyper-V Replica | Maintain a DR copy |
| Failover Clustering | High availability |

## Storage architectures

Discuss:

- local host storage;
- SMB 3;
- SAN/block storage;
- Cluster Shared Volumes;
- Storage Spaces Direct.

Evaluate capacity, latency, throughput, resiliency, backup, growth, failure domains and operational complexity.

## Validation

Explain why the simple local storage used in this lab is useful for training but does not provide clustered high availability.

## What you should observe

A technology can solve one problem while leaving another untouched: for example, Live Migration does not create a DR copy and local storage does not provide clustered resiliency.

## Validation checkpoint

Given a customer requirement, choose an appropriate migration/recovery/storage approach and state its main limitation.

## Expected end state

You can reason about Hyper-V availability and storage designs without treating similarly named features as interchangeable.
