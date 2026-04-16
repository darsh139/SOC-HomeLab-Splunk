# Incident Report #001 — Brute Force Authentication Attack

| Field | Details |
|---|---|
| **Date** | [Date you ran the attack] |
| **Severity** | High |
| **Status** | Resolved (Simulated) |
| **Analyst** | [Your Name] |
| **Affected Host** | Windows-10-Target (192.168.56.XX) |
| **MITRE Technique** | T1110.001 — Brute Force: Password Guessing |

---

## Executive Summary

A brute force credential attack was detected against the 
Windows-10-Target machine. The attack originated from 
192.168.56.XX (Kali-Attacker) and targeted the local 
Administrator account using an automated password list. 
Splunk detected the attack via repeated EventCode 4625 
(failed logon) events and triggered an automated alert.

---

## Timeline of Events

| Time | Event |
|---|---|
| [10:13] | Nmap port scan detected from Kali (reconnaissance phase) |
| [10:17] | First failed login attempt detected (EventCode 4625) |
| [10:19] | 5th failed attempt — Splunk alert triggered |
| [10:39] | Attack stopped — 847 total attempts logged |
| [11:00] | Investigation began in Splunk |
| [11:17] | Incident documented and closed |

---

## Evidence

### Splunk Search Used
```spl
index=windows EventCode=4625
| stats count by src_ip, user, host
| sort -count
```

### Findings
- **Source IP:** 192.168.56.XX
- **Target Account:** administrator
- **Total Failed Attempts:** 847
- **Duration:** ~3 minutes
- **Tool Used:** Hydra (identified from Sysmon process logs)
- **Successful Login:** No

### Screenshot
![Splunk Alert Evidence](../dashboards/screenshots/splunk-4625-search.png)

---

## Indicators of Compromise (IOCs)

| Type | Value | Context |
|---|---|---|
| IP Address | 192.168.56.XX | Attacker source IP |
| Username targeted | administrator | High-value account |
| Tool signature | hydra | Detected in Sysmon process logs |

---

## Root Cause Analysis

The attack was possible because:
1. SMB (port 445) was accessible from the internal network
2. No account lockout policy was configured
3. No alert was configured prior to this investigation

---

## Containment Actions (What Would Happen in Real Environment)

1. Block source IP `192.168.56.XX` at the firewall
2. Temporarily lock the `administrator` account
3. Force password reset on all targeted accounts
4. Implement account lockout policy (lock after 5 failures)
5. Restrict SMB access to authorized hosts only

---

## Lessons Learned

- Account lockout policies should be enforced on all Windows machines
- SMB should be blocked at the perimeter and restricted internally
- Brute force alerts should fire after 5 failures, not just logged
- The `administrator` default account should be renamed or disabled

---

## Recommendations

| Priority | Recommendation |
|---|---|
| High | Enable account lockout after 5 failed attempts |
| High | Block SMB (445) from untrusted network segments |
| Medium | Rename or disable the built-in Administrator account |
| Medium | Deploy MFA for all privileged accounts |
| Low | Implement network segmentation between attacker-reachable zones |
