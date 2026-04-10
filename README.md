# Splunk SOC Home Lab

A personal cybersecurity home lab built to simulate a real Security 
Operations Center (SOC) environment. This project demonstrates skills 
in SIEM configuration, log analysis, threat detection, and incident 
response — core competencies for a SOC Analyst role.

---

## Lab Overview

This lab was built entirely on a single physical machine using 
VirtualBox to run multiple virtual machines in an isolated network.

| Component | Role |
|---|---|
| Splunk Enterprise (Free) | SIEM — collects, indexes, and analyzes logs |
| Ubuntu Server 22.04 | Hosts the Splunk server |
| Windows 10 | Log source and attack target |
| Kali Linux | Attacker machine — simulates real threats |
| Sysmon | Deep Windows event logging |
| Splunk Universal Forwarder | Ships logs from Windows to Splunk |

---

## Architecture
<img width="1230" height="688" alt="1" src="https://github.com/user-attachments/assets/391a4848-8476-4384-9b99-930749d8bbc5" />


**Network:** All VMs run on an isolated VirtualBox NAT Network 
(`192.168.56.0/24`). Nothing touches the real home network.

| VM | IP Address |
|---|---|
| Splunk Server (Ubuntu) | 192.168.56.10 |
| Windows 10 Target | 192.168.56.XX |
| Kali Linux Attacker | 192.168.56.XX |

---

## What I Built and Learned

- Deployed and configured **Splunk Enterprise** as a centralized SIEM
- Installed and configured **Sysmon** with a hardened config 
  (olafhartong/sysmon-modular) for deep Windows telemetry
- Installed **Splunk Universal Forwarder** to ship Windows Security, 
  System, Application, and Sysmon logs to Splunk
- Created a custom **windows** index in Splunk to store all endpoint logs
- Simulated **brute force attacks** using Hydra and analyzed 
  EventCode 4625 (failed logon) alerts in Splunk
- Simulated **port scanning** using Nmap and correlated with 
  Sysmon network connection events
- Simulated **unauthorized account creation** and detected it 
  via EventCode 4720 and 4732
- Built **custom Splunk alerts** that fire automatically when 
  attack patterns are detected
- Built a **Splunk security dashboard** showing real-time 
  security events
- Wrote **incident response reports** following industry-standard 
  IR documentation practices

---

## Detection Rules Built

| Attack Technique | MITRE ATT&CK | Splunk EventCode | Alert Name |
|---|---|---|---|
| Brute force login | T1110.001 | 4625 | Brute Force Login Detected |
| New local account created | T1136.001 | 4720 | Suspicious Account Creation |
| Admin group modification | T1098 | 4732 | Privilege Escalation Detected |
| Port scan (reconnaissance) | T1046 | Sysmon 3 | Network Recon Detected |
| Suspicious process creation | T1059 | Sysmon 1 | Suspicious Command Executed |

---

## Dashboards

<img width="1287" height="806" alt="2" src="https://github.com/user-attachments/assets/a829f80d-a04c-4739-b82d-b10ff910f0a6" />

---

