# Incident Report — IR-003
**Date:** April 2025  
**Analyst:** Darsh Acharya  
**Severity:** High  

## Summary
An unauthorized local user account was created on the Windows 10 host 
(192.168.10.20), followed by that account being added to a privileged group. 
This was detected via Windows Security EventCode 4720 (account creation) 
and EventCode 4732 (account added to group), indicating a potential 
persistence and privilege escalation attempt.

## Timeline
| Time | Event |
|------|-------|
| 10:30 AM | EventCode 4720 logged — new local account created |
| 10:30 AM | EventCode 4732 logged — account added to Administrators group |
| 10:31 AM | Splunk alerts fire for both events |
| 10:32 AM | Analyst confirms unauthorized account creation from attacker machine |

## Detection
- **Tool:** Splunk SIEM  
- **EventCode:** 4720 (New Account Created), 4732 (Account Added to Group)  
- **SPL Query:** See `detection/account-creation-detection.spl`  
- **Alert:** Suspicious Account Creation / Privilege Escalation Detected  

## MITRE ATT&CK
- Technique: T1136.001 — Create Local Account  
- Technique: T1098 — Account Manipulation  

## Response Actions
1. Identified the newly created account name via Splunk logs
2. Confirmed the account was added to the Administrators group
3. In a real environment: immediately disable and delete the account, audit all recent privileged actions, investigate how the attacker gained access to create accounts, reset credentials of any compromised users

## Lessons Learned
Account creation events should always be monitored in real time. 
An attacker creating a backdoor account is a strong indicator of 
an active intrusion. Catching EventCode 4720 and 4732 together is 
a high-confidence signal of malicious activity.
