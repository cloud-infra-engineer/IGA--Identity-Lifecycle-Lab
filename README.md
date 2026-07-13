# Identity Governance & Lifecycle Automation (IGA Lab)

## Business Problem
Manual onboarding and offboarding processes create security and compliance risk. 
When employees join, move roles, or leave, manual account creation/updates/removal 
is slow and error-prone — commonly resulting in orphaned accounts (active credentials 
left behind after someone leaves), which are a frequent finding in security audits 
and a real attack vector.

## Solution Overview
Built an automated Joiner-Mover-Leaver (JML) identity lifecycle workflow using 
midPoint as the identity governance engine, integrating a simulated HR data source 
(CSV) with an OpenLDAP directory as the target system.

## Architecture
![Architecture Diagram](iga-lab-architecture.png)

## What I Built
[PLACEHOLDER — describe midPoint setup, connector configuration, data mapping 
between CSV attributes and LDAP schema, JML trigger logic]

## Troubleshooting & Problems I Hit
[PLACEHOLDER — document each real issue as: what broke, how you diagnosed it, 
what you changed, how you confirmed the fix. E.g. attribute type mismatches 
between CSV fields and LDAP schema.]

## Business Outcome
Automated provisioning and deprovisioning eliminates the manual gap that leads 
to orphaned accounts, and provides a full audit trail of every identity change — 
directly reducing the compliance risk described above.

## Scope Note
This project focuses on the core mechanics of identity governance and lifecycle 
automation — specifically, automated Joiner-Mover-Leaver (JML) provisioning and 
deprovisioning. 

Deliberately out of scope for this project: access reviews, entitlement 
certification, and broader identity governance features. These are covered 
separately in a later project using Entra ID Governance, once integrated into 
the full hybrid enterprise environment. This scoping decision keeps each project 
focused and demonstrable in depth, rather than attempting to cover every IAM 
capability shallowly across a single lab.
