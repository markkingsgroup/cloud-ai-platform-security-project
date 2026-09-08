# AD Attack & Detection Lab

![](https://img.shields.io/badge/Windows_/_AD-0078D6?style=flat-square) ![](https://img.shields.io/badge/Active_Directory-334155?style=flat-square) ![](https://img.shields.io/badge/Sysmon-334155?style=flat-square) ![](https://img.shields.io/badge/Wazuh-334155?style=flat-square)

`Week 18` · Difficulty ★★★★☆ · ~10-14 hrs · *Zero to Job-Ready* portfolio project No. 23 of 36

> Safely simulate Kerberoasting, AS-REP roasting, and DCSync - then detect them with Sysmon + a SIEM.

## 🌍 The real-world problem

Kerberoasting, AS-REP roasting, and DCSync are among the most common AD attacks and frequently go undetected. Engineers must understand the attack to write the detection.

## 🎯 Project goals

- Safely simulate Kerberoasting, AS-REP roasting and DCSync in an isolated lab.
- Ship Windows + Sysmon telemetry to a SIEM and write detections that fire.
- Do real purple-team work: run the attack, build the detection.

## 🧠 Skills you will learn

- AD attack techniques (Kerberoast/AS-REP/DCSync)
- Sysmon telemetry + Windows Event IDs
- Sigma detection rules
- SIEM ingestion (Wazuh)
- MITRE ATT&CK mapping

## 🛠 What you build

An isolated lab where you run each attack, ship Windows Security + Sysmon logs to Wazuh, and write detections (Sigma) that fire on the telemetry.

## 🏗 Architecture

```
Attacker (Rubeus / mimikatz)   →   AD + Sysmon (telemetry)   →   Wazuh SIEM (collect)   →   Detections (Event IDs / Sigma)
```
*Run the attack, build the detection.*

## ✅ Prerequisites

- Lab domain (Project 20) - fully isolated
- An attacker VM (Kali or a Windows box) inside the lab
- Sysmon + Wazuh from earlier projects

## ⚙️ Setup

Fully isolated lab, authorized testing only.

```bash
# On each host: install Sysmon + the Wazuh agent
sysmon64.exe -accepteula -i sysmonconfig.xml
# Attacker box (Kali / Windows) inside the SAME isolated network
```

## 🔨 Build it, step by step

### Step 1, Ship Windows telemetry to a SIEM

```bash
# install Sysmon with a tuned config on the DC + workstation
sysmon64.exe -accepteula -i sysmonconfig.xml
# install the Wazuh agent and forward Security + Sysmon channels
```

Everything here is for an isolated lab you own. Authorized testing only.

### Step 2, Simulate Kerberoasting + AS-REP roasting

```bash
# Kerberoast (request service tickets for SPN accounts)
Rubeus.exe kerberoast
# AS-REP roast (accounts with pre-auth disabled)
Rubeus.exe asreproast
```

### Step 3, Simulate DCSync (lab only)

```bash
# mimikatz DCSync replicates secrets like a DC would
lsadump::dcsync /domain:lab.local /user:krbtgt
```

### Step 4, Write the detections

- Kerberoasting -> Event ID 4769 with RC4 (0x17) + many SPN requests from one user
- AS-REP roasting -> Event ID 4768 with pre-auth not required
- DCSync -> Event ID 4662 with the DS-Replication-Get-Changes-All GUID by a non-DC account

```bash
title: Potential Kerberoasting
logsource: { product: windows, service: security }
detection:
  sel: { EventID: 4769, TicketEncryptionType: "0x17" }
  condition: sel
level: high
```

### Step 5, Convert to your SIEM + validate

```bash
sigma convert -t wazuh rules/kerberoast.yml
# add to local_rules.xml, re-run the attack, confirm the alert fires
```

## 🧪 Done when

- [ ] Each attack runs in the lab and generates telemetry
- [ ] Kerberoasting, AS-REP, and DCSync each raise a SIEM alert
- [ ] Each detection is documented with its Event ID + logic

## 🚀 Stretch goals

- Add Golden Ticket detection
- Detect DPAPI / LSASS dumping
- Build a small ATT&CK coverage map for AD

## 📦 Make it portfolio-ready

> Map each attack+detection to MITRE ATT&CK (T1558 Kerberoasting, T1003.006 DCSync). Include the Sigma rules and alert screenshots.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*