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

**Joiner workflow (in progress):** Built out the first stage of the Joiner-Mover-Leaver lifecycle — the Joiner process — which follows this flow:

1. A new employee record is added directly in the HR source (CSV) — the system of record for identity data (name, department, role, etc.).
2. An HR reconciliation task in midPoint pulls that new record in, creating the corresponding identity inside midPoint.
3. Verified the new identity appeared correctly in midPoint after running the HR reconciliation task.
   
 ![Lucas Weber Joiner Event](Lucas%20Webb%20-%20Joiner.png)

5. A second reconciliation task, run against the OpenLDAP resource, pushes that identity out to the LDAP directory, creating the actual account.
6. Verified the account appeared correctly in LDAP using an LDAP browser.

**Note on automatic provisioning:** Although the lab instructions describe reconciling HR and then separately reconciling OpenLDAP as two distinct manual steps, in practice a single HR reconciliation task was sufficient to both create the identity in midPoint and automatically provision the corresponding account into OpenLDAP — confirmed by checking the account directly in phpLDAPadmin. This reflects how midPoint's resource linking actually works: once a source system is reconciled, provisioning to any linked target resources happens automatically as part of that same lifecycle event, rather than requiring a manual reconciliation per target. This is arguably the more realistic model of automation — a single trigger event (a new HR record) cascades through the system without requiring manual intervention at each downstream target.

This demonstrates the core mechanic of automated provisioning: identity data flows one-way from the authoritative HR source, through midPoint's governance and transformation layer, out to the target directory — with no manual account creation required at any point in the chain.

**Design consideration — reconciliation frequency:** In this lab, reconciliation was triggered manually to clearly observe each stage of the JML flow as it happened. In a production environment, this would typically run on a scheduled interval rather than manually — the appropriate frequency depends on the organization's size and rate of change. A large enterprise with high hire/leaver volume might reconcile every 15–30 minutes to minimize the window where access is out of date, whereas a smaller organization might run it nightly. This is a tuning decision balanced against system load versus how urgently new access or account disablement needs to take effect.

*(Mover and Leaver stages, plus attribute-mapping detail and any further transformation logic required, to be added as the lab progresses.)*

## Troubleshooting & Problems I Hit

**Issue: SSH access unclear (no username specified)**
The lab guide said to SSH in using the IP and port, but didn't clearly state the login username — it was buried in a sentence rather than listed clearly. Had to reread the instructions carefully to spot that "using root" meant the username was root, not just descriptive text. Lesson: always check for a username explicitly before assuming SSH will prompt for one.

**Issue: PuTTY copy-paste not working via trackpad**
Struggled to copy commands from the browser instructions into the PuTTY terminal — right-click paste didn't work reliably on the laptop trackpad. Resolved by using the keyboard shortcut Shift+Insert to paste directly into PuTTY instead of relying on trackpad right-click/two-finger tap.

**Issue: Login attempts failing / temporary lockout**
Got locked out of SSH login temporarily after a few failed password attempts — standard SSH brute-force protection, not a fault with the VM. Waited a short time before retrying rather than repeatedly attempting, which resolved it once the correct username/password combination was confirmed.

**Issue: midPoint service not showing as "up" after starting the lab**
After running the start script, phpLDAPadmin and SimplifyHR came up immediately, but midPoint did not, and the web console at localhost:8080/midpoint returned a connection error. This wasn't an actual failure — midPoint takes noticeably longer to boot than the other services on first startup, since it initializes its own internal database and connectors. Re-checking the status script and refreshing the browser after waiting a few minutes confirmed midPoint had come up successfully. Lesson: don't assume a service has failed just because it isn't immediately up — check again after allowing more startup time, especially for Java-based platforms like midPoint.

**Issue: Reconciliation task creation UI substantially different from lab guide**
The lab guide's instructions for creating and running an HR reconciliation task (Resources → SimplifyHR → Run Task, with fields for Task name and a Schedule → Run now option) didn't match the current midPoint UI at almost every step. In my version, task creation is done via a separate "Server Tasks" menu → "New Task" → selecting "Reconciliation" as the task type, with configuration split across Basic, Activity, Schedule, Advanced options, and Operational attributes tabs. There was no visible "Run now" option under Schedule, and no dedicated task name field as described — instead, a "Save and Run" button under Operational attributes handled both actions at once.

Resolved by working through each tab systematically to identify the functional equivalent of each step described in the guide, rather than relying on exact button names. Confirmed success by checking that the HR record created earlier correctly appeared in midPoint's Users section after running the task.

## Business Outcome

Automated provisioning and deprovisioning eliminates the manual gap that leads to orphaned accounts, and provides a full audit trail of every identity change — directly reducing the compliance risk described above.
To apply this: open README.md, click edit, select all existing text and delete it, then paste this whole block in, and commit. That replaces everything in one clean pass rather than three separate edits.
