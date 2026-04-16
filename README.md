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
(`192.168.10.0/24`). Nothing touches the real home network.

| VM | IP Address |
|---|---|
| Splunk Server (Ubuntu) | 192.168.10.10 |
| Windows 10 Target | 192.168.10.20 |
| Kali Linux Attacker | 192.168.10.30 |

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
## Tools Used

- [Splunk Enterprise](https://www.splunk.com/en_us/products/splunk-enterprise.html)
- [Sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [sysmon-modular config](https://github.com/olafhartong/sysmon-modular)
- [Kali Linux](https://www.kali.org/)
- [VirtualBox](https://www.virtualbox.org/)
- [Nmap](https://nmap.org/)
- [Hydra](https://github.com/vanhauser-thc/thc-hydra)

---

## How to Reproduce This Lab

### Step 1 — Install VirtualBox
Download and install [VirtualBox](https://www.virtualbox.org/) on your host machine.

### Step 2 — Create a NAT Network
- Open VirtualBox → Tools → Network → NAT Networks → Create
- Name it `SOC-Lab`, set IP range to `192.168.10.0/24`

### Step 3 — Set Up the VMs
Download and install these three VMs, all connected to the `SOC-Lab` NAT network:
- **Ubuntu Server 22.04** — assign static IP `192.168.10.10`
- **Windows 10** — assign static IP `192.168.10.20`
- **Kali Linux** — assign static IP `192.168.10.30`

### Step 4 — Install Splunk on Ubuntu
```bash
wget -O splunk.deb "https://download.splunk.com/products/splunk/releases/9.1.0/linux/splunk-9.1.0-amd64.deb"
sudo dpkg -i splunk.deb
sudo /opt/splunk/bin/splunk start --accept-license
```
Access Splunk at: `http://192.168.10.10:8000`

### Step 5 — Install Sysmon on Windows
- Download [Sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon) and the [sysmon-modular config](https://github.com/olafhartong/sysmon-modular)
- Run in PowerShell as Admin:
```powershell
.\Sysmon64.exe -accepteula -i sysmonconfig.xml
```

### Step 6 — Install Splunk Universal Forwarder on Windows
- Download from Splunk's website
- During install, point it to your Splunk server: `192.168.10.10:9997`
- Configure it to forward: Security, System, Application, and Sysmon logs

### Step 7 — Run Attacks from Kali
```bash
# Brute force
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.10.20 smb

# Port scan
nmap -sS -p- 192.168.10.20
```

### Step 8 — Use the SPL Queries
See the `/detection` folder for all Splunk queries used in this lab.
