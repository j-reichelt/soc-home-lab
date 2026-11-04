# Phase 2 — Active Directory (DC01)

## What I Built

- VM: `DC-Rebuild` (VirtualBox name) — Windows Server 2022 (64-bit), 4096 MB RAM, 2 CPUs, 60 GB disk, single NIC on `intnet-servers`.
- Static IP `10.10.20.10/24`, gateway `10.10.20.1` (pfSense SERVERS interface), DNS `127.0.0.1` (becomes its own DNS server once promoted).
- Computer renamed to `DC01` after first login, before promotion.
- Installed the **Active Directory Domain Services** role via Server Manager → Add Roles and Features.

![VirtualBox VM details pane for DC-Rebuild showing Windows Server 2022, 4096 MB RAM, 2 CPUs, 60 GB disk, intnet-servers](../../screenshots/phase2/01-dc-vm-specs.png)
*VM specs for reference/rebuild purposes.*

### Domain Promotion

- Deployment: **Add a new forest**.
- Root domain name: `corp.local` (NetBIOS name: `CORP`).
- Forest / domain functional level: Windows Server 2016.
- Domain controller capabilities: DNS Server ✅, Global Catalog ✅, RODC ❌.
- Set a Directory Services Restore Mode (DSRM) password, reviewed the configuration summary, then installed and rebooted.

## OUs, Groups, Users

Structure built in Active Directory Users and Computers (`dsa.msc`) under `corp.local`:

- **OUs**: Servers, Workstations, Groups, Users (sub-OUs: Standard-Users, Privileged-Users, Service-Accounts)
- **Security groups**: SG-DomainAdmins, SG-HelpDesk, SG-Finance, SG-HR, SG-AllUsers (Global, Security)
- **Users** (~8–10 total): 1–2 Domain Admins, 1 Help Desk / Tier-1 account, 5–6 standard users spread across Finance/HR/general groups, 1–2 service accounts

| Full Name | Logon Name | Group(s) |
|---|---|---|
| Maria Chen | mchen | SG-Finance, SG-AllUsers |
| David Okafor | dokafor | SG-Finance, SG-AllUsers |
| Rachel Kim | rkim | SG-Finance, SG-AllUsers |
| James Whitfield | jwhitfield | SG-HR, SG-AllUsers |
| Priya Natarajan | pnatarajan | SG-HR, SG-AllUsers |
| Tom Alvarez | talvarez | SG-HR, SG-AllUsers |

![Reference table of standard user accounts, logon names, and their assigned groups](../../screenshots/phase2/09-user-account-reference-table.png)
*Quick-reference table used while provisioning accounts in AD Users and Computers.*

## Group Policy

GPO `SOC-Baseline-Security` linked to the Workstations OU:

- **Account lockout policy**: lockout threshold set to 5 invalid logon attempts.

![Account Lockout Threshold policy setting — 5 invalid logon attempts](../../screenshots/phase2/02-gpo-account-lockout-threshold.png)
*Account lockout threshold, set under Account Policies → Account Lockout Policy.*

- **Advanced Audit Policy**: Logon, Account Logon, Object Access, and Privilege Use categories enabled (Success and Failure) — this is what feeds the SIEM with meaningful events.

![Advanced Audit Policy — Audit Logon set to Success and Failure](../../screenshots/phase2/03-gpo-audit-logon.png)
*Audit Logon subcategory, enabled for both Success and Failure.*

![Advanced Audit Policy — Account Logon category with Credential Validation and Kerberos subcategories configured](../../screenshots/phase2/04-gpo-audit-account-logon-category.png)
*Account Logon category: Credential Validation and Kerberos Authentication Service auditing enabled.*

![Advanced Audit Policy — Object Access category with Audit File System set to Success and Failure](../../screenshots/phase2/05-gpo-audit-object-access-category.png)
*Object Access category: File System auditing enabled, to catch unauthorized file access attempts.*

![Advanced Audit Policy — Privilege Use category with Audit Sensitive Privilege Use set to Success and Failure](../../screenshots/phase2/06-gpo-audit-privilege-use-category.png)
*Privilege Use category: Sensitive Privilege Use auditing enabled, to catch privilege escalation attempts.*

*(Caption: pair these with a later screenshot of the resulting events showing up in Wazuh to demonstrate cause and effect.)*

- **Restricted Groups**: standard users removed from the local Administrators group on client machines, enforced via Restricted Groups on the built-in Administrators group.

![Restricted Groups — Administrators (built-in) Properties dialog in the GPMC](../../screenshots/phase2/07-gpo-restricted-groups-administrators.png)
*Restricted Groups policy for the built-in Administrators group.*

- GPO linked to the Workstations OU, enabled, not enforced.

![Group Policy Management console showing SOC-Baseline-Security linked to the Workstations OU](../../screenshots/phase2/08-gpo-linked-workstations-ou.png)
*SOC-Baseline-Security GPO linked and enabled on the Workstations OU.*
