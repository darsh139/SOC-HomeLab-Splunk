# Detection — Unauthorized Account Creation

## MITRE ATT&CK Mapping
- **Tactic:** Persistence
- **Technique:** T1136.001 — Create Account: Local Account

---

## What This Detects
A new local user account being created on a Windows machine. 
Attackers do this to maintain persistent access even if their 
original entry point is discovered and closed.

---

## Attack Simulated
Ran the following on the Windows VM to simulate an attacker 
creating a backdoor account:

```powershell
net user HackerAccount Password123! /add
net localgroup administrators HackerAccount /add
```

---

## Splunk Detection Query

```spl
index=windows (EventCode=4720 OR EventCode=4732)
| table _time, host, user, src_user, EventCode
| eval Event=case(
    EventCode=4720, "New account created",
    EventCode=4732, "Account added to admin group"
  )
| sort _time
```

### EventCode Reference:
- `4720` — A user account was created
- `4732` — A member was added to a security-enabled local group
  (means someone was given admin rights)

---

## Screenshot — Detection in Splunk

![Account Creation Detection](../dashboards/screenshots/splunk-4720-search.png)

---

## Alert Configuration
- **Alert name:** Suspicious Account Creation
- **Type:** Real-time
- **Trigger:** Any occurrence of EventCode 4720 outside 
  business hours OR by non-admin user
- **Action:** Add to Triggered Alerts + Email notification

---

## What a SOC Analyst Does Next (Response Steps)
1. Identify WHO created the account (src_user field)
2. Was that person authorized to create accounts?
3. Was the new account added to Administrators group? (4732)
4. Disable the suspicious account immediately
5. Check login history of the creator's account for compromise
6. Review what the new account accessed
7. Escalate to Tier 2 — this is a high-severity persistence indicator
