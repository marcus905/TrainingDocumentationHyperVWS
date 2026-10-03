# Windows Server 2025 & Hyper-V — 5-Day Training Course

## Purpose
Working documentation for a five-day remote course focused on Windows Server 2025 and Hyper-V. The lab is based on nested virtualization so every participant can reproduce the same environment.

## Course structure
| Day | Focus | Main outcome |
|---|---|---|
| Day 0 | Lab prerequisites | Student workstation and nested hosts ready |
| Day 1 | Windows Server 2025 fundamentals | Operational Windows Server host |
| Day 2 | Hyper-V fundamentals | Functional nested Hyper-V environment |
| Day 3 | Advanced Hyper-V | Checkpoints, Replica, storage and performance |
| Day 4 | Structured troubleshooting | Evidence-based diagnosis and root-cause analysis |
| Day 5 | Advanced scenarios | End-to-end troubleshooting, migration concepts and best practices |

## Documents
- [00 - Lab Prerequisites](00-Lab-Prerequisites.md)
- [01 - Day 1 - Windows Server 2025](01-Day-1-Windows-Server-2025.md)
- [02 - Day 2 - Hyper-V Fundamentals](02-Day-2-Hyper-V-Fundamentals.md)
- [03 - Day 3 - Advanced Hyper-V](03-Day-3-Advanced-Hyper-V.md)
- [04 - Day 4 - Structured Troubleshooting](04-Day-4-Troubleshooting.md)
- [05 - Day 5 - Advanced Scenarios, Migration and Best Practices](05-Day-5-Advanced-Scenarios-Migration-Best-Practices.md)
- [Instructor Guide](Instructor-Guide.md)

## Lab topology
~~~text
PHYSICAL STUDENT MACHINE
Windows 11 Pro/Enterprise or Windows Server
Hyper-V
|
+-- HV01 - Windows Server 2025
|   +-- DC01
|   +-- SRV01
|   +-- CLIENT01
|
+-- HV02 - Windows Server 2025
    +-- Replica workloads
~~~

## Design principles
1. Build one environment and evolve it throughout the week.
2. Demonstrate GUI and PowerShell workflows.
3. Verify every configuration change.
4. Introduce controlled faults after successful configuration.
5. Troubleshoot from evidence rather than assumptions.
6. Treat checkpoints, replication and high availability as distinct technologies.
7. Prefer repeatable configuration and documentation.

## Documentation baseline
Microsoft Learn is the authoritative technical reference.

- Hyper-V requirements: https://learn.microsoft.com/windows-server/virtualization/hyper-v/host-hardware-requirements
- Nested virtualization: https://learn.microsoft.com/windows-server/virtualization/hyper-v/enable-nested-virtualization
- Windows Server installation: https://learn.microsoft.com/windows-server/get-started/install-windows-server
- Server Core vs Desktop Experience: https://learn.microsoft.com/windows-server/get-started/getting-started-with-server-core
