# Group Policy Baselines + Windows LAPS

![](https://img.shields.io/badge/Windows_/_AD-0078D6?style=flat-square) ![](https://img.shields.io/badge/Group_Policy-334155?style=flat-square) ![](https://img.shields.io/badge/Windows_LAPS-334155?style=flat-square) ![](https://img.shields.io/badge/GPO-334155?style=flat-square)

`Week 18` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 22 of 36

> Deploy Microsoft Security Baseline GPOs and rotate local admin passwords with Windows LAPS.

## 🌍 The real-world problem

Shared/reused local administrator passwords let one compromised host become the whole network (pass-the-hash). Meanwhile GPOs drift from any baseline. Microsoft Security Baselines + LAPS fix both.

## 🎯 Project goals

- Deploy Microsoft Security Baseline GPOs across a Windows fleet.
- Rotate and escrow local admin passwords with Windows LAPS to kill pass-the-hash.
- Harden account, NTLM and legacy-protocol settings at scale via Group Policy.

## 🧠 Skills you will learn

- Group Policy at fleet scale
- Windows LAPS deployment
- Security Baseline GPO import + linking
- OU design and security filtering
- Pass-the-hash mitigation

## 🛠 What you build

Imported Microsoft Security Baseline GPOs, hardened account/lockout/NTLM settings, and Windows LAPS rotating + escrowing local admin passwords in AD.

## 🏗 Architecture

```
MS Baseline GPOs (import + link)   →   Windows LAPS (rotate local admin)   →   Hardening (lockout / NTLM / legacy)   →   RSoP (verify)
```
*Baselines + LAPS at fleet scale.*

## ✅ Prerequisites

- Lab domain from Project 20
- Microsoft Security Compliance Toolkit baselines
- Windows LAPS (built into current Windows)

## ⚙️ Setup

Uses the lab domain from Project 20.

```bash
# Get the Microsoft Security Compliance Toolkit baselines.
# Modern Windows has LAPS built in:
Get-Command -Module LAPS
```

## 🔨 Build it, step by step

### Step 1, Import the Microsoft Security Baseline GPOs

```bash
# from the baseline folder (Security Compliance Toolkit)
.\Baseline-LocalInstall.ps1 -Win11DomainJoined   # or the Server baseline script
# this imports + links the baseline GPOs in the domain
```

### Step 2, Deploy Windows LAPS

```bash
# Update the AD schema for LAPS, then set the policy via GPO/registry
Update-LapsADSchema
Set-LapsADComputerSelfPermission -Identity "OU=Workstations,DC=lab,DC=local"
# GPO: Computer > Policies > Admin Templates > System > LAPS > enable password backup to AD
```

### Step 3, Harden the key GPO settings

- Account lockout: threshold 5, duration 15 min
- Password policy: 14+ chars, complexity, history 24
- Restrict NTLM (audit then deny LM/NTLMv1)
- Disable legacy: SMBv1, LLMNR, NBT-NS, WDigest

```bash
# example: disable LLMNR via GPO-backed registry
Set-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" -Name EnableMulticast -Value 0
```

### Step 4, Scope GPOs with OUs + security filtering

```bash
New-GPLink -Name "MS Security Baseline - Workstation" -Target "OU=Workstations,DC=lab,DC=local"
# use security filtering so the right GPO hits the right machines
```

### Step 5, Verify policy + LAPS rotation

```bash
gpupdate /force; gpresult /h rsop.html
# retrieve a rotated local admin password from AD
Get-LapsADPassword -Identity "WKS01" -AsPlainText
```

## 🧪 Done when

- [ ] gpresult/RSoP shows the baseline + hardening GPOs applied
- [ ] A workstation local admin password is randomized and stored in AD
- [ ] LLMNR/NBT-NS and SMBv1 are disabled by policy

## 🚀 Stretch goals

- Add AppLocker / WDAC allow-listing via GPO
- Add a GPO to deploy Sysmon fleet-wide
- Audit GPOs for risky settings with a script

## 📦 Make it portfolio-ready

> Include an OU + GPO link diagram, the hardened settings table, and a screenshot of retrieving a LAPS password.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*