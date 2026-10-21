# Diagrams

Put your draw.io source file here (e.g. `network-diagram.drawio`) alongside the exported image (`network-diagram.png`) that the main README embeds.

Suggested contents for the diagram (Phase 8 task):
- All 4 pfSense segments (MGMT, SERVERS, WORKSTATIONS, DMZ) with subnet labels
- Every VM placed in its correct segment
- Firewall boundaries clearly marked between segments
- Arrows/notes showing what's explicitly allowed to cross segments (e.g. WORKSTATIONS → SERVERS: DNS/Kerberos/LDAP/SMB only)
