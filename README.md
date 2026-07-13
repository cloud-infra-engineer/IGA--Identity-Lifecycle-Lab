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
![Architecture Diagram](iga-lifecycle-architecture.png)

## What I Built
[PLACEHOLDER — describe midPoint setup, connector configuration, data mapping 
between CSV attributes and LDAP schema, JML trigger logic]

## Troubleshooting & Problems I Hit
Issue: SSH access unclear (no username specified)
The lab guide said to SSH in using the IP and port, but didn't clearly state the login username — it was buried in a sentence rather than listed clearly. Had to reread the instructions carefully to spot that "using root" meant the username was root, not just descriptive text. Lesson: always check for a username explicitly before assuming SSH will prompt for one.
Issue: PuTTY copy-paste not working via trackpad

Struggled to copy commands from the browser instructions into the PuTTY terminal — right-click paste didn't work reliably on the laptop trackpad. Resolved by using the keyboard shortcut Shift+Insert to paste directly into PuTTY instead of relying on trackpad right-click/two-finger tap.
Issue: Login attempts failing / temporary lockout

Got locked out of SSH login temporarily after a few failed password attempts — standard SSH brute-force protection, not a fault with the VM. Waited a short time before retrying rather than repeatedly attempting, which resolved it once the correct username/password combination was confirmed.
Issue: midPoint service not showing as "up" after starting the lab

After running the start script, phpLDAPadmin and SimplifyHR came up immediately, but midPoint did not, and the web console at localhost:8080/midpoint returned a connection error. This wasn't an actual failure — midPoint takes noticeably longer to boot than the other services on first startup, since it initializes its own internal database and connectors. Re-checking the status script and refreshing the browser after waiting a few minutes confirmed midPoint had come up successfully. Lesson: don't assume a service has failed just because it isn't immediately up — check again after allowing more startup time, especially for Java-based platforms like midPoint.

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
