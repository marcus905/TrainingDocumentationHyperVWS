# Day 1 — Windows Server 2025 Fundamentals

## Learning objectives

By the end of Day 1, students should be able to:

- explain the basic Windows Server architecture and installation options;
- distinguish Server Core and Desktop Experience;
- perform initial post-installation configuration;
- manage roles and features;
- use Server Manager, SConfig and PowerShell;
- configure IPv4 addressing, DNS and basic routing;
- inspect and configure local storage;
- validate a newly deployed server;
- troubleshoot common post-installation issues using evidence.

---

# Module 0 — PowerShell Essentials for the Course

## Why PowerShell matters in this course

PowerShell will be used throughout the course to inspect, configure and troubleshoot Windows Server and Hyper-V.

The goal of this module is not to teach PowerShell scripting in depth. Students only need a practical foundation that will make the later labs easier to follow.

By the end of this section, students should be comfortable with:

- the Verb-Noun command naming convention;
- discovering commands;
- reading command help;
- understanding that PowerShell works with objects rather than plain text;
- using the pipeline;
- filtering and selecting data;
- using variables;
- previewing administrative changes with WhatIf;
- identifying which PowerShell edition they are running.

---

## Windows PowerShell 5.1 vs PowerShell 7

Windows PowerShell and PowerShell are separate products.

**Windows PowerShell 5.1**

- ships with Windows;
- runs on the full .NET Framework;
- is Windows-only;
- is launched with powershell.exe;
- remains important for some Windows Server management modules.

**PowerShell 7**

- is installed separately;
- runs side-by-side with Windows PowerShell 5.1;
- is based on modern .NET;
- is cross-platform;
- is launched with pwsh.exe;
- can use many Windows PowerShell modules directly, while other modules may require Windows PowerShell Compatibility.

For this course, use **Windows PowerShell 5.1 by default unless the instructor explicitly asks you to use PowerShell 7**.

This keeps the Windows Server and Hyper-V labs consistent and avoids unexpected module-compatibility differences.

References:

- https://learn.microsoft.com/powershell/scripting/windows-powershell/overview
- https://learn.microsoft.com/powershell/scripting/install/install-powershell-on-windows
- https://learn.microsoft.com/powershell/module/microsoft.powershell.core/about/about_windows_powershell_compatibility

### Identify the current shell

Run:

~~~powershell
$PSVersionTable
~~~

Important fields:

- PSVersion;
- PSEdition.

In Windows PowerShell 5.1, PSEdition normally reports:

~~~text
Desktop
~~~

In PowerShell 7, it normally reports:

~~~text
Core
~~~

A concise view:

~~~powershell
$PSVersionTable | Select-Object PSVersion,PSEdition
~~~

### Check which executable started the session

Windows PowerShell:

~~~text
powershell.exe
~~~

PowerShell 7:

~~~text
pwsh.exe
~~~

If PowerShell 7 is installed, both shells can exist on the same server.

---

## Cmdlets and the Verb-Noun convention

Most PowerShell commands follow this pattern:

~~~text
Verb-Noun
~~~

Examples:

~~~powershell
Get-Service
Get-Process
Get-NetAdapter
Get-Volume
Start-Service
Stop-Service
~~~

The verb describes the action and the noun describes the managed object.

This naming model makes commands easier to discover.

---

## Discover commands with Get-Command

Find commands related to network adapters:

~~~powershell
Get-Command *NetAdapter*
~~~

Find commands using a particular verb:

~~~powershell
Get-Command -Verb Get
~~~

Find commands from a module:

~~~powershell
Get-Command -Module NetTCPIP
~~~

### Teaching point

If you remember the technology but not the exact cmdlet, command discovery is often faster and safer than guessing.

---

## Read help with Get-Help

Display help for a cmdlet:

~~~powershell
Get-Help Get-NetAdapter
~~~

Show examples:

~~~powershell
Get-Help Get-NetAdapter -Examples
~~~

Show detailed help:

~~~powershell
Get-Help Get-NetAdapter -Detailed
~~~

If help content is incomplete, administrative systems can update it with:

~~~powershell
Update-Help
~~~

Internet access and permissions may be required.

---

## PowerShell returns objects

Run:

~~~powershell
Get-Service
~~~

PowerShell does not simply return formatted text. It returns service objects with properties.

Inspect one service:

~~~powershell
Get-Service | Select-Object -First 1 | Format-List *
~~~

This exposes properties that can be filtered, sorted and selected.

### Why this matters

Later in the course, commands such as Get-VM, Get-NetAdapter and Get-Disk return objects representing real system resources.

---

## The pipeline

The pipeline character is:

~~~text
|
~~~

It sends objects from one command to another.

Example:

~~~powershell
Get-Service | Where-Object Status -eq "Running"
~~~

This means:

1. Get-Service retrieves service objects.
2. Where-Object keeps only services whose Status is Running.

Select specific properties:

~~~powershell
Get-Service |
    Where-Object Status -eq "Running" |
    Select-Object Name,Status
~~~

Sort the results:

~~~powershell
Get-Service |
    Where-Object Status -eq "Running" |
    Sort-Object Name |
    Select-Object Name,Status
~~~

### Course connection

The same pattern will later be used with:

~~~powershell
Get-WindowsFeature | Where-Object Installed
~~~

and:

~~~powershell
Get-NetRoute |
    Where-Object DestinationPrefix -eq "0.0.0.0/0"
~~~

---

## Select-Object

Select only the properties you need:

~~~powershell
Get-NetAdapter |
    Select-Object Name,Status,LinkSpeed,MacAddress
~~~

This is useful when a command returns much more information than is relevant to the current task.

---

## Where-Object

Filter objects:

~~~powershell
Get-Service |
    Where-Object Status -eq "Stopped"
~~~

A more explicit form is:

~~~powershell
Get-Service |
    Where-Object { $_.Status -eq "Stopped" }
~~~

The shorter syntax is sufficient for simple property comparisons.

The $_ variable in the script-block syntax represents the current object passing through the pipeline.

---

## Variables

Variables start with the dollar sign.

Example:

~~~powershell
$ServerName = "HV01"
$ServerName
~~~

Use the variable:

~~~powershell
Write-Host "Server name is $ServerName"
~~~

Variables help avoid repeatedly typing the same values.

Example:

~~~powershell
$Interface = "Ethernet"

Get-NetIPConfiguration -InterfaceAlias $Interface
Get-DnsClientServerAddress -InterfaceAlias $Interface
~~~

---

## Formatting output

Table view:

~~~powershell
Get-Service | Format-Table Name,Status
~~~

List view:

~~~powershell
Get-Service -Name WinRM | Format-List *
~~~

### Important note

Format-Table and Format-List are intended for presentation.

Avoid placing additional data-processing commands after formatting cmdlets because the formatting operation changes the objects being passed through the pipeline.

Prefer:

~~~powershell
Get-Service |
    Where-Object Status -eq "Running" |
    Select-Object Name,Status |
    Format-Table
~~~

rather than formatting early in the pipeline.

---

## Tab completion

PowerShell supports tab completion.

Start typing:

~~~text
Get-NetA
~~~

then press Tab.

PowerShell can complete:

- cmdlet names;
- parameters;
- some parameter values;
- file paths.

Students are encouraged to use tab completion rather than typing long command names from memory.

---

## Administrative privileges

Many commands that change Windows configuration require an elevated shell.

The PowerShell window should be started using:

**Run as administrator**

Commands that only retrieve information often work without elevation, while configuration commands may return access-denied or privilege-related errors.

---

## Preview changes with WhatIf

Many administrative cmdlets support the WhatIf common parameter.

Example:

~~~powershell
Install-WindowsFeature Telnet-Client -WhatIf
~~~

WhatIf shows what the command intends to do without making the change.

This is a useful safety habit for administrative work.

Not every cmdlet supports WhatIf, so always check command help when unsure.

---

## Confirm behavior

Some commands can request confirmation before making a change.

The common Confirm parameter can be used where supported:

~~~powershell
Remove-Item C:\Temp\Example.txt -Confirm
~~~

Do not run destructive examples during the course unless specifically instructed.

---

## Reading errors

When a command fails, read the error before changing anything.

Look for:

- the cmdlet that failed;
- the object or parameter involved;
- permission errors;
- invalid parameter values;
- missing resources;
- connectivity failures.

Example:

~~~powershell
Get-Service -Name "ThisServiceDoesNotExist"
~~~

The resulting error clearly indicates that the specified service cannot be found.

### Troubleshooting principle

An error message is evidence.

Do not immediately retry the same command without understanding why it failed.

---

## Mini exercise — Build a simple pipeline

### Task 1

List all services:

~~~powershell
Get-Service
~~~

### Task 2

Show only running services:

~~~powershell
Get-Service |
    Where-Object Status -eq "Running"
~~~

### Task 3

Display only service name and status:

~~~powershell
Get-Service |
    Where-Object Status -eq "Running" |
    Select-Object Name,Status
~~~

### Task 4

Sort the list by name:

~~~powershell
Get-Service |
    Where-Object Status -eq "Running" |
    Sort-Object Name |
    Select-Object Name,Status
~~~

### Validation

Students should be able to explain what each stage of the pipeline does.

---

## PowerShell primer checkpoint

Before continuing, students should be able to answer:

1. What is the difference between powershell.exe and pwsh.exe?
2. What does $PSVersionTable show?
3. What does Get-Command help you discover?
4. What does Get-Help -Examples provide?
5. What travels through a PowerShell pipeline?
6. What does Where-Object do?
7. What does Select-Object do?
8. What does the $_ variable represent?
9. Why is WhatIf useful?
10. Why should error messages be treated as troubleshooting evidence?

---

# Module 1 — Windows Server 2025 architecture and installation options

## Concepts

Discuss:

- Windows Server Standard and Datacenter editions;
- Server Core;
- Server with Desktop Experience;
- roles and role services;
- Windows features;
- Windows services;
- Event Logs;
- network interfaces and TCP/IP configuration;
- disks, partitions and volumes;
- local versus remote management;
- PowerShell as an administrative interface.

### Server Core vs Desktop Experience

Server Core has a reduced graphical footprint and is normally managed using PowerShell, SConfig and remote administration tools.

Desktop Experience includes the full Windows graphical shell.

For this course, HV01 and HV02 use **Desktop Experience** because students need local access to tools such as:

- Server Manager;
- Event Viewer;
- Performance Monitor;
- Resource Monitor;
- Hyper-V Manager.

Reference:

https://learn.microsoft.com/windows-server/get-started/getting-started-with-server-core

---

# Lab 1.1 — Initial server inspection and configuration

## Objective

Become familiar with a newly installed Windows Server 2025 system and establish a known configuration baseline.

Run all PowerShell commands from an **elevated PowerShell session** unless otherwise stated.

## Step 1 — Inspect the operating system

Run:

~~~powershell
Get-ComputerInfo
~~~

### What this command does

Get-ComputerInfo returns a broad inventory of operating system, hardware, firmware and configuration information.

The output is intentionally extensive. Students should not attempt to memorize it.

Focus on fields such as:

- WindowsProductName;
- WindowsEditionId;
- WindowsVersion;
- OsName;
- OsVersion;
- OsBuildNumber;
- CsName;
- CsDomain;
- CsProcessors;
- CsTotalPhysicalMemory;
- BiosFirmwareType.

A shorter view can be generated with:

~~~powershell
Get-ComputerInfo | Select-Object WindowsProductName,WindowsEditionId,OsVersion,OsBuildNumber,CsName,CsDomain,CsTotalPhysicalMemory,BiosFirmwareType
~~~

### Validation

Students should be able to identify:

1. the Windows Server edition;
2. the server name;
3. the build/version;
4. available physical memory;
5. firmware type.

---

## Step 2 — Verify the current computer name

Run:

~~~powershell
hostname
~~~

and compare with:

~~~powershell
$env:COMPUTERNAME
~~~

and:

~~~powershell
Get-ComputerInfo | Select-Object CsName
~~~

### Why use several methods?

This demonstrates that Windows configuration can often be inspected using:

- classic command-line utilities;
- environment variables;
- PowerShell cmdlets.

All three should report the same computer name.

---

## Step 3 — Rename the server

If the server is not already named HV01:

~~~powershell
Rename-Computer -NewName "HV01"
~~~

### What happens?

Rename-Computer changes the configured computer name, but the new name does not become fully active until the server restarts.

Verify the pending change:

~~~powershell
Get-ComputerInfo | Select-Object CsName
~~~

Restart when instructed by the trainer:

~~~powershell
Restart-Computer
~~~

After restart:

~~~powershell
hostname
~~~

Expected result:

~~~text
HV01
~~~

### Troubleshooting note

If Rename-Computer fails, check that:

- PowerShell is running elevated;
- the requested name is valid;
- no policy or domain restriction prevents the change.

---

## Step 4 — Review time and time zone configuration

Run:

~~~powershell
Get-Date
Get-TimeZone
~~~

### Why this matters

Incorrect system time can affect:

- authentication;
- Kerberos;
- certificates;
- event correlation;
- troubleshooting timelines;
- replication technologies.

List available time zones if required:

~~~powershell
Get-TimeZone -ListAvailable
~~~

Do not change the time zone unless requested by the instructor.

---

## Step 5 — Inspect network adapters

Run:

~~~powershell
Get-NetAdapter
~~~

### Important fields

Look at:

- Name;
- InterfaceDescription;
- Status;
- LinkSpeed;
- MacAddress;
- ifIndex.

A more concise view:

~~~powershell
Get-NetAdapter | Select-Object Name,Status,LinkSpeed,MacAddress,ifIndex
~~~

### Interpretation

Status should normally be **Up** for an active interface.

If the interface is Down, determine whether:

- the virtual adapter is disconnected;
- the Hyper-V virtual switch is incorrect;
- the adapter is administratively disabled.

---

## Step 6 — Inspect IP addressing

Run:

~~~powershell
Get-NetIPAddress
~~~

This can return multiple addresses including IPv4, IPv6 and automatically generated addresses.

To focus on IPv4:

~~~powershell
Get-NetIPAddress -AddressFamily IPv4
~~~

For an operational summary:

~~~powershell
Get-NetIPConfiguration
~~~

### What to identify

Students should locate:

- interface name;
- IPv4 address;
- prefix length;
- default gateway;
- DNS servers.

Compare the output with the classic command:

~~~powershell
ipconfig /all
~~~

### Teaching point

Get-NetIPConfiguration is usually easier to consume programmatically.

ipconfig /all remains extremely useful during troubleshooting because it exposes a familiar consolidated view.

---

## Step 7 — Inspect DNS configuration

Run:

~~~powershell
Get-DnsClientServerAddress
~~~

Limit output to IPv4:

~~~powershell
Get-DnsClientServerAddress -AddressFamily IPv4
~~~

### Questions for students

- Which adapter has DNS servers configured?
- Is the server using one or multiple DNS servers?
- Are those DNS servers reachable?
- Are they appropriate for the lab network?

---

## Step 8 — Inspect routing

Run:

~~~powershell
Get-NetRoute
~~~

The routing table can be large.

Focus on IPv4 default routes:

~~~powershell
Get-NetRoute -AddressFamily IPv4 | Where-Object DestinationPrefix -eq "0.0.0.0/0"
~~~

### Important fields

- DestinationPrefix;
- NextHop;
- InterfaceAlias;
- RouteMetric;
- ifMetric.

### Teaching point

The default route determines where traffic is sent when no more specific route exists.

A machine can have a correct IP address and still fail to communicate outside its subnet if the default gateway is wrong or missing.

---

## Step 9 — Test connectivity

Start with basic IP connectivity:

~~~powershell
Test-NetConnection 1.1.1.1
~~~

Important field:

~~~text
PingSucceeded
~~~

Next test a hostname:

~~~powershell
Test-NetConnection microsoft.com
~~~

Then test DNS directly:

~~~powershell
Resolve-DnsName microsoft.com
~~~

### Interpretation

Possible outcomes:

| Test | Result | Likely conclusion |
|---|---|---|
| IP succeeds, DNS succeeds | Normal | Network path and DNS are functional |
| IP succeeds, DNS fails | Problem | Investigate DNS |
| IP fails, DNS resolves | Possible routing/firewall issue | DNS may work but data path is failing |
| Both fail | Broader connectivity issue | Check adapter, IP, route and upstream network |

The exact behavior of public ICMP tests can be affected by upstream firewalls, so do not use ping as the only proof of network health.

---

# Module 2 — Roles and features

Windows Server uses roles and features to add operating-system capabilities.

Microsoft supports managing them through both Server Manager and PowerShell. Get-WindowsFeature lists roles and features, while Install-WindowsFeature installs them. Management tools are not automatically added for every role unless requested with the appropriate option. citeturn697734search0turn697734search1

## Step 1 — List available roles and features

~~~powershell
Get-WindowsFeature
~~~

### Reading the output

Typical columns include:

- Display Name;
- Name;
- Install State.

The **Name** value is the identifier used by PowerShell.

For example, a display name may be friendly text while the PowerShell name is shorter.

---

## Step 2 — Display only installed components

~~~powershell
Get-WindowsFeature | Where-Object Installed
~~~

Alternative syntax:

~~~powershell
Get-WindowsFeature | Where-Object InstallState -eq "Installed"
~~~

### Teaching point

The pipeline sends objects returned by Get-WindowsFeature to Where-Object for filtering.

Students should understand that this is object-based processing rather than text parsing.

---

## Step 3 — Search for a feature

Before installing anything, find it:

~~~powershell
Get-WindowsFeature *Telnet*
~~~

This demonstrates wildcard filtering.

---

## Step 4 — Preview a change

Use WhatIf before performing the installation:

~~~powershell
Install-WindowsFeature Telnet-Client -WhatIf
~~~

### Why use WhatIf?

WhatIf shows the intended action without applying the change.

It is a useful habit when learning administrative PowerShell commands.

---

## Step 5 — Install the training feature

~~~powershell
Install-WindowsFeature Telnet-Client
~~~

The returned object includes values such as:

- Success;
- RestartNeeded;
- ExitCode;
- FeatureResult.

Verify:

~~~powershell
Get-WindowsFeature Telnet-Client
~~~

Expected state:

~~~text
Installed
~~~

The Telnet Client is used only as a simple feature-management example. It is not being recommended as a production remote-administration method.

---

## Step 6 — Remove the feature

Preferred current cmdlet:

~~~powershell
Uninstall-WindowsFeature Telnet-Client
~~~

Verify again:

~~~powershell
Get-WindowsFeature Telnet-Client
~~~

Expected state:

~~~text
Available
~~~

---

# Module 3 — Windows Server administration tools

Students should know when each tool is appropriate.

| Tool | Typical use |
|---|---|
| Server Manager | Roles, features, server overview and multi-server management |
| Computer Management | Local administration console |
| Services | Service startup/state management |
| Event Viewer | Logs and diagnostic events |
| Task Manager | Fast process/resource overview |
| Resource Monitor | Per-process CPU, disk, memory and network detail |
| Performance Monitor | Counters, baselines and historical collection |
| SConfig | Server configuration, especially important on Server Core |
| PowerShell | Repeatable and scriptable administration |
| Windows Admin Center | Browser-based Windows infrastructure management |

## Exercise 1.2 — Find the same information using GUI and PowerShell

Students should identify:

1. the server name;
2. active network interfaces;
3. installed roles;
4. service status;
5. recent system errors;
6. disk/volume state.

For each item, find one GUI method and one PowerShell method.

Example:

| Information | GUI | PowerShell |
|---|---|---|
| Network adapters | Server Manager / Network Connections | Get-NetAdapter |
| System errors | Event Viewer | Get-WinEvent |
| Volumes | Disk Management | Get-Volume |
| Services | Services.msc | Get-Service |

---

# Module 4 — Basic networking configuration

## Addressing plan

The instructor should provide the final lab addressing plan.

Example only:

~~~text
HV01
IPv4 address: 10.10.10.11
Prefix:       /24
Gateway:      10.10.10.1
DNS:          10.10.10.10
~~~

Do not copy this example blindly if the class topology uses different addresses.

---

## Lab 1.3 — Configure a static IPv4 address

### Step 1 — Identify the interface name

~~~powershell
Get-NetAdapter
~~~

Assume the interface is named Ethernet.

### Step 2 — Inspect existing configuration

~~~powershell
Get-NetIPConfiguration -InterfaceAlias "Ethernet"
~~~

### Step 3 — Configure the IP address

~~~powershell
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 10.10.10.11 -PrefixLength 24 -DefaultGateway 10.10.10.1
~~~

### Parameter explanation

- InterfaceAlias selects the network adapter.
- IPAddress defines the static address.
- PrefixLength defines the subnet.
- DefaultGateway creates the default route.

### Important remote-lab warning

Changing the wrong interface can interrupt connectivity.

Students should verify the interface before executing this command.

### Step 4 — Configure DNS

~~~powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 10.10.10.10
~~~

For multiple DNS servers:

~~~powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 10.10.10.10,10.10.10.11
~~~

### Step 5 — Validate

~~~powershell
Get-NetIPConfiguration -InterfaceAlias "Ethernet"
Get-DnsClientServerAddress -InterfaceAlias "Ethernet"
Get-NetRoute -AddressFamily IPv4
~~~

Test:

~~~powershell
Test-NetConnection 10.10.10.1
Resolve-DnsName microsoft.com
~~~

Students should record their final configuration.

---

# Module 5 — Local storage

## Concepts

Explain the distinction between:

- physical/virtual disk;
- disk initialization;
- GPT partition table;
- partition;
- volume;
- filesystem;
- drive letter;
- volume label.

## Step 1 — Inspect disks

~~~powershell
Get-Disk
~~~

Important fields:

- Number;
- FriendlyName;
- OperationalStatus;
- PartitionStyle;
- Size.

Students must carefully identify the newly attached training disk.

Do not assume the new disk is always Disk 1.

---

## Step 2 — Inspect partitions

~~~powershell
Get-Partition
~~~

This maps partitions to disk numbers and drive letters.

---

## Step 3 — Inspect volumes

~~~powershell
Get-Volume
~~~

Important fields:

- DriveLetter;
- FileSystemLabel;
- FileSystem;
- HealthStatus;
- Size;
- SizeRemaining.

---

# Lab 1.4 — Prepare the Hyper-V data disk

## Safety check

Before modifying a disk, identify the intended training disk:

~~~powershell
Get-Disk | Format-Table Number,FriendlyName,PartitionStyle,OperationalStatus,Size
~~~

The instructor should confirm the disk number before students continue.

For the examples below, assume the training disk is **Disk 1**.

## Step 1 — Initialize the disk

~~~powershell
Initialize-Disk -Number 1 -PartitionStyle GPT
~~~

### What this does

Initialize-Disk prepares a raw disk with a partition table.

GPT is used for the training disk.

Verify:

~~~powershell
Get-Disk -Number 1
~~~

PartitionStyle should now report GPT.

---

## Step 2 — Create a partition

~~~powershell
New-Partition -DiskNumber 1 -UseMaximumSize -DriveLetter D
~~~

### What this does

- DiskNumber selects the disk.
- UseMaximumSize allocates the available space.
- DriveLetter assigns D:.

Verify:

~~~powershell
Get-Partition -DiskNumber 1
~~~

---

## Step 3 — Format the volume

~~~powershell
Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel "HyperVData" -Confirm:$false
~~~

### What this does

Creates an NTFS filesystem and labels the volume HyperVData.

Verify:

~~~powershell
Get-Volume -DriveLetter D
~~~

Expected:

- FileSystem = NTFS;
- FileSystemLabel = HyperVData;
- HealthStatus = Healthy.

---

## Step 4 — Create the Hyper-V folder structure

~~~powershell
New-Item -ItemType Directory -Path "D:\Hyper-V\VMs" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\VHDX" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\ISO" -Force
New-Item -ItemType Directory -Path "D:\Hyper-V\Replica" -Force
~~~

Verify:

~~~powershell
Get-ChildItem "D:\Hyper-V"
~~~

Expected structure:

~~~text
D:\Hyper-V
|
+-- VMs
+-- VHDX
+-- ISO
+-- Replica
~~~

### Teaching point

The exact production folder layout is an organizational decision.

The purpose of this structure is to introduce predictable placement and operational consistency before installing Hyper-V.

---

# Break/Fix 1 — DNS failure

## Scenario

The instructor intentionally configures an incorrect DNS server.

The student is told only:

> The server appears to have network connectivity, but accessing resources by hostname fails.

## Rule

Do not change the configuration immediately.

First collect evidence.

## Step 1 — Check adapter and IP configuration

~~~powershell
Get-NetAdapter
Get-NetIPConfiguration
ipconfig /all
~~~

Questions:

- Is the interface Up?
- Is the IP correct?
- Is the gateway correct?
- Which DNS server is configured?

## Step 2 — Test the network path

Test the local gateway:

~~~powershell
Test-NetConnection 10.10.10.1
~~~

Test another known IP if supplied by the instructor.

## Step 3 — Test DNS resolution

~~~powershell
Resolve-DnsName microsoft.com
~~~

If the lab has an internal DNS server, also test an internal hostname.

## Step 4 — Inspect DNS servers

~~~powershell
Get-DnsClientServerAddress -AddressFamily IPv4
~~~

## Student conclusion

Students should report:

- symptom;
- evidence;
- affected layer;
- likely root cause;
- proposed correction.

Only then should the DNS configuration be corrected.

## Validate after correction

~~~powershell
Resolve-DnsName microsoft.com
Test-NetConnection microsoft.com -Port 443
~~~

Using TCP port 443 validation helps demonstrate that successful name resolution and actual application connectivity are distinct checks.

---

# Day 1 review questions

Students should be able to answer:

1. What is the difference between Server Core and Desktop Experience?
2. What information does Get-NetIPConfiguration consolidate?
3. Why can correct IP addressing still result in failed external communication?
4. What is the purpose of a default route?
5. What is the difference between Test-NetConnection and Resolve-DnsName?
6. Why should a disk number always be verified before Initialize-Disk?
7. What is the difference between a disk, partition and volume?
8. Why use WhatIf before certain administrative commands?
9. What does the PowerShell pipeline do in Get-WindowsFeature | Where-Object Installed?
10. Why is consistent folder structure useful in server administration?

---

# End-of-day validation checklist

- [ ] Windows Server edition/build identified.
- [ ] Server name verified.
- [ ] Time and time zone checked.
- [ ] Network interfaces inspected.
- [ ] IPv4 configuration understood and documented.
- [ ] DNS configuration understood and documented.
- [ ] Default route identified.
- [ ] Connectivity and DNS tested separately.
- [ ] Roles/features queried and modified.
- [ ] Administration tools reviewed.
- [ ] Training data disk initialized safely.
- [ ] NTFS volume created and verified.
- [ ] Hyper-V folder structure created.
- [ ] DNS break/fix scenario completed using evidence.
- [ ] Student can explain the reasoning behind the commands used.

---

# Microsoft references

- Windows Server overview: https://learn.microsoft.com/windows-server/get-started/overview
- Install Windows Server: https://learn.microsoft.com/windows-server/get-started/install-windows-server
- Windows Server hardware requirements: https://learn.microsoft.com/windows-server/get-started/hardware-requirements
- Server Core: https://learn.microsoft.com/windows-server/get-started/getting-started-with-server-core
- Roles and features: https://learn.microsoft.com/windows-server/administration/server-manager/add-remove-roles-features
- Install-WindowsFeature: https://learn.microsoft.com/powershell/module/servermanager/install-windowsfeature?view=windowsserver2025-ps
- Networking PowerShell reference: https://learn.microsoft.com/powershell/module/nettcpip/
- DNS Client PowerShell reference: https://learn.microsoft.com/powershell/module/dnsclient/
- Storage PowerShell reference: https://learn.microsoft.com/powershell/module/storage/
