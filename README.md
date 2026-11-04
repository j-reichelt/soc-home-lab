# SOC Home Lab — Segmented Network, AD, SIEM & Pen-Test Exercise

A self-built, segmented security operations lab: pfSense-based network segmentation, an Active Directory domain with tiered users/groups, a Wazuh SIEM ingesting Sysmon telemetry, and a live pen-test exercise run against an intentionally vulnerable target — with detections mapped to MITRE ATT&CK.

## Problem Statement

*(2–4 sentences: what you set out to prove/build and why):
I wanted hands-on evidence of segmentation design, identity management, and detection engineering beyond what a certification alone demonstrates, so I built a small enterprise network from scratch and ran a real attack against it to see what my own defenses would catch or miss.

## Architecture

![Network Diagram](diagrams/network-diagram.png)

| Segment | Subnet | Purpose |
|---|---|---|
| MGMT | 10.10.10.0/24 | Administrative access to pfSense |
| SERVERS | 10.10.20.0/24 | Domain controller, SIEM |
| WORKSTATIONS | 10.10.30.0/24 | Domain-joined clients |
| DMZ | 10.10.40.0/24 | Attacker (Kali) + intentionally vulnerable target (Metasploitable2) |

Segmentation is enforced at pfSense with a default-deny posture: WORKSTATIONS can only reach SERVERS on specific required ports (DNS, Kerberos, LDAP, SMB); DMZ cannot reach either internal segment at all, by default.

## Tools Used

- **VirtualBox** — hypervisor
- **pfSense CE** — firewall / router / network segmentation
- **Windows Server 2022** — Active Directory Domain Services, Group Policy
- **Windows 11** — domain-joined client workstations
- **Wazuh** — SIEM (manager, indexer, dashboard)
- **Sysmon** (SwiftOnSecurity config) — endpoint telemetry
- **Kali Linux** — attacker platform
- **Metasploitable2** — intentionally vulnerable target
- **Wireshark** — packet capture
- **Tailscale** — remote access for the colleague running the pen-test
- **Ansible** *(stretch goal)* — infrastructure-as-code for agent deployment / config backup

## What I Built

*(Checklist or short narrative per phase — fill in as you complete each one)*

- [x] Phase 0 — Prerequisites & environment prep
- [x] Phase 1 — pfSense network segmentation
- [ ] Phase 2 — Active Directory (domain, OUs, groups, users, GPO)
- [ ] Phase 3 — Client workstations, domain-joined
- [ ] Phase 4 — Wazuh SIEM deployment and agent enrollment
- [ ] Phase 5 — Attacker infrastructure (Kali + Metasploitable2) and remote access setup
- [ ] Phase 6 — Live pen-test exercise
- [ ] Phase 7 — Automated deployment (GPO-pushed Sysmon + Wazuh agent)
- [ ] Phase 8 — Detection write-up and MITRE ATT&CK mapping

Detailed, per-phase build notes with screenshots live in [`/docs/build-notes`](docs/build-notes).

## What I Detected

*(Fill in after Phase 6 — summarize the attack scenario(s) run, what Wazuh caught in real time, and reference the full mapping in [`docs/MITRE-ATTACK-mapping.md`](docs/MITRE-ATTACK-mapping.md))*

## What I'd Improve Next

*(Honest observations — e.g. missing log sources, detections that should exist but don't yet, segmentation rules that are still too permissive, anything the pen-test exercise exposed)*

## Repo Structure

```
.
├── README.md
├── docs/
│ ├── build-notes/ # per-phase write-ups with embedded screenshots
│ ├── RoE-template.md # Rules of Engagement used for the pen-test exercise
│ └── MITRE-ATTACK-mapping.md
├── diagrams/ # network diagram (draw.io source + exported image)
├── screenshots/ # organized by phase, referenced from build-notes
└── playbooks/ # Ansible playbooks (stretch goal)
```

## Sanitization Note

All usernames, passwords, and externally-meaningful IPs in this repo are placeholders. Internal lab IP ranges (10.10.x.x, 192.168.56.x) are safe to leave as-is since they're private/lab-only addressing with no real-world exposure.
