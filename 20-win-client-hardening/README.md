# Windows 10/11 Client Hardening & Endpoint Security

![](https://img.shields.io/badge/Windows_/_AD-0078D6?style=flat-square) ![](https://img.shields.io/badge/Windows_10/11-334155?style=flat-square) ![](https://img.shields.io/badge/PowerShell-334155?style=flat-square) ![](https://img.shields.io/badge/Defender-334155?style=flat-square)

`Week 17` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 20 of 36

> Harden Windows 10/11 endpoints: BitLocker, Defender + ASR rules, the MS client baseline, and attack-surface reduction.

## 🌍 The real-world problem

Default Windows 10/11 clients allow macros, script abuse, and lateral movement; local admin is often reused; disks may be unencrypted. The endpoint is the most-attacked surface in any enterprise.

## 🎯 Project goals

- Harden the Windows 10/11 endpoint, the most-attacked surface.
- Enable BitLocker, Defender ASR rules and Controlled Folder Access.
- Audit the client controls and prove they are enforced.

## 🧠 Skills you will learn

- Windows 10/11 endpoint security
- BitLocker disk encryption
- Microsoft Defender + Attack Surface Reduction rules
- PowerShell client baseline + audit
- Anti-ransomware controls

## 🛠 What you build

A documented client baseline: BitLocker drive encryption, Microsoft Defender + Attack Surface Reduction (ASR) rules, the MS Windows 10/11 Security Baseline, and a PowerShell audit of the controls.

## 🏗 Architecture

```
Win 10/11 (endpoint)   →   MS Baseline (LGPO import)   →   BitLocker + ASR (+ Defender)   →   client_audit.ps1 (verify)
```
*Harden the most-attacked surface: the endpoint.*

## ✅ Prerequisites

- A Windows 10 or 11 VM (free eval / dev ISO)
- Local admin
- Microsoft Security Compliance Toolkit (Win10/11 baselines)

## ⚙️ Setup

Use a Windows 10 or 11 eval/dev VM with a virtual TPM enabled (needed for BitLocker).

```bash
# Get the Security Compliance Toolkit Win10/11 baselines; run from an ELEVATED PowerShell
Set-ExecutionPolicy RemoteSigned -Scope Process
Get-Tpm   # confirm a TPM is present
```

Enable a virtual TPM in your hypervisor or BitLocker falls back to a password protector.

## 🔨 Build it, step by step

### Step 1, Snapshot the client baseline

Capture the defaults: BitLocker status, Defender state, and risky features.

```bash
Get-BitLockerVolume | Select MountPoint, ProtectionStatus
Get-MpComputerStatus | Select RealTimeProtectionEnabled, AMServiceEnabled
Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol | Select State
```

### Step 2, Apply the Microsoft Windows 10/11 Security Baseline

```bash
# from the Security Compliance Toolkit (Win11 or Win10 baseline folder)
.\Baseline-LocalInstall.ps1 -Win11NonDomainJoined
gpupdate /force
```

### Step 3, Enable BitLocker + Defender ASR rules

BitLocker stops offline disk theft; ASR rules block the macro/script techniques attackers use to land.

```bash
# BitLocker (TPM-backed)
Enable-BitLocker -MountPoint "C:" -EncryptionMethod XtsAes256 -UsedSpaceOnly -TpmProtector
# A few high-value ASR rules (block Office child processes, credential theft, obfuscated scripts)
Set-MpPreference -AttackSurfaceReductionRules_Ids `
  D4F940AB-401B-4EFC-AADC-AD5F3C50688A,9E6C4E1F-7D60-472F-BA1A-A39EF669E4B2,5BEB7EFE-FD9A-4556-801D-275E5FFC04CC `
  -AttackSurfaceReductionRules_Actions Enabled,Enabled,Enabled
```

### Step 4, Kill legacy + lock down the endpoint

```bash
# Disable SMBv1, LLMNR, and macro abuse paths via policy
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol -NoRestart
Set-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" -Name EnableMulticast -Value 0
# Controlled Folder Access (anti-ransomware)
Set-MpPreference -EnableControlledFolderAccess Enabled
```

### Step 5, Audit the client controls

```bash
# client_audit.ps1
"BitLocker C: : " + (Get-BitLockerVolume -MountPoint C:).ProtectionStatus
"Defender RTP : " + (Get-MpComputerStatus).RealTimeProtectionEnabled
"ASR rules    : " + ((Get-MpPreference).AttackSurfaceReductionRules_Ids.Count)
"SMBv1        : " + (Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol).State
```

## 🧪 Done when

- [ ] BitLocker is encrypting C: with a TPM protector
- [ ] Defender real-time protection + ASR rules are enabled
- [ ] SMBv1/LLMNR are disabled; the client audit script reports all PASS

## 🚀 Stretch goals

- Deploy the baseline fleet-wide via Intune or GPO
- Add AppLocker / WDAC application allow-listing
- Onboard the client to Microsoft Defender for Endpoint

## 📦 Make it portfolio-ready

> Show the before/after audit, the ASR rules you enabled (and why), and a note that these GPOs deploy fleet-wide via Project 21.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*