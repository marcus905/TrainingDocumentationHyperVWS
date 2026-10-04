# Module 08 — Security and Recurring Patterns

## Security software

Antivirus, EDR, backup agents, file-system filters and network inspection can affect Hyper-V behavior.

Do not blindly disable security controls.

Use:

1. evidence;
2. current Microsoft/vendor guidance;
3. the smallest safe change;
4. retest;
5. document security impact.

## Recurring patterns

### VM will not start

Check VM state, VMMS, VHDX availability, free storage, memory, switch references and Hyper-V events.

### VM is slow

Check guest process/workload, vCPU, memory pressure, disk latency, checkpoint chain and host contention.

### VM has no network

Check vNIC, switch, IP, gateway, NAT/routing, DNS, firewall and application port.

### Replica unhealthy

Check replication state, name resolution, HTTPS port, certificates, target capacity and authorization.

Legacy configuration is not automatically wrong. First prove whether it contributes to the symptom.
