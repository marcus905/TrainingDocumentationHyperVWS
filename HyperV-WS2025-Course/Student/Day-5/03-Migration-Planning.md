# Module 03 — Migration Planning

## Goal

Plan migration before touching the source workload.

## Inventory

Record:

- VM name/owner/purpose;
- guest OS;
- vCPU/memory;
- disks and formats;
- firmware/boot mode;
- network/VLAN/IP/DNS;
- application dependencies;
- backup/monitoring/security agents;
- RPO/RTO;
- acceptable downtime.

## Compatibility

Check guest support, BIOS/UEFI requirements, disk conversion, licensing dependencies, static MAC requirements, VLAN/IP recreation, source hypervisor tools/drivers, Secure Boot and time behavior.

## Dependency map

A VM can depend on DNS, databases, file shares, certificates, firewalls, monitoring and backup.

## Cutover

~~~text
Pre-checks
 -> quiesce/shutdown
 -> convert/move
 -> Hyper-V configuration
 -> network cutover
 -> application validation
 -> rollback decision
~~~

## Validation

A successful boot is not enough. Validate application function, authentication, networking, disk visibility, time, backup, monitoring, security tooling, performance and recovery.
