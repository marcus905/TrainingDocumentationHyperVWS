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
- troubleshoot common post-installation issues using evidence;
- use the Hyper-V PowerShell module for read-only inspection of the outer lab.

---

# Module 0 — PowerShell Essentials for the Course

## Why PowerShell matters in this course

PowerShell will be used throughout the course to inspect, configure and troubleshoot Windows Server and Hyper-V.

The goal is not to teach PowerShell scripting in depth. This primer covers only the concepts and habits used repeatedly in the labs.

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

**Windows PowerShell 5.1** ships with Windows, uses the full .NET Framework, runs only on Windows, and is launched with powershell.exe. It remains important because some Windows Server management modules were designed for it.

**PowerShell 7** is installed separately, runs side-by-side with Windows PowerShell 5.1, uses modern .NET, is cross-platform, and is launched with pwsh.exe. Many Windows management modules work directly in PowerShell 7; others may use Windows PowerShell Compatibility.

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

If local help content is incomplete, update it with:

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

PowerShell returns service objects, not just formatted text.

Inspect one service:

~~~powershell
Get-Service | Select-Object -First 1 | Format-List *
~~~

This exposes properties that can be filtered, sorted and selected.

### Why this matters

Later, Get-VM, Get-NetAdapter and Get-Disk use the same object model, which lets us filter and select properties consistently.

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

## Filtering and selecting properties

Use Select-Object to keep only the properties you need:

~~~powershell
Get-NetAdapter |
    Select-Object Name,Status,LinkSpeed,MacAddress
~~~

Use Where-Object to keep only objects that match a condition:

~~~powershell
Get-Service |
    Where-Object Status -eq "Stopped"
~~~

For more complex conditions, use the script-block form:

~~~powershell
Get-Service |
    Where-Object { $_.Status -eq "Stopped" }
~~~

In that form, $_ represents the current object moving through the pipeline.

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

# Module 0B — Hyper-V PowerShell Orientation

## Why introduce Hyper-V PowerShell on Day 1?

Hyper-V is not installed inside HV01 until Day 2, but the **physical Windows 11 host already runs Hyper-V** and already contains HV01 and HV02.

That gives students a safe opportunity to learn the Hyper-V PowerShell vocabulary before they begin configuring the nested Hyper-V host.

This section is intentionally **read-only**. Students should use it to discover and inspect Hyper-V objects on the physical host, not to experiment with configuration-changing commands.

## Connect the general PowerShell primer to Hyper-V

The same patterns introduced in Module 0 apply directly to the Hyper-V module:

~~~text
Get-Command
Get-Help
objects
pipeline
Where-Object
Select-Object
~~~

The main new skill is learning common Hyper-V nouns:

~~~text
VM
VMHost
VMProcessor
VMMemory
VMNetworkAdapter
VMSwitch
VMHardDiskDrive
VHD
VMFirmware
VMSnapshot
VMReplication
~~~

Typical verbs indicate intent:

~~~text
Get-*       inspect
New-*       create
Set-*       change
Add-*       attach
Connect-*   connect
Start-*     start
Stop-*      stop
Remove-*    remove
~~~

The verb is a useful risk signal, but students should still read command help before execution.

## Run this section on the physical Windows 11 host

Confirm the Hyper-V module is available:

~~~powershell
Get-Module -ListAvailable Hyper-V
~~~

Discover Hyper-V commands:

~~~powershell
Get-Command -Module Hyper-V
~~~

Search by noun or keyword:

~~~powershell
Get-Command -Module Hyper-V -Noun VM
Get-Command -Module Hyper-V -Noun VMSwitch
Get-Command -Module Hyper-V -Name "*VHD*"
~~~

Read examples:

~~~powershell
Get-Help Get-VM -Examples
Get-Help Get-VMNetworkAdapter -Examples
~~~

## Inspect the existing outer lab

~~~powershell
Get-VM
~~~

Select useful properties:

~~~powershell
Get-VM |
    Select-Object Name,State,Generation,ProcessorCount,MemoryAssigned
~~~

Inspect HV01:

~~~powershell
Get-VM -Name HV01
Get-VMProcessor -VMName HV01
Get-VMMemory -VMName HV01
Get-VMNetworkAdapter -VMName HV01
Get-VMHardDiskDrive -VMName HV01
Get-VMFirmware -VMName HV01
~~~

Inspect virtual networking:

~~~powershell
Get-VMSwitch

Get-VMNetworkAdapter -VMName HV01,HV02 |
    Select-Object VMName,Name,SwitchName,MacAddress,Status
~~~

Inspect host defaults:

~~~powershell
Get-VMHost |
    Select-Object VirtualMachinePath,VirtualHardDiskPath,LogicalProcessorCount
~~~

## Apply the pipeline model

~~~powershell
Get-VM |
    Where-Object State -eq "Running" |
    Select-Object Name,State,ProcessorCount,MemoryAssigned
~~~

This is the same object-and-pipeline model students just used with Windows services and networking.

## Safety rule

During this Day 1 orientation, use read-only inspection commands unless the instructor explicitly directs otherwise.

Do not experiment with configuration-changing Hyper-V commands on the physical host.

Day 2 provides controlled opportunities to use New-, Set-, Connect-, Start-, Stop- and Remove- cmdlets inside HV01.

## Primer checkpoint

Students should be able to answer:

1. Which PowerShell module provides Hyper-V cmdlets?
2. How can you discover commands related to virtual disks?
3. What is the difference between Get-VM and Get-VMHost?
4. Which cmdlet inspects a VM's vNIC and switch mapping?
5. Which cmdlet inspects VM memory configuration?
6. Why is the physical Windows 11 host used for this Day 1 orientation?
7. Why are read-only Get-* commands emphasized here?

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

## Windows Server architecture — a practical view

For this course, it is useful to think of Windows Server as a set of layers rather than as a collection of isolated tools.

At the bottom, hardware and firmware expose CPU, memory, storage and network devices. The Windows kernel and drivers provide the operating-system foundation. Core networking and storage subsystems sit above that foundation, followed by Windows services and the server roles/features that provide business functionality. Administration tools such as Server Manager and PowerShell operate across those layers.

~~~text
Hardware / Firmware
        |
Windows Kernel
        |
Drivers / Networking / Storage
        |
Windows Services
        |
Roles and Features
        |
Management Layer
GUI / Server Manager / PowerShell / Remote tools
~~~

This layered view is important later in the course because the same user-visible symptom may originate from different layers. For example, a failed application connection might be caused by a service, DNS, routing, a virtual NIC, or an underlying host problem.

### Architecture discussion points

- The kernel and drivers provide the core operating-system and hardware abstraction layer.
- Networking and storage subsystems expose the resources consumed by roles and applications.
- Windows services provide background operating-system and application functionality.
- Roles and features add server capabilities such as Hyper-V, DNS or file services.
- Management can be local or remote and can be performed through graphical tools, PowerShell or browser-based tools.

---

## What is new in Windows Server 2025?

Windows Server 2025 includes changes across security, storage, networking and server management. This course focuses only on the changes that are most relevant to infrastructure and Hyper-V administrators.

Microsoft reference:

https://learn.microsoft.com/windows-server/get-started/whats-new-windows-server-2025

### Security improvements

**Credential Guard** is enabled by default on supported Windows Server 2025 systems that meet the requirements. Credential Guard uses virtualization-based security to protect credentials such as NTLM hashes and Kerberos secrets.

Reference:

https://learn.microsoft.com/windows/security/identity-protection/credential-guard/configure

**SMB security defaults** have also been strengthened. Windows Server 2025 requires SMB signing by default for outbound SMB connections and includes additional SMB hardening and auditing improvements.

Reference:

https://learn.microsoft.com/windows-server/storage/file-server/smb-feature-descriptions

### SMB and file-services improvements

Windows Server 2025 expands SMB capabilities, including:

- SMB over QUIC availability in Standard and Datacenter;
- additional SMB signing and encryption auditing;
- SMB alternative port support;
- more restrictive default firewall behavior for file sharing;
- authentication rate limiting and NTLM-blocking capabilities.

These changes matter for administrators because older assumptions about SMB connectivity and defaults may no longer apply.

### Storage improvements

Windows Server 2025 includes storage enhancements such as:

- optimized NVMe performance;
- Storage Replica compression;
- Storage Replica Enhanced Log;
- ReFS native deduplication and compression scenarios;
- additional thin-provisioning capabilities in supported Storage Spaces Direct environments.

The course does not implement all of these features, but they are relevant when comparing older Windows Server environments with Windows Server 2025.

### Hotpatch

Windows Server 2025 supports Hotpatch scenarios that can apply eligible security updates without rebooting the server, depending on the deployment model and current Microsoft support requirements.

Reference:

https://learn.microsoft.com/windows-server/get-started/hotpatch

### Course perspective

Students are not expected to configure every new Windows Server 2025 feature on Day 1. The objective is to recognize important platform changes and know where to validate current Microsoft guidance before deploying them.

---

## Day 1 deployment verification checklist

The actual Windows Server installation is performed during Day 0 and briefly reviewed by the instructor at the beginning of Day 1.

Before continuing with the Day 1 labs, verify:

- [ ] HV01 boots normally.
- [ ] Windows Server 2025 is installed.
- [ ] Desktop Experience is the selected installation option.
- [ ] The expected Standard or Datacenter edition is installed.
- [ ] The system disk is healthy and has adequate free space.
- [ ] The local Administrator account is usable.
- [ ] No unexpected pending reboot is blocking configuration.
- [ ] The VM clock and time zone are reasonable.
- [ ] The VM has at least one connected network adapter.
- [ ] PowerShell can be opened with administrative privileges.
- [ ] Windows Server can be updated when the lab network provides Internet access.

Useful verification commands:

~~~powershell
Get-ComputerInfo |
    Select-Object WindowsProductName,WindowsEditionId,OsVersion,OsBuildNumber,CsName

Get-Volume
Get-NetAdapter
Get-TimeZone
~~~

If any item fails, correct the lab environment before continuing.

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

Windows Server roles and features can be managed through both Server Manager and PowerShell. Get-WindowsFeature lists them, while Install-WindowsFeature installs them. Some server roles also expose options for installing their management tools.

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

### Apply the primer

This is the same object-and-pipeline pattern used earlier: Get-WindowsFeature returns objects and Where-Object filters them by property.

---

## Step 3 — Search for a feature

Before installing anything, find it:

~~~powershell
Get-WindowsFeature *Telnet*
~~~

This demonstrates wildcard filtering.

---

## Step 4 — Preview the installation

Apply the WhatIf safety pattern introduced in the PowerShell primer:

~~~powershell
Install-WindowsFeature Telnet-Client -WhatIf
~~~

Confirm that the preview describes the expected feature change before running the real installation.

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

# Exercise 1.3 — Verify and manage basic Windows services

## Objective

Use the Services management tools and PowerShell to confirm that essential Windows services exist, understand their current state and safely start a service when appropriate.

This exercise is about operational verification, not changing production service policies.

## Step 1 — Inspect selected services

Run:

~~~powershell
Get-Service -Name EventLog,WinRM,W32Time
~~~

Review:

- Name;
- DisplayName;
- Status.

### What these services represent

- **EventLog** — Windows Event Log service, required for system and application event logging.
- **WinRM** — Windows Remote Management, used by many remote administration workflows.
- **W32Time** — Windows Time service, used for time synchronization.

## Step 2 — Inspect startup configuration

Get-Service shows current runtime state, but not all configuration details.

Run:

~~~powershell
Get-CimInstance Win32_Service |
    Where-Object Name -in "EventLog","WinRM","W32Time" |
    Select-Object Name,State,StartMode
~~~

Compare **State** with **StartMode**.

A service can be configured for automatic startup but currently stopped, or configured manually and started only when needed.

## Step 3 — Verify Event Log service

~~~powershell
Get-Service EventLog
~~~

Expected result:

~~~text
Status : Running
~~~

Do not stop the EventLog service during this lab.

## Step 4 — Verify Windows Remote Management

~~~powershell
Get-Service WinRM
~~~

If WinRM is stopped and the instructor confirms it should be running in the lab:

~~~powershell
Start-Service WinRM
~~~

Verify:

~~~powershell
Get-Service WinRM
~~~

Then inspect the WinRM configuration:

~~~powershell
winrm enumerate winrm/config/listener
~~~

The exact listener state can vary depending on the server configuration and policy.

## Step 5 — Verify Windows Time

~~~powershell
Get-Service W32Time
w32tm /query /status
~~~

If the service is not running, discuss why time synchronization matters before changing the configuration.

## Step 6 — GUI comparison

Open **Services.msc** and locate:

- Windows Event Log;
- Windows Remote Management;
- Windows Time.

Compare the GUI values with the PowerShell output.

## Validation

Students should be able to explain the difference between:

- service existence;
- startup type;
- current service state;
- operational verification of the service.

---

# Module 4 — Basic networking configuration

## Addressing plan

Day 0 established the outer management network used throughout the course:

~~~text
Network:      192.168.240.0/24
Gateway/NAT:  192.168.240.1
HV01:         192.168.240.11
HV02:         192.168.240.12
DNS:          1.1.1.1 or another instructor-approved external DNS server
~~~

Day 1 validates this configuration and repairs it if necessary rather than introducing a separate example network.

---

## Lab 1.4 — Configure a static IPv4 address

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
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.240.11 -PrefixLength 24 -DefaultGateway 192.168.240.1
~~~

### Read the command before running it

Confirm that InterfaceAlias targets the intended adapter and that IPAddress, PrefixLength and DefaultGateway match the instructor-provided addressing plan.

### Important remote-lab warning

Changing the wrong interface can interrupt connectivity.

Students should verify the interface before executing this command.

### Step 4 — Configure DNS

~~~powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 1.1.1.1
~~~

For multiple DNS servers:

~~~powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 1.1.1.1
~~~

### Step 5 — Validate

~~~powershell
Get-NetIPConfiguration -InterfaceAlias "Ethernet"
Get-DnsClientServerAddress -InterfaceAlias "Ethernet"
Get-NetRoute -AddressFamily IPv4
~~~

Test:

~~~powershell
Test-NetConnection 192.168.240.1
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

# Lab 1.5 — Prepare the Hyper-V data disk

Day 0 attached a dedicated 200 GB dynamically expanding data disk to HV01.

This lab initializes that disk and reserves drive **D:** for nested Hyper-V content.

## Safety check

Before modifying a disk, identify the intended training disk:

~~~powershell
Get-Disk | Format-Table Number,FriendlyName,PartitionStyle,OperationalStatus,Size
~~~

The instructor should confirm the disk number before students continue.

For the examples below, assume the training disk is **Disk 1** only after verifying its size and current state.

The training disk should be the approximately 200 GB raw disk attached during Day 0.

Do not initialize the Windows OS disk.

## Step 1 — Initialize the disk

~~~powershell
Initialize-Disk -Number 1 -PartitionStyle GPT
~~~

### Expected effect

The raw training disk is initialized with a GPT partition table.

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

### Expected effect

One partition consumes the available training disk space and is assigned drive letter D:.

Verify:

~~~powershell
Get-Partition -DiskNumber 1
~~~

---

## Step 3 — Format the volume

~~~powershell
Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel "HyperVData" -Confirm:$false
~~~

### Expected effect

The D: volume is formatted as NTFS and labeled HyperVData.

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
New-Item -ItemType Directory -Path "D:\Hyper-V\Export" -Force
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

# Lab 1.6 — Stage Windows Server installation media inside HV01

## Objective

Day 2 creates nested Windows Server VMs inside HV01.

The Windows Server ISO currently exists on the physical Windows 11 host, so copy it into HV01 before Day 2.

Target path:

~~~text
D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso
~~~

## Recommended method — Hyper-V Guest Service Interface

### Step 1 — Enable Guest Service Interface on HV01

On the physical Windows 11 host:

~~~powershell
Enable-VMIntegrationService -VMName "HV01" -Name "Guest Service Interface"
~~~

Verify:

~~~powershell
Get-VMIntegrationService -VMName "HV01"
~~~

### Step 2 — Copy the ISO into HV01

On the physical Windows 11 host:

~~~powershell
Copy-VMFile -Name "HV01" -SourcePath "C:\HyperV-Course\ISO\WS2025-EVAL-x64-EN.iso" -DestinationPath "D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso" -FileSource Host -CreateFullPath
~~~

The destination path is interpreted inside HV01.

### Step 3 — Verify inside HV01

~~~powershell
Get-Item "D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso"
Get-FileHash "D:\Hyper-V\ISO\WS2025-EVAL-x64-EN.iso" -Algorithm SHA256
~~~

Compare the hash with the source ISO on the physical host.

## Alternative

If Copy-VMFile is unavailable or blocked in the student's environment, use an instructor-approved temporary file-share method.

Do not proceed to Day 2 until the ISO is present inside HV01.

---

# Break/Fix 1 — DNS failure

## Prepare the controlled fault

The student creates the DNS fault locally so the remote course does not depend on instructor access to the student's machine.

First record the known-good DNS configuration:

~~~powershell
Get-DnsClientServerAddress -InterfaceAlias "Ethernet" -AddressFamily IPv4
~~~

Then configure the controlled invalid DNS server:

~~~powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 192.168.240.254
~~~

After applying the fault, stop looking at the break instructions and work only from the incident symptom:

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
Test-NetConnection 192.168.240.1
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

## Correct and validate

Restore the course baseline DNS value:

~~~powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 1.1.1.1
~~~

Then validate:

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
8. How does the WhatIf pattern reduce risk before a configuration change?
9. How does Get-WindowsFeature | Where-Object Installed reuse the object-and-pipeline model from the primer?
10. Why is consistent folder structure useful in server administration?
11. What is the difference between Get-VM and Get-VMHost?
12. Which Hyper-V cmdlet shows a VM's virtual network adapter and switch mapping?

---

# End-of-day validation checklist

- [ ] Windows Server edition/build identified.
- [ ] Hyper-V PowerShell orientation completed on the physical host.
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
- [ ] Windows Server ISO staged inside HV01 for Day 2.
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
- What's new in Windows Server 2025: https://learn.microsoft.com/windows-server/get-started/whats-new-windows-server-2025
- Credential Guard: https://learn.microsoft.com/windows/security/identity-protection/credential-guard/configure
- SMB features: https://learn.microsoft.com/windows-server/storage/file-server/smb-feature-descriptions
- Hotpatch: https://learn.microsoft.com/windows-server/get-started/hotpatch
