# Module 01 — Advanced Multi-Layer Troubleshooting

## Introduction

Day 4 taught a repeatable incident method. Day 5 extends it to situations where several infrastructure layers may contribute to the same user-visible problem and where issues must be prioritized by operational risk.

## Goal

Apply the Day 4 method across several infrastructure layers.

## Concepts to keep in mind

A symptom can originate in the application, guest OS, virtual hardware, Hyper-V configuration, host, storage/network stack, outer infrastructure, or an external dependency. Correlation across layers is more useful than checking every tool in sequence.

## What you will do

Use the layer-isolation questions to work through several example symptoms and decide which evidence would rule layers in or out before any change.

## Hands-on / detailed content

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

## What you should observe

Some evidence narrows scope rather than proving root cause. For example, 'only one VM is affected' reduces the likelihood of a host-wide outage but does not identify the failed component by itself.

## Validation checkpoint

Given a multi-symptom incident, rank the issues by data-loss risk, outage, degraded resilience, performance, and warning-only impact.

## Expected end state

You can prioritize and structure a complex incident before touching configuration.
