# Windows Server 2019/2022 Security Hardening

![](https://img.shields.io/badge/Windows_/_AD-0078D6?style=flat-square) ![](https://img.shields.io/badge/PowerShell-334155?style=flat-square) ![](https://img.shields.io/badge/Server_2019/2022-334155?style=flat-square) ![](https://img.shields.io/badge/CIS-334155?style=flat-square)

`Week 17` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 19 of 36

> Harden Windows Server 2019/2022 to the CIS / Microsoft Security Baseline with PowerShell evidence.

## 🌍 The real-world problem

Default Windows Server 2019/2022 ships with legacy protocols (SMBv1, NTLMv1), a weak audit policy, unnecessary services, and no security baseline - exactly the gaps used for lateral movement and ransomware.

## 🎯 Project goals

- Harden Windows Server 2019/2022 to the Microsoft/CIS Security Baseline.
- Kill legacy protocols (SMBv1, NTLMv1) and turn on real audit logging.
- Verify every control with a PowerShell audit script and before/after evidence.

## 🧠 Skills you will learn

- Windows Server administration
- PowerShell hardening + auditing
- Microsoft Security Compliance Toolkit (LGPO)
- SMB/NTLM/Kerberos protocol security
- CIS Windows Benchmark mapping

## 🛠 What you build

A PowerShell hardening + audit pair aligned to the Microsoft Security Baseline and CIS Windows Server 2019/2022 benchmark, with before/after evidence.

## 🏗 Architecture

```
Default Server (legacy on)   →   Baseline GPO (LGPO import)   →   Hardening (SMB / NTLM / audit)   →   audit.ps1 (verify)
```
*Harden to the Microsoft baseline, then prove it.*

## ✅ Prerequisites

- A Windows Server 2019 or 2022 VM (free eval ISO)
- Local admin
- Microsoft Security Compliance Toolkit (LGPO + Server 2019/2022 baselines)

## ⚙️ Setup

Use a throwaway Windows Server 2022 eval VM.

```bash
# Download the Microsoft Security Compliance Toolkit (LGPO.exe + baselines)
# Run all hardening from an ELEVATED PowerShell:
Set-ExecutionPolicy RemoteSigned -Scope Process
```

Snapshot the VM first so you can roll back and re-run for a clean demo.

## 🔨 Build it, step by step

### Step 1, Snapshot the insecure baseline

Capture the weak defaults first - services, audit policy, SMB config.

```bash
Get-SmbServerConfiguration | Select EnableSMB1Protocol, RequireSecuritySignature
auditpol /get /category:* | Select-String "No Auditing"
Get-Service | Where-Object {$_.Status -eq "Running"} | Measure-Object
```

### Step 2, Apply the Microsoft Security Baseline

Use LGPO.exe from the Security Compliance Toolkit to import the official baseline GPO backup.

```bash
# from an elevated PowerShell in the baseline folder
.\LGPO.exe /g ".\GPOs\{GUID-of-MS-Windows-Server-baseline}"
gpupdate /force
```

### Step 3, Kill legacy protocols + enforce SMB signing

```bash
# Disable SMBv1, require signing, disable NTLMv1
Set-SmbServerConfiguration -EnableSMB1Protocol $false -RequireSecuritySignature $true -Force
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol -NoRestart
# Restrict NTLM (audit first, then restrict)
Set-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\MSV1_0" -Name LmCompatibilityLevel -Value 5
```

### Step 4, Turn on advanced audit policy + firewall

```bash
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
auditpol /set /subcategory:"Process Creation" /success:enable
auditpol /set /subcategory:"Security Group Management" /success:enable /failure:enable
NetSh Advfirewall set allprofiles state on
```

### Step 5, Verify with an audit script

```bash
# audit.ps1 - re-check the key controls
$smb1 = (Get-SmbServerConfiguration).EnableSMB1Protocol
"SMBv1 disabled : " + (-not $smb1)
"SMB signing    : " + (Get-SmbServerConfiguration).RequireSecuritySignature
"Firewall on    : " + ((Get-NetFirewallProfile).Enabled -contains $true)
```

## 🧪 Done when

- [ ] SMBv1 is disabled and SMB signing required
- [ ] Advanced audit policy is logging logons + process creation
- [ ] The audit script reports all controls PASS

## 🚀 Stretch goals

- Run the CIS-CAT or Microsoft Badge tool and report the score
- Wrap it as a DSC configuration
- Compare to the CIS Windows Server Benchmark line by line

## 📦 Make it portfolio-ready

> Lead with the CIS/MS-Baseline mapping table and a before/after of the audit script. Note SMBv1 + NTLMv1 as the ransomware-relevant fixes.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*