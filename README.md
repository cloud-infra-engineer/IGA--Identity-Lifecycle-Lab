# Identity Governance & Lifecycle Automation (IGA Lab)

## Business Problem

Manual account provisioning is a reasonable way to manage identity in a small organisation — with ten or twenty employees, it's realistic to track who joins, moves role, or leaves by hand, since the volume of change is low enough to manage manually. That changes at scale. Once an organisation grows to hundreds or thousands of employees, manual onboarding and offboarding processes create real security and compliance risk. When employees join, move roles, or leave, manual account creation/updates/removal becomes slow and error-prone — commonly resulting in orphaned accounts (active credentials left behind after someone leaves), which are a frequent finding in security audits and a real attack vector.

## Solution Overview

Built an automated Joiner-Mover-Leaver (JML) identity lifecycle workflow using midPoint as the identity governance engine, integrating a simulated HR data source (CSV) with an OpenLDAP directory as the target system. The specific tools used here — midPoint and OpenLDAP — are illustrative rather than prescriptive; the same lifecycle pattern applies regardless of which IGA engine or directory service sits underneath it (e.g. SailPoint, Active Directory, Entra ID). What this project demonstrates is the underlying identity governance logic — a defined source of truth, automated reconciliation, and lifecycle-driven provisioning — rather than proficiency in any single vendor's product.

## Scope Note

This project focuses on the core mechanics of identity governance and lifecycle automation — specifically, automated Joiner-Mover-Leaver (JML) provisioning and deprovisioning.

Deliberately out of scope for this project: access reviews, entitlement certification, and broader identity governance features. These are covered separately in a later project using Entra ID Governance, once integrated into the full hybrid enterprise environment. This scoping decision keeps each project focused and demonstrable in depth, rather than attempting to cover every IAM capability shallowly across a single lab.

## Architecture

![Architecture Diagram](iga-lifecycle-architecture.png)

## What I Built

I configured midPoint as the identity governance engine sitting between a simulated HR source system (CSV-based) and an OpenLDAP directory as the target.

**Connectivity setup:** Configured and tested both resource connections in midPoint — the HR CSV source and the OpenLDAP target — confirming successful communication with each before building any workflow logic on top ("Test Connection succeeded" on both).

**Joiner workflow:** Built out the first stage of the Joiner-Mover-Leaver lifecycle — the Joiner process — which follows this flow:

A new employee record is added directly in the HR source (CSV) — the system of record for identity data (name, department, role, etc.). An HR reconciliation task in midPoint pulls that new record in, creating the corresponding identity inside midPoint. Verified the new identity appeared correctly in midPoint after running the HR reconciliation task.

![Lucas Weber Joiner Event](Lucas%20Webb%20-%20Joiner.png)

A second reconciliation task, run against the OpenLDAP resource, pushes that identity out to the LDAP directory, creating the actual account. Verified the account appeared correctly in LDAP using an LDAP browser.

![Lucas Weber in LDAP](Lucas-Webber-ldap.png)

**Note on automatic provisioning:** Although the lab instructions describe reconciling HR and then separately reconciling OpenLDAP as two distinct manual steps, in practice a single HR reconciliation task was sufficient to both create the identity in midPoint and automatically provision the corresponding account into OpenLDAP — confirmed by checking the account directly in phpLDAPadmin.

This reflects how midPoint's resource linking actually works: once a source system is reconciled, provisioning to any linked target resources happens automatically as part of that same lifecycle event, rather than requiring a manual reconciliation per target. This is arguably the more realistic model of automation — a single trigger event (a new HR record) cascades through the system without requiring manual intervention at each downstream target.

This demonstrates the core mechanic of automated provisioning: identity data flows one-way from the authoritative HR source, through midPoint's governance and transformation layer, out to the target directory — with no manual account creation required at any point in the chain.

**Mover workflow (conceptual):** The Mover stage of the JML lifecycle covers an existing employee changing department or role, rather than joining or leaving the organisation. This lab's SimplifyHR portal only supports adding a new employee (Joiner) and terminating one (Leaver) — there's no function to edit an existing employee's department, so this stage isn't backed by screenshots the way Joiner, Leaver, and Reactivation are.

The process itself,
