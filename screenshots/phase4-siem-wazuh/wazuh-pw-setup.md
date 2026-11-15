# wazuh-pw-setup — Wazuh Credentials & Access Reference

**Project:** SOC Home Lab
**Host:** SIEM01 — Ubuntu Server 24.04 LTS — `10.10.20.20`
**Component:** Wazuh 4.14 all-in-one (Manager · Indexer · Dashboard)
**Captured:** 2026-11-14

> ⚠️ **Sensitive — keep out of GitHub and any shared location.** Store this in a
> password manager. If any of these end up public, rotate them.

---

## Dashboard login (the one you use daily)

| Field | Value |
|-------|-------|
| URL | `https://10.10.20.20` (accept the self-signed cert warning) |
| Username | `admin` |
| Password | `2E0ScDGcX?jdIOtR5dCv962x?8y+.44D` |

---

## Wazuh indexer — internal users

| User | Purpose | Password |
|------|---------|----------|
| `admin` | Web UI + indexer admin (dashboard login) | `2E0ScDGcX?jdIOtR5dCv962x?8y+.44D` |
| `anomalyadmin` | Anomaly detection user for the web UI | `U?mG4fu3JCnXbqMQxA0qY5z?GSVPo86*` |
| `kibanaserver` | Dashboard ↔ indexer connection | `KPI9FM6zDMzqmOCfP7veHp9Q.VvTutuI` |
| `kibanaro` | Read-only dashboard user (.kibana index) | `dVQhDQAvUhGkx4EW6fsva7dz0uQu??+G` |
| `logstash` | Filebeat CRUD on Wazuh indices | `3ydurGlJU6v85?v*BvjaDtnz2Nbd4qJk` |
| `readall` | READ access to all indices | `I5TdPBBE4lJFsmzhKZLdhSL*XPh6uAui` |
| `snapshotrestore` | Snapshot / restore operations | `+D?2I0eIeUN6frEyQc626uK92C9fTzP6` |

---

## Wazuh API users

| User | Purpose | Password |
|------|---------|----------|
| `wazuh` | Wazuh API user | `3OsNhD.HMvy4WxihrRoNpywSu8AzH1Ln` |
| `wazuh-wui` | Wazuh API user (dashboard backend) | `9xo63T9TZFzC+ssp*65TMvXy24ilqQg4` |

---

## Authoritative source (use this if anything above fails to log in)

These values were transcribed from the installer output, which can be hard to read
char-for-char. The canonical copy lives on SIEM01 inside `wazuh-install-files.tar`.
To print the exact passwords any time:

```bash
sudo tar -O -xf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
```

Keep `wazuh-install-files.tar` somewhere safe (or delete it from the server once
these are in your password manager — it contains the cluster key and all certs too).

---

## Notes
- First action: confirm the `admin` login works at `https://10.10.20.20`. If it
  doesn't, re-run the command above and compare — a single mis-read character
  (0/O, l/1/I) is the usual culprit.
- Sanitize before publishing anything from this lab to GitHub: replace these with
  placeholders like `<WAZUH_ADMIN_PASSWORD>`.
