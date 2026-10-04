# Module 08 — Performance and Tuning

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
