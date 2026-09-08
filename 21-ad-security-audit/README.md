# Active Directory Security Audit & Hardening

![](https://img.shields.io/badge/Windows_/_AD-0078D6?style=flat-square) ![](https://img.shields.io/badge/Active_Directory-334155?style=flat-square) ![](https://img.shields.io/badge/PingCastle-334155?style=flat-square) ![](https://img.shields.io/badge/BloodHound-334155?style=flat-square)

`Week 18` · Difficulty ★★★★☆ · ~10-14 hrs · *Zero to Job-Ready* portfolio project No. 21 of 36

> Build a lab domain, find attack paths with PingCastle + BloodHound, then remediate them.

## 🌍 The real-world problem

AD accumulates risk: Domain Admin sprawl, stale privileged accounts, unconstrained Kerberos delegation, and weak ACLs that let an attacker walk from a normal user to Domain Admin.

## 🎯 Project goals

- Build a lab domain and find the path from a normal user to Domain Admin.
- Map attack paths with PingCastle and BloodHound, then break them.
- Show you can assess and harden the #1 enterprise target.

## 🧠 Skills you will learn

- Active Directory architecture
- PingCastle health scoring
- BloodHound / SharpHound attack-path mapping
- Tiered admin model + delegation fixes
- AD remediation and re-assessment

## 🛠 What you build

A lab AD forest assessed with PingCastle (health score) and BloodHound (attack paths), then hardened with a tiered admin model and delegation fixes.

## 🏗 Architecture

```
Lab Domain (DC + members)   →   PingCastle (health score)   →   BloodHound (attack paths)   →   Remediate (tiering / delegation)
```
*Find the path to Domain Admin, then break it.*

## ✅ Prerequisites

- A lab DC (Windows Server promoted to a domain controller) + a member workstation
- PingCastle + BloodHound + SharpHound
- Isolated lab network only

## ⚙️ Setup

Build an isolated lab forest - never touch production AD.

```bash
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
Install-ADDSForest -DomainName "lab.local" -InstallDns -Force
# Download PingCastle + BloodHound + SharpHound into the lab
```

Offensive AD tooling is for isolated labs you own only.

## 🔨 Build it, step by step

### Step 1, Stand up a lab domain

```bash
# Promote a Windows Server to a DC
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
Install-ADDSForest -DomainName "lab.local" -InstallDns -Force
# create a few users/groups + a member workstation joined to lab.local
```

Lab only, isolated network. Never run offensive AD tooling against production or networks you do not own.

### Step 2, Run a PingCastle health check

PingCastle scores your domain and lists prioritized risks.

```bash
PingCastle.exe --healthcheck --server lab.local
# open the HTML report - note the global risk score and the top rules
```

### Step 3, Map attack paths with BloodHound

```bash
# collect with SharpHound, then import the zip into BloodHound
SharpHound.exe -c All -d lab.local
# In BloodHound run: "Shortest paths to Domain Admins" + "Find principals with DCSync rights"
```

### Step 4, Remediate the top findings

- Empty/limit Domain Admins; use a tiered admin model (Tier 0/1/2)
- Remove unconstrained delegation; prefer constrained / RBCD
- Add privileged accounts to the Protected Users group
- Disable + clean up stale accounts (PasswordLastSet, LastLogon)

```bash
# find unconstrained delegation
Get-ADComputer -Filter {TrustedForDelegation -eq $true} -Properties TrustedForDelegation
# add an admin to Protected Users
Add-ADGroupMember "Protected Users" -Members "tier0-admin"
```

### Step 5, Re-scan and show the improvement

```bash
PingCastle.exe --healthcheck --server lab.local
# compare the new global score + resolved rules against the first report
```

## 🧪 Done when

- [ ] PingCastle global risk score drops after remediation
- [ ] BloodHound shows shortest-path-to-DA is broken
- [ ] Unconstrained delegation is removed and Domain Admins is minimized

## 🚀 Stretch goals

- Implement the full Tier 0 isolation (PAWs)
- Add an AD certificate services (ADCS) ESC check
- Map findings to MITRE ATT&CK (Credential Access, Lateral Movement)

## 📦 Make it portfolio-ready

> Show the PingCastle before/after score, a BloodHound attack-path screenshot, and a one-page tiered-admin model diagram.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*