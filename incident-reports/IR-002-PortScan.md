# Incident Report — IR-002
**Date:** April 2025  
**Analyst:** Darsh Acharya  
**Severity:** Medium  

## Summary
A port scan was detected originating from the Kali Linux attacker machine 
(192.168.10.30) targeting the Windows 10 host (192.168.10.20). Sysmon 
network connection events (EventCode 3) recorded connections to over 15 
unique ports within a short time window, indicating active reconnaissance.

## Timeline
| Time | Event |
|------|-------|
| 10:15 AM | First Sysmon network connection event logged |
| 10:15 AM | Rapid connections to multiple ports observed |
| 10:16 AM | Splunk alert fires — unique port threshold exceeded |
| 10:17 AM | Analyst identifies source IP 192.168.10.30 as attacker |

## Detection
- **Tool:** Splunk SIEM  
- **EventCode:** Sysmon EventCode 3 (Network Connection)  
- **SPL Query:** See `detection/port-scan-detection.spl`  
- **Alert:** Network Recon Detected  

## MITRE ATT&CK
- Technique: T1046 — Network Service Scanning  

## Response Actions
1. Identified scanning IP via Splunk query
2. Reviewed Sysmon logs to confirm port scan pattern
3. In a real environment: block source IP at firewall, investigate what services were exposed, alert the network team

## Lessons Learned
Port scans are often the first step before an actual attack. 
Detecting reconnaissance early gives defenders a critical window to respond 
before the attacker moves to exploitation.
