# Module 08 — Performance and Tuning

## Introduction

Performance troubleshooting needs a baseline before it needs a fix. This module teaches you to compare guest symptoms with VM and host evidence so tuning decisions are based on a constrained resource rather than intuition.

## Goal

Build a baseline and tune from evidence rather than guesses.

Discover Hyper-V counters:

~~~powershell
Get-Counter -ListSet *Hyper-V*
~~~

Record:

- VM state;
- host CPU;
- host available memory;
- disk latency;
- guest CPU/memory behavior.

Nested virtualization makes these training observations rather than physical-hardware benchmarks.


## Concepts to keep in mind

High utilization is not automatically a problem. Look for sustained constraint, latency, queueing, memory pressure, or contention, and always consider the nested-lab outer layer when interpreting numbers.

## Tuning method

~~~text
Measure
  -> identify constrained resource
  -> change one variable
  -> measure again
~~~

Useful checks:

~~~powershell
Get-VMProcessor SRV01
Get-VMMemory SRV01
Get-VMHardDiskDrive SRV01
Get-VMNetworkAdapter SRV01
~~~

Run a short bounded guest CPU workload and compare guest and host views before, during and after.

Do not assume that more vCPU or RAM automatically improves performance.


## What you will do

Discover Hyper-V counters, record a basic host/guest baseline, run a bounded CPU workload, compare guest and host views, and then return to baseline.


## What you should observe

The guest may report high CPU even when the host still has capacity, or the host may be constrained while one guest appears normal. The relationship between layers is the teaching point.


## Validation checkpoint

Identify which metric changed during the workload and explain what additional evidence you would need before resizing the VM.


## Expected end state

You have a reusable baseline and a measure-change-measure tuning method for Day 4.
