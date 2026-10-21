# Phase 2 — Active Directory (DC01)

## What I Built

*(DC01 specs, domain name corp.local, promotion process.)*

![AD promotion wizard](../../screenshots/phase2/ad-promotion.png)

## OUs, Groups, Users

*(OU tree: Servers, Workstations, Groups, Users > Standard-Users/Privileged-Users/Service-Accounts. Security groups created. ~8-10 accounts across tiers.)*

![AD Users and Computers OU tree](../../screenshots/phase2/ad-ou-tree.png)

## Group Policy

*(SOC-Baseline-Security GPO: password policy, advanced audit policy categories enabled, local admin restriction on clients.)*

![Advanced Audit Policy](../../screenshots/phase2/audit-policy.png)

*(Caption: pair this with a later screenshot of the resulting events showing up in Wazuh to demonstrate cause and effect.)*
