# Defender for Cloud Secure-Score Hardener

![](https://img.shields.io/badge/Azure-0089D6?style=flat-square) ![](https://img.shields.io/badge/Azure_CLI-334155?style=flat-square) ![](https://img.shields.io/badge/Defender-334155?style=flat-square) ![](https://img.shields.io/badge/MCSB-334155?style=flat-square)

`Week 13` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 11 of 36

> Raise an Azure subscription Secure Score by remediating Defender for Cloud recommendations.

## 🌍 The real-world problem

Azure exposes a Secure Score and a stream of recommendations, but turning that into prioritized, applied remediations is the actual work most teams skip.

## 🎯 Project goals

- Raise an Azure subscription Secure Score by remediating Defender recommendations.
- Turn a stream of recommendations into prioritized, applied fixes.
- Document the before/after score climb as evidence.

## 🧠 Skills you will learn

- Azure CLI and Defender for Cloud
- Secure Score and posture measurement
- Remediating MCSB recommendations
- HTTPS-only / NSG / disk-encryption fixes
- Evidence-based remediation reporting

## 🛠 What you build

A documented walkthrough that reads Defender recommendations via CLI, fixes the top 5, and shows the score climb.

## 🏗 Architecture

```
Defender (recommendations)   →   Triage (top findings)   →   Remediate (apply fixes)   →   Secure Score (climbs)
```
*Turn recommendations into posture.*

## ✅ Prerequisites

- Azure subscription with Defender enabled (Project 9)
- Azure CLI

## ⚙️ Setup

```bash
az login
az extension add --name security 2>/dev/null || true
az security secure-scores list -o table   # confirm you can read the score
```

## 🔨 Build it, step by step

### Step 1, Read your Secure Score

```bash
az login
az security secure-scores list --query "[].{name:displayName,score:score.percentage}" -o table
```

### Step 2, Pull the unhealthy recommendations

```bash
az security assessment list \
  --query "[?status.code=='Unhealthy'].{name:displayName,resource:resourceDetails.id}" -o table
```

### Step 3, Fix the top findings

Common high-impact fixes (apply via CLI or portal, then document):

- Enable Storage account "secure transfer required"
- Restrict NSG rules open to the internet
- Enable disk encryption on VMs
- Turn on auditing for SQL databases

```bash
# Example: enforce HTTPS-only on a storage account
az storage account update -n <ACCT> -g <RG> --https-only true
```

### Step 4, Re-measure and capture evidence

```bash
az security secure-scores list --query "[].score.percentage" -o tsv
```

Screenshot the score before and after - the delta is your headline result.

## 🧪 Done when

- [ ] Secure Score increases measurably
- [ ] At least 5 recommendations move to Healthy
- [ ] Each fix is documented with the command used

## 🚀 Stretch goals

- Export recommendations to CSV weekly
- Automate one fix with an Azure Policy remediation task
- Compare MCSB coverage vs CIS Azure

## 📦 Make it portfolio-ready

> Show the before/after Secure Score and a table of fixes mapped to the Microsoft Cloud Security Benchmark.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*