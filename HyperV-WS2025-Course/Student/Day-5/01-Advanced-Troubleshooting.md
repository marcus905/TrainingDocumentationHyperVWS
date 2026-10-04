# Module 01 — Advanced Multi-Layer Troubleshooting

## Goal

Apply the Day 4 method across several infrastructure layers.

~~~text
Application
   |
Guest OS
   |
Virtual CPU / Memory / Disk / NIC
   |
Hyper-V configuration
   |
Hyper-V host
   |
Windows networking / storage
   |
Physical / outer infrastructure
   |
External services
~~~

Ask:

- one VM or many?
- does the symptom follow the VM or stay with the host?
- one switch or all networking?
- guest pressure or host pressure?
- local VM storage or shared host storage?
- what changed recently?

## Prioritization

Typical order:

1. data-loss risk;
2. total outage;
3. degraded recovery/resilience;
4. severe performance;
5. warnings;
6. optimization.

Do not optimize a low-risk issue while a higher-risk failure remains unresolved.
