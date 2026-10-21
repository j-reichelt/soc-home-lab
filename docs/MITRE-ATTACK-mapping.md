# MITRE ATT&CK Mapping — Pen-Test Exercise Findings

Fill in one row per meaningful event (detected or missed) from the Phase 7 exercise.

| # | Technique ID | Technique Name | What the Attacker Did | Wazuh Rule Fired? | Detected? | Notes / Gap |
|---|---|---|---|---|---|---|
| 1 | T1046 | Network Service Discovery | nmap service scan against Metasploitable2 | *(rule ID/name or "none")* | Yes / No | |
| 2 | T1190 | Exploit Public-Facing Application | vsftpd backdoor exploitation | | | |
| 3 | T1110 | Brute Force | Credential brute-force against exposed service login | | | |
| 4 | | | | | | |

## Legend

- **Detected?** — did an alert actually fire and would a real analyst have seen it in time?
- **Notes / Gap** — if not detected, what log source or rule would be needed to catch it? If detected, any tuning needed (false positive risk, alert clarity, etc.)?

## Summary

*(2–3 sentences once the table is filled: overall detection coverage, the most important gap found, and the single improvement you'd prioritize first.)*
