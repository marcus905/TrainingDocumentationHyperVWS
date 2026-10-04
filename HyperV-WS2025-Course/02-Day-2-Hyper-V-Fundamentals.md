# Day 2 — Hyper-V Fundamentals

## Learning objectives

By the end of Day 2, students should be able to:

- explain the purpose and basic concepts of server virtualization;
- describe the main components of Hyper-V architecture;
- explain the role of the root partition, child partitions, VMBus, VSPs and VSCs;
- install and validate the Hyper-V role on Windows Server 2025;
- use Hyper-V Manager and PowerShell to manage the host;
- create and configure Generation 2 virtual machines;
- configure virtual CPU and memory;
- understand VHDX storage basics;
- create and use Hyper-V virtual switches;
- configure an Internal switch with NAT for the nested course lab;
- verify connectivity between the Hyper-V host, nested VM and external network;
- recognize high-level operational differences between Hyper-V and other hypervisors.

---

# Module 1 — Virtualization fundamentals

## Why virtualization?

Virtualization allows multiple isolated operating systems to share the same physical server hardware.

Instead of dedicating one physical server to each workload, a hypervisor presents virtualized CPU, memory, storage and networking resources to virtual machines.

~~~text
Physical Hardware
       |
    Hypervisor
       |
+------+------+------+
|      |      |
VM1    VM2    VM3
~~~

### Main benefits

Virtualization can provide:

- improved hardware utilization;
- workload isolation;
- faster server provisioning;
- easier testing and development;
- simpler recovery and replication scenarios;
- standardized virtual hardware;
- workload mobility between compatible hosts.

Virtualization does not remove physical resource limits. All VMs still ultimately share the host CPU, memory, storage and network resources.

---

# Module 2 — Hyper-V architecture

Hyper-V is Microsoft's Type-1 hypervisor technology. The hypervisor runs directly on the hardware and creates isolated execution environments called partitions. The Windows management operating system runs in the **root partition**, while virtual machines run in **child partitions**.

Microsoft reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/architecture

## Simplified architecture

~~~text
+------------------------------------------------------+
|                 Management Tools                     |
|       Hyper-V Manager / PowerShell / WAC             |
+--------------------------+---------------------------+
                           |
+--------------------------v---------------------------+
|                    Root Partition                    |
| Windows Server 2025                                   |
| VMMS / virtualization stack / device drivers          |
| VSPs - Virtualization Service Providers               |
+--------------------------+---------------------------+
                           |
                         VMBus
             +-------------+-------------+
             |                           |
+------------v------------+  +-----------v------------+
|     Child Partition     |  |    Child Partition     |
|        SRV01            |  |         DC01           |
| Guest OS                |  | Guest OS               |
| VSCs                    |  | VSCs                   |
+-------------------------+  +------------------------+
             |
+------------v-----------------------------------------+
|                    Hypervisor                        |
+------------------------------------------------------+
|                Physical Hardware                     |
| CPU / Memory / Storage / Network                     |
+------------------------------------------------------+
~~~

## Root partition

The root partition:

- runs the Windows management operating system;
- owns the physical device drivers;
- hosts the virtualization management stack;
- creates and manages child partitions;
- provides access to physical I/O resources on behalf of VMs.

The root partition is not simply another VM. It has a privileged management role in Hyper-V architecture.

## Child partitions

Virtual machines run in child partitions.

Child partitions:

- have a virtualized view of processors and memory;
- do not directly control the physical hardware;
- access virtual devices through the Hyper-V virtualization stack;
- remain isolated from other child partitions.

## VMBus

The **VMBus** is a high-speed logical communication channel between the root partition and child partitions.

It is used by synthetic Hyper-V devices to exchange I/O efficiently between the guest and host.

## VSP and VSC

Hyper-V uses:

- **VSP — Virtualization Service Provider** in the root partition;
- **VSC — Virtualization Service Client** in the guest.

The VSC communicates with the corresponding VSP through VMBus.

This design avoids emulating every hardware operation and provides more efficient I/O for supported guest operating systems.

---

# Module 3 — Hyper-V terminology and VM generations

Microsoft reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/features-terminology

## Generation 1 vs Generation 2

Hyper-V supports two VM generations. The generation is selected when the VM is created and cannot be changed later.

Microsoft recommends Generation 2 for most modern workloads.

| Feature | Generation 1 | Generation 2 |
|---|---|---|
| Firmware | Legacy BIOS | UEFI |
| Secure Boot | No | Yes |
| Legacy device compatibility | Higher | Reduced |
| Modern Windows workloads | Supported | Preferred |
| Boot from SCSI | No | Yes |
| Recommended for new WS2025 VMs | Only when specifically required | Yes |

For this course, all newly created virtual machines use **Generation 2** unless an exercise explicitly states otherwise.

Reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v

---

# Module 4 — Install and validate the Hyper-V role

Installing Hyper-V on Windows Server does more than add a management console. It enables the virtualization platform and adds the Windows components required to create and manage virtual machines.

Microsoft reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-hyper-v

## What the Hyper-V role adds

Installing the role provides or enables components such as:

- the Hyper-V hypervisor;
- the Hyper-V Virtual Machine Management service, VMMS;
- the virtualization management stack;
- Hyper-V virtual networking components;
- the Hyper-V PowerShell module;
- Hyper-V Manager when management tools are included;
- support for creating and managing child partitions.

The exact set of installed components depends on whether management tools are included and on the Windows Server installation option.

## Why the restart matters

The Hyper-V hypervisor must be loaded during system startup.

That is why installing the role normally requires a restart before the server can operate as a Hyper-V host.

Conceptually:

~~~text
Before restart

Windows Server
     |
Hyper-V role files present

After restart

Windows Server management OS
     |
Hyper-V virtualization stack
     |
Hypervisor
     |
Physical hardware
~~~

## Installed does not mean ready

A successful role installation only confirms that the Hyper-V components are present.

Before treating the server as ready for workloads, validate:

- VMMS is running;
- Hyper-V Manager or PowerShell can communicate with the host;
- default VM and VHDX paths are appropriate;
- required virtual switches exist;
- storage has adequate capacity;
- nested virtualization is available in this course environment.

The rest of Day 2 performs those readiness checks step by step.

# Lab 2.1 — Install Hyper-V on HV01

## Objective

Install the Hyper-V role and management tools on the nested Windows Server 2025 host HV01.

Before continuing, verify that nested virtualization was enabled during Day 0.

## Step 1 — Verify current role state

~~~powershell
Get-WindowsFeature Hyper-V
~~~

Expected before installation:

~~~text
Install State : Available
~~~

## Step 2 — Preview the installation

Apply the WhatIf pattern from Day 1:

~~~powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -WhatIf
~~~

Confirm that the preview includes Hyper-V and the relevant management components.

## Step 3 — Install the role

~~~powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
~~~

The server restarts automatically if required.

## Step 4 — Validate after restart

~~~powershell
Get-WindowsFeature Hyper-V
Get-Service vmms
Get-VMHost
~~~

Expected:

- Hyper-V reports Installed;
- VMMS reports Running;
- Get-VMHost returns HV01 configuration.

Review fields such as:

- ComputerName;
- VirtualMachinePath;
- VirtualHardDiskPath;
- LogicalProcessorCount;
- MemoryCapacity;
- EnableEnhancedSessionMode.

## Step 5 — Open Hyper-V Manager

Launch **Hyper-V Manager** and confirm that HV01 appears as the connected host.

## Step 6 — Configure default Hyper-V storage paths

Day 1 prepared dedicated Hyper-V folders. Configure the host so new VMs and virtual disks use those locations by default.

~~~powershell
Set-VMHost -VirtualMachinePath "D:\Hyper-V\VMs" -VirtualHardDiskPath "D:\Hyper-V\VHDX"
~~~

Verify:

~~~powershell
Get-VMHost | Select-Object VirtualMachinePath,VirtualHardDiskPath
~~~

Expected values:

~~~text
VirtualMachinePath  : D:\Hyper-V\VMs
VirtualHardDiskPath : D:\Hyper-V\VHDX
~~~

### Why this matters

A consistent default path reduces accidental placement of VM configuration and VHDX files on the operating-system volume and makes later administration easier.

## Validation checkpoint

- [ ] Hyper-V role reports Installed.
- [ ] VMMS is Running.
- [ ] Hyper-V Manager opens successfully.
- [ ] Get-VMHost returns HV01 configuration.
- [ ] Default VM path is D:\Hyper-V\VMs.
- [ ] Default VHDX path is D:\Hyper-V\VHDX.

---

# Module 5 — Hyper-V management tools

Hyper-V can be administered locally or remotely using several different tools.

The correct tool depends on the scale of the environment, whether the host is standalone or clustered, and whether the task is interactive, repetitive or automated.

## Local vs remote management

A Hyper-V host does not need to be administered only from its local console.

Common approaches include:

- local Hyper-V Manager on the host;
- Hyper-V Manager from another Windows computer;
- PowerShell locally or remotely;
- Windows Admin Center;
- Failover Cluster Manager for clustered environments;
- System Center Virtual Machine Manager for larger managed fabrics.

Remote management becomes especially important when production hosts run Server Core or when administrators manage many hosts from a separate workstation.

## Hyper-V Manager

Hyper-V Manager is the main graphical administration tool used in this course.

It is suitable for:

- creating and editing VMs;
- starting and stopping VMs;
- connecting to VM consoles;
- managing checkpoints;
- creating virtual switches;
- inspecting virtual disks and network adapters;
- connecting to additional Hyper-V hosts.

Hyper-V Manager is excellent for understanding configuration visually, but repetitive operations are often faster and more consistent in PowerShell.

## PowerShell Hyper-V module

The Hyper-V module exposes the same platform through cmdlets.

Examples:

~~~powershell
Get-VM
Get-VMHost
Get-VMSwitch
Get-VMNetworkAdapter
Get-VMHardDiskDrive
~~~

PowerShell is especially useful when:

- querying many VMs;
- standardizing configuration;
- repeating the same operation;
- collecting troubleshooting information;
- scripting administrative workflows.

## Windows Admin Center

Windows Admin Center provides browser-based management for Windows Server infrastructure.

It can manage Windows Server and Hyper-V hosts without requiring a full local desktop session on the target server.

It is useful for:

- remote server administration;
- consolidated host views;
- Hyper-V management;
- storage, networking and event inspection.

Windows Admin Center is introduced conceptually here; the course does not depend on it for the core labs.

Reference:

https://learn.microsoft.com/windows-server/manage/windows-admin-center/overview

## Failover Cluster Manager

Failover Cluster Manager becomes relevant when Hyper-V hosts participate in a Windows Failover Cluster.

It is used for:

- clustered roles;
- clustered VMs;
- ownership and failover;
- Cluster Shared Volumes;
- cluster health and dependencies.

High availability is introduced on Day 3.

## System Center Virtual Machine Manager

System Center Virtual Machine Manager, SCVMM, is designed for larger enterprise virtualization estates.

It adds capabilities such as:

- centralized fabric management;
- host groups;
- templates;
- library resources;
- placement;
- larger-scale VM lifecycle management.

SCVMM is outside the hands-on scope of this course, but students should recognize where it fits.

## Which tool for which task?

| Task | Typical tool |
|---|---|
| Create one VM interactively | Hyper-V Manager |
| Inspect one host visually | Hyper-V Manager / Windows Admin Center |
| Query many VMs | PowerShell |
| Apply repeatable VM settings | PowerShell |
| Manage clustered Hyper-V roles | Failover Cluster Manager |
| Browser-based remote administration | Windows Admin Center |
| Manage a large virtualization fabric | SCVMM |

## Remote Hyper-V Manager concept

Hyper-V Manager can connect to another host using **Connect to Server**.

In a production environment, remote connectivity also depends on:

- authentication;
- firewall rules;
- name resolution;
- administrative permissions;
- management protocol configuration.

Those dependencies become useful troubleshooting layers later in the course.

## Exercise — Explore the Hyper-V management surface

List Hyper-V commands:

~~~powershell
Get-Command -Module Hyper-V
~~~

Find VM-related commands:

~~~powershell
Get-Command -Module Hyper-V *VM*
~~~

Inspect the host:

~~~powershell
Get-VMHost
~~~

Inspect current VMs and switches:

~~~powershell
Get-VM
Get-VMSwitch
~~~

### Validation

Students should be able to identify which management tool they would choose for:

1. creating a single VM;
2. querying 30 VMs;
3. managing a clustered VM;
4. managing a remote server from a browser;
5. applying the same VM setting repeatedly.

---

# Module 6 — Virtual CPU and memory

## Virtual processors

A VM is assigned one or more **virtual processors (vCPUs)**.

The hypervisor schedules virtual processors onto the host's logical processors.

Assigning more vCPUs does not automatically make a VM faster. Oversizing can increase scheduling contention and may reduce overall efficiency.

For this course, start small and increase resources only when evidence justifies it.

## Static memory

With static memory, the VM receives a fixed amount of RAM while running.

~~~text
Startup RAM: 4 GB
Dynamic Memory: Disabled
~~~

## Dynamic Memory

Dynamic Memory allows Hyper-V to adjust the memory assigned to a running VM according to workload demand and configured limits.

Microsoft reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/dynamic-memory

Important settings:

- **Startup RAM** — memory available during VM startup;
- **Minimum RAM** — lower boundary Hyper-V can reclaim toward after startup;
- **Maximum RAM** — upper boundary Hyper-V can allocate;
- **Memory Buffer** — additional memory target above current demand;
- **Memory Weight** — relative priority when the host is under memory pressure.

Dynamic Memory can improve consolidation, but it is not appropriate for every workload. Application support requirements must always be checked.

---

# Module 7 — Hyper-V storage fundamentals

Hyper-V virtual machines normally use virtual hard disks.

## VHD and VHDX

**VHDX** is the preferred format for modern Hyper-V workloads.

For this course, use VHDX unless a compatibility exercise explicitly requires VHD.

## Common virtual disk types

### Dynamically expanding

The file grows as data is written, up to its configured maximum size.

Advantages:

- efficient initial use of host storage;
- quick to create.

Considerations:

- the host must still have enough free space as the disk grows;
- uncontrolled growth can create capacity problems.

### Fixed size

The full configured capacity is allocated when the disk is created.

Advantages:

- predictable space allocation;
- useful in some performance-sensitive or operationally controlled scenarios.

Considerations:

- takes the full amount of host storage immediately;
- creation can take longer.

### Differencing disks

A differencing disk stores changes relative to a parent virtual disk.

They are useful in specific deployment and lab scenarios, but introduce parent-child dependencies and should be managed carefully.

Checkpoint disks are covered in detail on Day 3.

## Course storage standard

~~~text
D:\Hyper-V
|
+-- VMs
+-- VHDX
+-- ISO
+-- Replica
~~~

For Day 2:

- VM configuration files go under D:\Hyper-V\VMs;
- virtual hard disks go under D:\Hyper-V\VHDX;
- ISO media goes under D:\Hyper-V\ISO.

---

# Module 8 — Hyper-V virtual networking

A Hyper-V virtual switch is a software-based Layer 2 Ethernet switch implemented by Hyper-V.

Microsoft reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines

## External switch

~~~text
VM
 |
Hyper-V External vSwitch
 |
Physical NIC
 |
Physical Network
~~~

An External switch allows VMs to communicate with systems outside the host through a physical network adapter.

In a remote nested-lab environment, creating an External switch inside HV01 introduces additional dependencies on the outer virtualization layer.

For that reason, **External switching is demonstrated conceptually, but is not the standard nested-lab path for this course**.

## Internal switch

~~~text
          HV01
           |
     vEthernet adapter
           |
       Internal
       vSwitch
       /     \
    SRV01   DC01
~~~

An Internal switch connects the Hyper-V host and VMs attached to the switch. It does not directly bridge those VMs to the physical network.

## Private switch

~~~text
SRV01 ---- Private vSwitch ---- DC01
~~~

A Private switch connects VMs to each other but does not provide host connectivity.

## Lab networking design

The course uses an **Internal switch plus Windows NAT**.

~~~text
Outer physical/virtual network
            |
           HV01
     Windows NAT function
            |
      172.22.0.1/24
            |
        vSW-Lab
       /       \
   DC01        SRV01
172.22.0.10  172.22.0.20
~~~

This design gives the nested VMs:

- connectivity to HV01;
- connectivity to each other;
- outbound connectivity through HV01;
- a predictable course subnet.

Windows NAT does not provide DHCP in this design, so nested VMs use static addresses.

---

# Lab 2.2 — Create the course virtual network

## Objective

Create the Internal vSwitch and NAT configuration used by the nested lab for the remainder of the course.

## Addressing plan

~~~text
Network:  172.22.0.0/24
Gateway:  172.22.0.1

DC01:     172.22.0.10
SRV01:    172.22.0.20
CLIENT01: 172.22.0.100
~~~

The host-side Internal switch adapter uses 172.22.0.1.

## Step 1 — Check existing virtual switches

~~~powershell
Get-VMSwitch
~~~

Confirm that no switch named vSW-Lab already exists.

## Step 2 — Create the Internal switch

~~~powershell
New-VMSwitch -Name "vSW-Lab" -SwitchType Internal
Get-VMSwitch -Name "vSW-Lab"
~~~

Expected:

~~~text
SwitchType : Internal
~~~

## Step 3 — Locate the host-side virtual adapter

~~~powershell
Get-NetAdapter | Where-Object Name -like "*vSW-Lab*"
~~~

Hyper-V creates a host adapter typically named:

~~~text
vEthernet (vSW-Lab)
~~~

## Step 4 — Assign the gateway address

~~~powershell
New-NetIPAddress -InterfaceAlias "vEthernet (vSW-Lab)" -IPAddress 172.22.0.1 -PrefixLength 24
Get-NetIPAddress -InterfaceAlias "vEthernet (vSW-Lab)" -AddressFamily IPv4
~~~

## Step 5 — Create NAT

~~~powershell
Get-NetNat
New-NetNat -Name "LabNAT" -InternalIPInterfaceAddressPrefix "172.22.0.0/24"
Get-NetNat -Name "LabNAT"
~~~

Only create the NAT object once. If LabNAT already exists, inspect it before changing anything.

## Step 6 — Create a Private switch for comparison

~~~powershell
New-VMSwitch -Name "vSW-Private" -SwitchType Private
Get-VMSwitch | Select-Object Name,SwitchType
~~~

## Validation checkpoint

- [ ] vSW-Lab exists and is Internal.
- [ ] vEthernet (vSW-Lab) has 172.22.0.1/24.
- [ ] LabNAT exists for 172.22.0.0/24.
- [ ] vSW-Private exists.
- [ ] Students can explain Internal vs Private.

---

# Module 9 — Create and configure a virtual machine

Creating a VM is not only a wizard operation. A virtual machine is a collection of configuration choices that together define its virtual hardware, firmware, storage, networking and runtime behavior.

Microsoft reference:

https://learn.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v

## VM configuration model

~~~text
Virtual Machine
|
+-- Firmware / Generation
+-- Virtual processors
+-- Memory
+-- Virtual disks
+-- Virtual network adapters
+-- Security settings
+-- Integration services
+-- Checkpoint settings
~~~

The VM configuration is separate from the guest operating system stored inside its VHDX files.

That distinction matters operationally because:

- a VM can exist without an installed operating system;
- VHDX files can be attached or detached independently;
- VM configuration can point to one or more virtual disks;
- deleting a VM configuration does not always imply deleting every associated VHDX;
- troubleshooting may involve either VM configuration or guest OS state.

## Design before creation

Before creating a VM, decide:

~~~text
Workload requirements
        |
Generation
        |
CPU / Memory
        |
Storage
        |
Networking
        |
Security / Secure Boot
~~~

For this course, the standard defaults are:

- Generation 2;
- UEFI;
- Secure Boot enabled for supported Windows guests;
- modest vCPU allocation;
- Dynamic Memory where appropriate;
- VHDX storage;
- vSW-Lab networking.

## Settings that may require the VM to be off

Some VM settings can be changed while the VM is running, while others require the VM to be stopped.

Students should develop the habit of checking the current VM state before applying configuration changes.

Use:

~~~powershell
Get-VM SRV01
~~~

and inspect:

~~~text
State
~~~

Examples of settings commonly changed while the VM is off include certain firmware and hardware configuration options.

The exact online/offline requirements vary by setting, so use PowerShell help or Hyper-V Manager to confirm before changing production workloads.

## VM configuration vs guest configuration

It is useful to separate:

~~~text
Hyper-V host view
- vCPU
- memory
- VHDX
- vNIC
- firmware
- vSwitch

Guest OS view
- hostname
- IP address
- filesystem
- services
- applications
~~~

A problem visible inside the guest may still originate from the Hyper-V configuration layer.

# Lab 2.3 — Create SRV01

## Objective

Create a Generation 2 Windows Server 2025 VM and configure its virtual CPU, Dynamic Memory, storage, firmware and networking.

## VM specification

~~~text
Name:            SRV01
Generation:      2
vCPU:            2
Startup RAM:     2 GB
Minimum RAM:     1 GB
Maximum RAM:     4 GB
OS disk:         50 GB VHDX
Network:         vSW-Lab
Guest IP:        172.22.0.20/24
Gateway:         172.22.0.1
~~~

## Step 1 — Create with Hyper-V Manager

Use **New > Virtual Machine**.

Configure:

1. Name: SRV01.
2. Generation: Generation 2.
3. Startup memory: 2048 MB.
4. Connect to vSW-Lab.
5. Create a 50 GB VHDX under D:\Hyper-V\VHDX.
6. Attach the Windows Server 2025 ISO from D:\Hyper-V\ISO.
7. Finish the wizard.

Then open VM Settings and configure 2 virtual processors.

## Step 2 — Review the PowerShell equivalent

~~~powershell
New-VM -Name "SRV01" -Generation 2 -MemoryStartupBytes 2GB -Path "D:\Hyper-V\VMs" -NewVHDPath "D:\Hyper-V\VHDX\SRV01.vhdx" -NewVHDSizeBytes 50GB -SwitchName "vSW-Lab"
Set-VMProcessor -VMName "SRV01" -Count 2
Set-VMMemory -VMName "SRV01" -DynamicMemoryEnabled $true -MinimumBytes 1GB -StartupBytes 2GB -MaximumBytes 4GB
~~~

## Step 3 — Inspect VM configuration

~~~powershell
Get-VM SRV01
Get-VMProcessor SRV01
Get-VMMemory SRV01
Get-VMNetworkAdapter SRV01
Get-VMHardDiskDrive SRV01
Get-VMFirmware SRV01
~~~

Students should identify:

- VM state;
- vCPU count;
- memory limits;
- connected switch;
- VHDX path;
- firmware configuration.

## Step 4 — Confirm Secure Boot

~~~powershell
Get-VMFirmware SRV01 | Select-Object SecureBoot
~~~

Do not disable Secure Boot for a supported Windows Server 2025 guest.

---

# Lab 2.4 — VM lifecycle operations

## Objective

Practice the basic Hyper-V VM states used during day-to-day administration.

Start by checking SRV01:

~~~powershell
Get-VM SRV01
~~~

If the VM is not running:

~~~powershell
Start-VM SRV01
~~~

Verify:

~~~powershell
Get-VM SRV01
~~~

## Save the VM

~~~powershell
Save-VM SRV01
~~~

Check state:

~~~powershell
Get-VM SRV01
~~~

Expected state:

~~~text
Saved
~~~

## Resume the VM

~~~powershell
Resume-VM SRV01
~~~

Verify:

~~~powershell
Get-VM SRV01
~~~

Expected state:

~~~text
Running
~~~

## Other lifecycle commands

Useful commands include:

~~~powershell
Stop-VM SRV01
Restart-VM SRV01
Start-VM SRV01
~~~

Do not force-stop the VM unless instructed.

### State discussion

Students should recognize the difference between:

- **Running** — guest is actively executing;
- **Off** — guest is powered off;
- **Saved** — VM memory/device state is persisted to disk;
- **Paused** — execution is temporarily suspended.

### Validation checkpoint

- [ ] Student can start SRV01.
- [ ] Student can save and resume SRV01.
- [ ] Student can identify current VM state with Get-VM.

---

# Lab 2.5 — Install Windows Server 2025 in SRV01

## Step 1 — Verify ISO attachment

~~~powershell
Get-VMDvdDrive SRV01
~~~

If the ISO has not been attached:

~~~powershell
Add-VMDvdDrive -VMName "SRV01" -Path "D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso"
~~~

If your ISO filename differs, use the actual local path.

## Step 2 — Set DVD as first boot device if required

~~~powershell
$DVD = Get-VMDvdDrive SRV01
Set-VMFirmware -VMName SRV01 -FirstBootDevice $DVD
~~~

## Step 3 — Start and connect

~~~powershell
Start-VM SRV01
vmconnect.exe localhost SRV01
~~~

Install Windows Server 2025 with Desktop Experience.

After installation:

1. set the Administrator password;
2. sign in;
3. rename the guest to SRV01 if necessary;
4. restart if required.

---

# Lab 2.6 — Add a data disk to SRV01

## Objective

Create an additional VHDX and attach it to an existing VM.

This demonstrates that a VM can have multiple virtual disks and that storage can be added independently of the OS disk.

## Step 1 — Create the VHDX

On HV01:

~~~powershell
New-VHD -Path "D:\Hyper-V\VHDX\SRV01-DATA.vhdx" -SizeBytes 10GB -Dynamic
~~~

Inspect it:

~~~powershell
Get-VHD "D:\Hyper-V\VHDX\SRV01-DATA.vhdx"
~~~

Review:

- VhdType;
- FileSize;
- Size;
- Path.

## Step 2 — Attach the disk to SRV01

~~~powershell
Add-VMHardDiskDrive -VMName "SRV01" -Path "D:\Hyper-V\VHDX\SRV01-DATA.vhdx"
~~~

Verify from the host:

~~~powershell
Get-VMHardDiskDrive SRV01
~~~

Students should see both the OS disk and SRV01-DATA.vhdx.

## Step 3 — Verify inside the guest

Inside SRV01:

~~~powershell
Get-Disk
~~~

A new offline or uninitialized disk should appear.

Do not initialize or format it during this exercise. Disk initialization was already covered on Day 1.

### Teaching point

Hyper-V virtual storage and guest storage are separate layers:

- the host owns and attaches the VHDX file;
- the guest sees the attached VHDX as a virtual disk.

### Validation checkpoint

- [ ] SRV01-DATA.vhdx exists.
- [ ] The VHDX is attached to SRV01.
- [ ] Get-VMHardDiskDrive shows two disks.
- [ ] Get-Disk inside SRV01 shows the additional disk.

---

# Lab 2.7 — Configure SRV01 networking

## Step 1 — Inspect the guest adapter

Inside SRV01:

~~~powershell
Get-NetAdapter
Get-NetIPConfiguration
~~~

## Step 2 — Configure static IPv4

Assuming the adapter is named Ethernet:

~~~powershell
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 172.22.0.20 -PrefixLength 24 -DefaultGateway 172.22.0.1
~~~

For Day 2, use a DNS server reachable through the lab NAT as instructed by the trainer.

Example:

~~~powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 1.1.1.1
~~~

Later, when DC01 becomes the lab DNS server, guest DNS configuration can be changed to use DC01.

## Step 3 — Verify guest-to-host connectivity

~~~powershell
Test-NetConnection 172.22.0.1
~~~

## Step 4 — Verify external connectivity

~~~powershell
Test-NetConnection 1.1.1.1
Resolve-DnsName microsoft.com
Test-NetConnection microsoft.com -Port 443
~~~

## Step 5 — Inspect from HV01

On HV01:

~~~powershell
Get-VMNetworkAdapter -VMName SRV01
Test-NetConnection 172.22.0.20
~~~

## Connectivity matrix

| Source | Destination | Expected |
|---|---|---|
| HV01 | SRV01 | Reachable |
| SRV01 | HV01 / 172.22.0.1 | Reachable |
| SRV01 | Internet IP | Reachable through NAT |
| SRV01 | Public DNS name | Reachable if DNS is configured |

---

# Lab 2.8 — Create DC01

Create a second Generation 2 VM.

Suggested configuration:

~~~text
Name:        DC01
vCPU:        2
Startup RAM: 2 GB
VHDX:        40 GB
Switch:      vSW-Lab
Guest IP:    172.22.0.10/24
Gateway:     172.22.0.1
~~~

At this stage DC01 is only a Windows Server VM.

Do not install AD DS or DNS unless the instructor explicitly chooses to extend the lab.

Verify:

~~~powershell
Get-VM DC01
Get-VMNetworkAdapter DC01
Get-VMHardDiskDrive DC01
~~~

---

# Module 10 — Operational comparison with other hypervisors

The goal is not to teach another virtualization platform, but to help administrators map familiar concepts.

| General virtualization concept | Hyper-V terminology |
|---|---|
| Hypervisor host | Hyper-V host |
| VM configuration | Virtual machine |
| Virtual CPU | Virtual processor |
| Virtual disk | VHDX |
| Virtual switch | Hyper-V Virtual Switch |
| Snapshot-style point in time | Checkpoint |
| Guest integration tools | Hyper-V Integration Services |
| VM replication | Hyper-V Replica |
| Host-to-host VM movement | Live Migration |

### Operational differences to keep in mind

- Hyper-V management is strongly integrated with Windows Server, PowerShell and Windows security.
- Hyper-V networking uses Windows networking constructs and Hyper-V Virtual Switches.
- Hyper-V storage commonly uses VHDX files stored on local disks, SMB shares, CSVs or other supported Windows storage architectures.
- Generation 2 VMs use UEFI and Secure Boot rather than legacy BIOS.
- Similar terminology across hypervisors does not always imply identical architecture or behavior.

A deeper VMware-to-Hyper-V mapping is covered on Day 5.

---

# Break/Fix 2 — Virtual network failure

## Scenario

SRV01 was working earlier, but now it cannot reach the Internet and cannot reach the lab gateway.

The instructor injects **one** fault.

Possible instructor faults:

- SRV01 connected to vSW-Private instead of vSW-Lab;
- SRV01 virtual NIC disconnected;
- wrong IPv4 subnet inside SRV01;
- wrong default gateway;
- LabNAT missing;
- incorrect DNS server.

Students are not told which fault was injected.

## Troubleshooting rule

Do not change configuration until the failure layer has been identified.

## Step 1 — Check VM state

~~~powershell
Get-VM SRV01
~~~

## Step 2 — Check virtual networking

~~~powershell
Get-VMNetworkAdapter -VMName SRV01 | Select-Object VMName,SwitchName,Status,MacAddress
Get-VMSwitch
~~~

## Step 3 — Check NAT and host-side lab adapter

~~~powershell
Get-NetIPAddress -InterfaceAlias "vEthernet (vSW-Lab)" -AddressFamily IPv4
Get-NetNat
~~~

## Step 4 — Check guest networking

Inside SRV01:

~~~powershell
Get-NetAdapter
Get-NetIPConfiguration
Get-NetRoute -AddressFamily IPv4
Get-DnsClientServerAddress -AddressFamily IPv4
~~~

## Step 5 — Test one layer at a time

Inside SRV01:

~~~powershell
Test-NetConnection 172.22.0.1
Test-NetConnection 1.1.1.1
Resolve-DnsName microsoft.com
Test-NetConnection microsoft.com -Port 443
~~~

## Student conclusion

Before applying a fix, report:

- observed symptom;
- layer where the failure occurs;
- evidence;
- proposed root cause;
- intended corrective action.

## Validation after correction

Repeat the connectivity matrix and verify that the expected paths work again.

---

# Day 2 review questions

Students should be able to answer:

1. Why is Hyper-V considered a Type-1 hypervisor?
2. What is the role of the root partition?
3. What is a child partition?
4. What purpose does VMBus serve?
5. What is the relationship between a VSP and a VSC?
6. Why is Generation 2 preferred for new Windows Server 2025 VMs?
7. Why does assigning more vCPUs not always improve performance?
8. What is the difference between Startup, Minimum and Maximum RAM with Dynamic Memory?
9. What is the difference between an External, Internal and Private switch?
10. Why does this nested lab use Internal switch + NAT instead of an External switch?
11. What is the difference between Saved and Off VM states?
12. What is the role of the VHDX file?
13. Why should VM files and virtual disks use predictable storage paths?
14. Why is it useful to configure host default VM and VHDX paths?
15. Which checks distinguish an IP problem from a DNS problem?
16. What evidence proves which vSwitch a VM is connected to?

---

# End-of-day validation checklist

- [ ] Hyper-V role installed on HV01.
- [ ] VMMS running.
- [ ] Hyper-V Manager operational.
- [ ] Hyper-V architecture understood at a high level.
- [ ] Root and child partitions can be explained.
- [ ] VMBus/VSP/VSC concept understood.
- [ ] Generation 1 vs Generation 2 understood.
- [ ] vSW-Lab created as Internal.
- [ ] LabNAT configured for 172.22.0.0/24.
- [ ] vSW-Private created for comparison.
- [ ] SRV01 created as Generation 2.
- [ ] SRV01 configured with 2 vCPUs.
- [ ] Dynamic Memory configured and understood.
- [ ] Basic VM lifecycle states exercised.
- [ ] Hyper-V default VM/VHDX paths configured.
- [ ] SRV01 VHDX stored under the course storage path.
- [ ] Additional SRV01 data VHDX created and attached.
- [ ] Secure Boot inspected.
- [ ] Windows Server 2025 installed in SRV01.
- [ ] SRV01 configured with static IPv4.
- [ ] HV01-to-SRV01 connectivity verified.
- [ ] SRV01 outbound IP connectivity verified.
- [ ] SRV01 DNS resolution verified.
- [ ] SRV01 TCP/443 connectivity verified.
- [ ] DC01 created.
- [ ] Virtual-network break/fix exercise completed using evidence.
- [ ] Students can map basic virtualization concepts to Hyper-V terminology.

---

# Microsoft references

- Hyper-V overview: https://learn.microsoft.com/windows-server/virtualization/hyper-v/overview
- Hyper-V architecture: https://learn.microsoft.com/windows-server/virtualization/hyper-v/architecture
- Hyper-V terminology and features: https://learn.microsoft.com/windows-server/virtualization/hyper-v/features-terminology
- Install Hyper-V: https://learn.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-hyper-v
- Create a virtual machine: https://learn.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v
- VM generations: https://learn.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v
- Dynamic Memory: https://learn.microsoft.com/windows-server/virtualization/hyper-v/dynamic-memory
- Virtual switches: https://learn.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines
- Nested virtualization: https://learn.microsoft.com/windows-server/virtualization/hyper-v/enable-nested-virtualization
