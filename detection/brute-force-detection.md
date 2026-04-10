
# Detection — Brute Force Login Attack

## MITRE ATT&CK Mapping
- **Tactic:** Credential Access
- **Technique:** T1110.001 — Brute Force: Password Guessing

---

## What This Detects
Multiple failed login attempts against a Windows account in a 
short time period — a classic indicator of an automated 
brute force attack.

---

## Attack Simulated
Used **Hydra** from Kali Linux to brute force the Windows 
Administrator account over SMB:

```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt smb://192.168.56.XX
```

---

## Splunk Detection Query

```spl
index=windows EventCode=4625
| stats count by src_ip, user, host
| where count > 5
| sort -count
```

### What each part does:
- `EventCode=4625` — Windows failed logon event
- `stats count by src_ip, user, host` — groups failures by 
  attacker IP, targeted user, and machine
- `where count > 5` — only shows accounts with more than 
  5 failures (filters noise)
- `sort -count` — worst offenders at the top

---

## Screenshot — Detection in Splunk

![Brute Force Detection](../dashboards/screenshots/splunk-4625-search.png)

---

## Alert Configuration
- **Alert name:** Brute Force Login Detected
- **Type:** Real-time
- **Trigger:** More than 5 failed logins from same IP in 60 seconds
- **Action:** Add to Triggered Alerts

---

## What a SOC Analyst Does Next (Response Steps)
1. Identify the source IP from the alert
2. Check if the IP is internal or external
3. Check which accounts were targeted
4. If any login SUCCEEDED after failures — treat as compromised
5. Block the source IP at the firewall
6. Lock the targeted account temporarily
7. Reset credentials if any succeeded
8. Escalate to Tier 2 if source IP is external or persistent
