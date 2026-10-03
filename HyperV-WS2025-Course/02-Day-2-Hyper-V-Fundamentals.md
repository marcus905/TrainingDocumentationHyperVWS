# Day 2 — Hyper-V Fundamentals

## Learning objectives
Students should be able to explain Hyper-V architecture, install the role, create/configure VMs, configure vCPU/memory/storage/networking, create virtual switches and verify connectivity.

## Module 1 — Hyper-V architecture
Topics:
- Type-1 hypervisor
- root/management partition
- child partitions
- VMBus
- Generation 1 vs Generation 2
- virtual processors
- static and Dynamic Memory
- VHD/VHDX
- virtual NICs and switches

Reference: https://learn.microsoft.com/windows-server/virtualization/hyper-v/overview

## Lab 2.1 — Install Hyper-V
~~~powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
~~~

After restart:

~~~powershell
Get-WindowsFeature Hyper-V
Get-Service vmms
Get-VMHost
~~~

## Module 2 — Virtual networking
Switch types:
- External: connects VMs to a physical network.
- Internal: connects host and attached VMs.
- Private: connects only attached VMs.

Reference: https://learn.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines

## Lab 2.2 — Create lab switches
~~~powershell
New-VMSwitch -Name "vSW-Lab" -SwitchType Internal
Get-VMSwitch
Get-NetAdapter
~~~

Also create a Private switch. External switching should be demonstrated carefully during remote delivery.

## Lab 2.3 — Create SRV01
~~~powershell
New-VM -Name "SRV01" -Generation 2 -MemoryStartupBytes 2GB -NewVHDPath "D:\Hyper-V\VHDX\SRV01.vhdx" -NewVHDSizeBytes 50GB -SwitchName "vSW-Lab"
Set-VMProcessor -VMName "SRV01" -Count 2
Set-VMMemory -VMName "SRV01" -DynamicMemoryEnabled $true -MinimumBytes 1GB -StartupBytes 2GB -MaximumBytes 4GB
~~~

Inspect:

~~~powershell
Get-VM SRV01
Get-VMProcessor SRV01
Get-VMMemory SRV01
Get-VMNetworkAdapter SRV01
Get-VMHardDiskDrive SRV01
~~~

## Lab 2.4 — Install guest OS
Attach Windows Server 2025 ISO, boot SRV01, install the guest, configure hostname/IP and validate host-to-guest connectivity.

~~~powershell
Get-VMDvdDrive SRV01
Get-VMFirmware SRV01
~~~

## Lab 2.5 — Create DC01
Suggested: Generation 2, 2 vCPU, 2 GB startup RAM, 40 GB disk, vSW-Lab.

## Break/Fix 2 — Virtual networking
Possible faults:
- wrong switch;
- disconnected vNIC;
- wrong subnet;
- wrong gateway;
- duplicate IP;
- bad DNS.

Host:

~~~powershell
Get-VMSwitch
Get-VMNetworkAdapter -VMName SRV01
~~~

Guest:

~~~powershell
ipconfig /all
Get-NetIPConfiguration
Get-NetRoute
Test-NetConnection
Resolve-DnsName
~~~

Students must locate the fault layer: guest, Hyper-V networking, host or upstream.

## End-of-day validation
- [ ] Hyper-V installed.
- [ ] VMMS operational.
- [ ] vSW-Lab created.
- [ ] SRV01 created.
- [ ] CPU and Dynamic Memory understood.
- [ ] VHDX path documented.
- [ ] Guest networking operational.
- [ ] VM configuration inspectable through PowerShell.

## Microsoft references
- https://learn.microsoft.com/windows-server/virtualization/hyper-v/overview
- https://learn.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-hyper-v
- https://learn.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines
- https://learn.microsoft.com/windows-server/virtualization/hyper-v/enable-nested-virtualization
