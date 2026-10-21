# Rules of Engagement — SOC Home Lab Pen-Test Exercise

**Date / Time Window:** *(e.g. Saturday, [date], 2:00 PM – 4:00 PM ET)*

**Participants:**
- Defender / Lab Owner: *(you)*
- Attacker: *(colleague's name)*

## Scope

**In scope:**
- Target: Metasploitable2 VM — IP `10.10.40.x` (confirm exact IP at test time)
- Attack path: via Kali VM (`10.10.40.x`), reached over Tailscale SSH/VNC
- Techniques: standard reconnaissance (nmap scans), exploitation of known Metasploitable2 vulnerabilities (e.g. vsftpd backdoor, Samba misconfig), brute-force attempts against exposed service logins

**Out of scope:**
- The pfSense firewall itself (no attempts to exploit, crash, or exhaust its resources)
- Any segment other than DMZ (SERVERS, WORKSTATIONS, MGMT) — segmentation should prevent this anyway, but it is explicitly not an authorized target
- The host laptop / hypervisor itself
- Any destructive action beyond what's needed to demonstrate exploitation (no wiping, no ransomware simulation beyond what's pre-agreed)

## Stop Condition

Either party may end the exercise immediately by sending the message/phrase: **"STOP TEST"** in the agreed communication channel (e.g. a shared chat). No justification required — exercise ends immediately on this signal.

## Communication

- Primary channel: *(e.g. a Discord/Slack call, or a shared text thread)*
- Defender confirms Wazuh dashboard and Wireshark capture are running and ready *before* giving the go-ahead
- Attacker confirms Tailscale connection and SSH/VNC access *before* starting active testing

## Success Criteria

This isn't pass/fail — the goal is detection coverage insight. Useful outcomes either way:
- Attack technique produces a clear Wazuh alert → confirms detection works
- Attack technique goes unnoticed → identifies a real gap to document and (optionally) fix

## Post-Test

- Attacker provides a brief rundown of exactly what was attempted, in order, with approximate timestamps
- Defender exports/screenshots the relevant Wazuh alerts and stops the Wireshark capture
- Both debrief: what was expected to be caught vs. what actually was
