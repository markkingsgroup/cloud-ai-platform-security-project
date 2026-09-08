# Sysmon + Windows Event Threat Hunting

![](https://img.shields.io/badge/Windows_/_AD-0078D6?style=flat-square) ![](https://img.shields.io/badge/Sysmon-334155?style=flat-square) ![](https://img.shields.io/badge/Sigma-334155?style=flat-square) ![](https://img.shields.io/badge/PowerShell-334155?style=flat-square)

`Week 18` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 24 of 36

> Deploy Sysmon and hunt Windows hosts for lateral movement, persistence, and credential abuse.

## 🌍 The real-world problem

Windows produces rich telemetry but raw Event Logs are overwhelming. Hunters need to know which Event IDs matter and how attacker behaviors look in them.

## 🎯 Project goals

- Deploy a tuned Sysmon and hunt hosts for lateral movement, persistence and credential abuse.
- Learn the high-value Windows Event IDs and what attacks look like in them.
- Write Sigma rules that catch encoded PowerShell, LOLBins and rogue services.

## 🧠 Skills you will learn

- Sysmon deployment + tuning
- Windows Event Log threat hunting
- Sigma rule authoring
- Detecting LOLBins + encoded PowerShell
- Host-based detection engineering

## 🛠 What you build

A tuned Sysmon deployment plus PowerShell/Sigma hunts for encoded PowerShell, LOLBins, suspicious services, and new-account persistence.

## 🏗 Architecture

```
Windows host (activity)   →   Sysmon (rich telemetry)   →   Hunt (PowerShell / Sigma)   →   Detections (LOLBins / persistence)
```
*Where most enterprise attacks are caught.*

## ✅ Prerequisites

- A Windows VM
- Sysmon (SwiftOnSecurity / Olaf config)
- PowerShell

## ⚙️ Setup

```bash
# Grab a tuned Sysmon config (SwiftOnSecurity or Olaf Hartong)
sysmon64.exe -accepteula -i sysmonconfig-export.xml
Get-WinEvent -ListLog "Microsoft-Windows-Sysmon/Operational"
```

## 🔨 Build it, step by step

### Step 1, Deploy a tuned Sysmon

```bash
sysmon64.exe -accepteula -i sysmonconfig-export.xml
Get-WinEvent -ListLog "Microsoft-Windows-Sysmon/Operational"
```

### Step 2, Learn the high-value Event IDs

- Security 4624/4625 - logon success/failure (type 3 = network, 10 = RDP)
- Security 4688 - process creation (with command line)
- Security 7045 / Sysmon 1 - service install / process create
- Sysmon 3 - network connection; 11 - file create; 13 - registry

### Step 3, Hunt for attacker behaviors

```bash
# Encoded PowerShell
Get-WinEvent -FilterHashtable @{LogName="Security";Id=4688} |
  Where-Object { $_.Message -match "-enc|-EncodedCommand|FromBase64String" }
# New service install (persistence)
Get-WinEvent -FilterHashtable @{LogName="System";Id=7045}
```

### Step 4, Write 3 Windows Sigma rules

```bash
title: Encoded PowerShell Command
logsource: { product: windows, category: process_creation }
detection:
  sel: { Image|endswith: "powershell.exe", CommandLine|contains: "-enc" }
  condition: sel
level: high
```

### Step 5, Validate against simulated activity

Run a benign encoded PowerShell command and a fake service install; confirm your hunts + rules catch them.

## 🧪 Done when

- [ ] Sysmon is logging process + network + registry events
- [ ] Your hunts surface the simulated encoded-PowerShell + new service
- [ ] 3 Windows Sigma rules fire on the test activity

## 🚀 Stretch goals

- Hunt for LOLBins (certutil, mshta, regsvr32)
- Detect RDP lateral movement (4624 type 10)
- Ship to your SIEM and build a Windows dashboard

## 📦 Make it portfolio-ready

> Include the Event-ID cheat sheet, the 3 Sigma rules, and a note on tuning Sysmon to cut noise.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*