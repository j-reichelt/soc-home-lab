# Phase 5 — Automated Deployment

## Sysmon via GPO

*(Startup script pushing Sysmon + SwiftOnSecurity config to Workstations/Servers OUs.)*

## Wazuh Agent via GPO

*(GPO Software Installation or startup script pushing the Wazuh agent MSI.)*

![Zero-touch agent enrollment](../../screenshots/phase5/auto-enroll.png)

*(Caption: freshly domain-joined VM rebooting and appearing as an Active agent with zero manual steps — your best automation proof point.)*

## Infrastructure-as-Code (stretch goal)

*(Ansible playbook(s) — link to /playbooks. What they automate: Wazuh agent install on Linux targets, and/or pfSense config backup via XML export API.)*
