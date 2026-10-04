# Module 02 — Course Folders and Media

## Introduction

The course uses predictable file locations so later commands can be identical across student machines. Preparing the folders and verifying the installation media now removes unnecessary variation from every later lab.

## Goal

Prepare a predictable folder structure and verify the Windows Server 2025 ISO.

~~~powershell
$Root = "C:\HyperV-Course"
$Folders = @($Root,"$Root\ISO","$Root\VMs","$Root\VHDX","$Root\Export","$Root\Scripts","$Root\Logs")
$Folders | ForEach-Object { New-Item -ItemType Directory -Path $_ -Force | Out-Null }
~~~

Place the Windows Server 2025 x64 ISO in `C:\HyperV-Course\ISO` and use the local name `WS2025-EVAL-x64-EN.iso` where practical.

Verify:

~~~powershell
Get-FileHash "C:\HyperV-Course\ISO\WS2025-EVAL-x64-EN.iso" -Algorithm SHA256
~~~

Recommended host edition: Windows Server 2025 Datacenter with Desktop Experience.


## Concepts to keep in mind

A consistent storage layout makes troubleshooting easier. ISO integrity also matters: a damaged installation image can produce symptoms that look like VM, storage, or operating-system problems.

## Validation

- [ ] Course folders exist.
- [ ] ISO exists.
- [ ] SHA-256 recorded/verified.

## What you should observe

The folder structure should be easy to recognize and the ISO hash should match the class source value.


## Validation checkpoint

Verify the ISO exists at the expected path and record its SHA-256 value before continuing.


## Expected end state

The physical host has a clean course workspace and a verified Windows Server installation source.


## What you will do

Create the standard course folder tree, place the Windows Server ISO in the expected location, and verify the ISO with SHA-256 before it is used to install any VM.
