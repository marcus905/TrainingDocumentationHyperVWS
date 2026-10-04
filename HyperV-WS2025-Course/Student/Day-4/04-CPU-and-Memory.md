# Module 04 — CPU and Memory Break/Fix

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
