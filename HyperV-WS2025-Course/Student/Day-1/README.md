# Day 1 — Windows Server 2025 Fundamentals


## Concepts to keep in mind

The same pattern will repeat all week: understand the layer, inspect current state, make a controlled change, verify the result, and keep enough evidence to troubleshoot if the result is unexpected.

## Introduction

Day 1 establishes the Windows Server habits the rest of the course relies on. The focus is not just making HV01 work, but learning how to inspect, configure, verify, and troubleshoot a server before Hyper-V is introduced.

## Sequence

1. [PowerShell essentials](01-PowerShell-Essentials.md)
2. [Architecture and Windows Server 2025](02-Architecture-and-WS2025.md)
3. [Initial server configuration](03-Initial-Configuration.md)
4. [Roles, features and tools](04-Roles-Features-and-Tools.md)
5. [Networking](05-Networking.md)
6. [Local storage](06-Local-Storage.md)
7. [Stage installation media](07-Stage-Installation-Media.md)
8. [Break/Fix — DNS](08-Break-Fix-DNS.md)
9. [Day 1 check](99-Day-1-Check.md)

## Expected end state

HV01 has a validated Windows Server configuration, a D: Hyper-V data volume, the course folder structure, the Windows Server ISO staged inside HV01, and a known-good outer network.

## What you will do

Follow the modules in order so that networking, storage, PowerShell, and installation media are all ready before Day 2.


## What you should observe

At the end of the day, HV01 should be a known-good Windows Server platform rather than merely an installed operating system.


## Validation checkpoint

Use the Day 1 validation module before proceeding to Hyper-V.
