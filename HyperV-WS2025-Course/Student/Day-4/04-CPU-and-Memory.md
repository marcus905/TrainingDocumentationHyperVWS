# Module 04 — CPU and Memory Break/Fix

## Introduction

CPU and memory problems are common places for administrators to jump directly to resizing. These two incidents are designed to make you prove that a resource is constrained before changing the VM configuration.

## Concepts to keep in mind

High CPU can be a legitimate workload, and low free memory alone does not prove harmful memory pressure. Compare demand, sustained behavior, guest evidence, and host capacity.

## What you will do

Run the bounded CPU workload and investigate it across guest/VM/host layers. Then apply the constrained-memory recipe, investigate the resulting behavior, and restore the normal memory baseline.

## CPU scenario

### Break

Inside SRV01 run a bounded CPU workload:

~~~powershell
1..400000 | ForEach-Object { [math]::Sqrt($_) } | Out-Null
~~~

Repeat if needed while observing Task Manager.

### Investigate

Inside SRV01:

~~~powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Name,Id,CPU
Get-Counter '\Processor(_Total)\% Processor Time' -SampleInterval 2 -MaxSamples 10
~~~

On HV01:

~~~powershell
Get-VMProcessor SRV01
Get-VM SRV01 | Select-Object Name,State,CPUUsage
~~~

Prove whether the issue is guest workload, VM sizing, host contention or only a transient spike.

## Memory scenario

### Break

On HV01:

~~~powershell
Stop-VM SRV01
Set-VMMemory -VMName SRV01 -DynamicMemoryEnabled $true -MinimumBytes 512MB -StartupBytes 1GB -MaximumBytes 1GB
Start-VM SRV01
~~~

### Investigate

~~~powershell
Get-VMMemory SRV01
Get-VM SRV01 | Select-Object Name,MemoryAssigned,MemoryDemand,MemoryStatus
~~~

Inside SRV01 sample available memory and Pages/sec.

### Reset

~~~powershell
Stop-VM SRV01
Set-VMMemory -VMName SRV01 -DynamicMemoryEnabled $true -MinimumBytes 1GB -StartupBytes 2GB -MaximumBytes 4GB
Start-VM SRV01
~~~

## What you should observe

The CPU scenario should show a clear workload-correlated rise. The memory scenario should show SRV01 operating with a much tighter configured ceiling than its normal baseline.

## Validation checkpoint

For each incident, state whether the root cause is workload, VM configuration, or host contention and cite the evidence used.

## Expected end state

SRV01 is restored to 1 GB minimum, 2 GB startup, and 4 GB maximum Dynamic Memory with no artificial CPU workload remaining.
