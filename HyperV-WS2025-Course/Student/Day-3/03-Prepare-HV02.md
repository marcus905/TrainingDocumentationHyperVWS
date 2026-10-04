# Module 03 — Prepare HV02

## Goal

Turn HV02 into the recovery-side Hyper-V host.

## Initialize the training disk

~~~powershell
Get-Disk
~~~

Identify the approximately 200 GB raw disk. Do not assume the disk number.

~~~powershell
Initialize-Disk -Number <DiskNumber> -PartitionStyle GPT
New-Partition -DiskNumber <DiskNumber> -UseMaximumSize -DriveLetter D
Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel "Hyper-V Data" -Confirm:$false
~~~

## Install Hyper-V

~~~powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
~~~

Create D:\Hyper-V\VMs, VHDX, Replica and Export, then set the default VM/VHDX paths.

## Recovery-side workload network

~~~powershell
New-VMSwitch -Name "vSW-Lab" -SwitchType Internal
New-NetIPAddress -InterfaceAlias "vEthernet (vSW-Lab)" -IPAddress 172.22.0.1 -PrefixLength 24
New-NetNat -Name "LabNAT" -InternalIPInterfaceAddressPrefix "172.22.0.0/24"
~~~

HV01 and HV02 each have separate isolated 172.22.0.0/24 networks.

Verify HV01 ↔ HV02 management connectivity using 192.168.240.11 and 192.168.240.12.
