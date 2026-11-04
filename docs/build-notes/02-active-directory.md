# Phase 2 — Active Directory (DC01)

## What I Built

- VM: `DC-Rebuild` (VirtualBox name) — Windows Server 2022 (64-bit), 4096 MB RAM, 2 CPUs, 60 GB disk, single NIC on `intnet-servers`.
- Static IP `10.10.20.10/24`, gateway `10.10.20.1` (pfSense SERVERS interface), DNS `127.0.0.1` (becomes its own DNS server once promoted).
- Computer renamed to `DC01` after first login, before promotion.
- Installed the **Active Directory Domain Services** role via Server Manager → Add Roles and Features.

![VirtualBox VM details pane for DC-Rebuild showing Windows Server 2022, 4096 MB RAM, 2 CPUs, 60 GB disk, intnet-servers](../../screenshots/phase2/01-dc-vm-specs.png)
*VM specs for reference/rebuild purposes.*

### Domain Promotion

Server Manager flags the post-deployment configuration step right after the AD DS role finishes installing:

![Server Manager Dashboard with the post-deployment notification to promote this server to a domain controller](../../screenshots/phase2/10-server-manager-post-deployment-notification.png)
*Post-deployment configuration flag — triggers the "Promote this server to a domain controller" wizard.*

- Deployment: **Add a new forest**.
- Root domain name: `corp.local` (NetBIOS name: `CORP`).
- Forest / domain functional level: Windows Server 2016.
- Domain controller capabilities: DNS Server ✅, Global Catalog ✅, RODC ❌.
- Set a Directory Services Restore Mode (DSRM) password, reviewed the configuration summary, then installed and rebooted.

![AD DS Configuration Wizard — Domain Controller Options screen with DNS Server and Global Catalog checked](../../screenshots/phase2/11-adds-wizard-domain-controller-options.png)
*Domain Controller Options: forest/domain functional level and DSRM password.*

![AD DS Configuration Wizard — Review Options screen confirming corp.local as a new forest, NetBIOS CORP, DNS Server: Yes](../../screenshots/phase2/12-adds-wizard-review-options.png)
*Final review before installation — new forest `corp.local`, NetBIOS `CORP`, DNS and Global Catalog enabled.*

![AD DS Configuration Wizard — Prerequisites Check screen, all checks passed successfully](../../screenshots/phase2/13-adds-wizard-prerequisites-check.png)
*Prerequisites check passed (with the expected NT4-compatibility and DNS-delegation warnings, which don't apply to a single-forest lab).*

### Post-Promotion State

Right after the reboot, the domain exists but is still empty — no OUs, groups, or users yet:

![AD Users and Computers showing a freshly-promoted, empty corp.local domain with no OUs yet](../../screenshots/phase2/14-ad-users-computers-initial-empty-domain.png)
*corp.local immediately after promotion — baseline state before building out the OU structure.*

![Server Manager Local Server properties for DC — computer name DC, domain corp.local, IP 10.10.20.10, Windows Server 2022 Standard Evaluation](../../screenshots/phase2/15-local-server-properties.png)
*Local Server properties, confirming hostname, domain membership, and networking post-promotion.*

## OUs, Groups, Users

Structure built in Active Directory Users and Computers (`dsa.msc`) under `corp.local`:

- **OUs**: Servers, Workstations, Groups, Users (sub-OUs: Standard-Users, Privileged-Users, Service-Accounts)
- **Security groups**: SG-DomainAdmins, SG-HelpDesk, SG-Finance, SG-HR, SG-AllUsers (Global, Security)
- **Users** (~8–10 total): 1–2 Domain Admins, 1 Help Desk / Tier-1 account, 5–6 standard users spread across Finance/HR/general groups, 1–2 service accounts

![Full Active Directory Users and Computers OU tree — Builtin, Computers, Domain Controllers, Users, Servers, Workstations, Groups, Corp-users with Standard-Users/Privileged-Users/Service-Accounts sub-OUs](../../screenshots/phase2/16-ad-users-computers-ou-tree-full.png)
*Complete OU layout under `corp.local`, including the three-tier user structure under Corp-users.*

![Groups OU showing the five security groups — SG-AllUsers, SG-DomainAdmins, SG-Finance, SG-HelpDesk, SG-HR](../../screenshots/phase2/17-groups-ou-security-groups.png)
*Security groups created under the Groups OU.*

| Full Name | Logon Name | Group(s) |
|---|---|---|
| Maria Chen | mchen | SG-Finance, SG-AllUsers |
| David Okafor | dokafor | SG-Finance, SG-AllUsers |
| Rachel Kim | rkim | SG-Finance, SG-AllUsers |
| James Whitfield | jwhitfield | SG-HR, SG-AllUsers |
| Priya Natarajan | pnatarajan | SG-HR, SG-AllUsers |
| Tom Alvarez | talvarez | SG-HR, SG-AllUsers |

![Standard-Users OU with the six standard user accounts](../../screenshots/phase2/18-standard-users-ou.png)
*Standard-Users OU — day-to-day accounts with no elevated rights.*

![Reference table of standard user accounts, logon names, and their assigned groups](../../screenshots/phase2/09-user-account-reference-table.png)
*Quick-reference table used while provisioning accounts in AD Users and Computers.*

Alongside the standard tier, a separate **Privileged-Users** OU holds the small set of accounts with elevated (Domain Admin / Tier-0) rights — kept apart from day-to-day accounts so Restricted Groups and tighter auditing can target them specifically:

![Privileged-Users OU with the privileged/admin-tier accounts — Lena Petrova, Marcus Dub…, Samantha R…](../../screenshots/phase2/19-privileged-users-ou.png)
*Privileged-Users OU — separated from Standard-Users so GPOs and auditing can treat admin-tier accounts differently.*

A **Service-Accounts** OU holds the non-interactive accounts used by other parts of the lab (the Wazuh agent enrollment and backup jobs):

![Service-Accounts OU with svc-wazuh and svc-backup](../../screenshots/phase2/20-service-accounts-ou.png)
*Service-Accounts OU — `svc-wazuh` and `svc-backup`, kept separate from human accounts.*

## Group Policy

GPO `SOC-Baseline-Security` linked to the Workstations OU:

![Group Policy Management console at the Forest: corp.local root, before drilling into the GPO](../../screenshots/phase2/21-gpm-forest-root-view.png)
*Group Policy Management — forest root view, showing the corp.local domain and OU structure GPOs attach to.*

- **Password policy**: minimum password length set to 12 characters.

![Minimum password length policy setting — 12 characters](../../screenshots/phase2/22-gpo-minimum-password-length.png)
*Minimum password length, set under Account Policies → Password Policy.*

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
