# Module 01 — Troubleshooting Method

## Introduction

Good troubleshooting is a decision process, not a collection of commands. This module gives you a repeatable structure that keeps evidence separate from assumptions and prevents random configuration changes.

## Goal

Use the same repeatable process for every incident.

~~~text
1. Identify symptom
2. Define scope
3. Establish timeline and impact
4. Collect evidence
5. Build hypotheses
6. Test one hypothesis at a time
7. Apply corrective action
8. Validate
9. Document
~~~


## Concepts to keep in mind

A symptom describes what is observed; a hypothesis proposes why. Scope, timeline, impact, and recent changes help rank hypotheses before any fix is attempted.

## Problem statement

Use:

~~~text
What:
Where:
When:
Scope:
Impact:
Expected behavior:
Observed behavior:
Recent changes:
~~~

Separate symptoms from assumptions.

Before a disruptive action, ask whether it will destroy useful evidence.

## Validation

Given a vague statement such as "the network is broken", rewrite it as a specific observable symptom.


## What you will do

Take vague problem statements and rewrite them into specific, testable descriptions using What/Where/When/Scope/Impact/Expected/Observed/Recent changes.


## What you should observe

The clearer the problem statement becomes, the fewer unrelated components need to be investigated first.


## Validation checkpoint

For a sample incident, state one observation and one hypothesis without mixing them together.


## Expected end state

You have a troubleshooting framework that will be reused for every Day 4 scenario.
