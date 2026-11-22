# Phase 7 — Automated Deployment

> 🚧 **Planned — not yet implemented.** This phase is part of the lab roadmap.
> The automation proof screenshot will be added here once complete.

## Sysmon via GPO

*(Startup script pushing Sysmon + SwiftOnSecurity config to Workstations/Servers OUs.)*

## Wazuh Agent via GPO

*(GPO Software Installation or startup script pushing the Wazuh agent MSI. Target proof:
a freshly domain-joined VM rebooting and appearing as an Active agent with zero manual
steps — the best automation proof point.)*

## Infrastructure-as-Code (stretch goal)

*(Ansible playbook(s) — link to /playbooks. What they automate: Wazuh agent install on Linux targets, and/or pfSense config backup via XML export API.)*
