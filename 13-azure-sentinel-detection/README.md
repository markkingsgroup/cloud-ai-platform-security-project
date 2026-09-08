# Azure Monitor to Sentinel Detection Lab

![](https://img.shields.io/badge/Azure-0089D6?style=flat-square) ![](https://img.shields.io/badge/Sentinel-334155?style=flat-square) ![](https://img.shields.io/badge/KQL-334155?style=flat-square) ![](https://img.shields.io/badge/Azure-334155?style=flat-square)

`Week 14` · Difficulty ★★★★☆ · ~8-12 hrs · *Zero to Job-Ready* portfolio project No. 13 of 36

> Wire Activity Logs into Microsoft Sentinel and write KQL analytics rules that catch attacks.

## 🌍 The real-world problem

Azure produces rich logs but most go unanalyzed. Engineers must connect them to Sentinel and write detections in KQL that fire on real malicious activity.

## 🎯 Project goals

- Wire Activity and Entra logs into Microsoft Sentinel.
- Write KQL analytics rules that fire on role grants, mass deletion and impossible travel.
- Build a demonstrable, in-demand cloud-SIEM detection skill.

## 🧠 Skills you will learn

- Microsoft Sentinel (cloud SIEM)
- KQL (Kusto Query Language) detections
- Data connectors and analytics rules
- Detection engineering on Azure
- Incident validation and testing

## 🛠 What you build

A Sentinel workspace ingesting Activity + Entra logs, with 3 KQL analytics rules (role assignment, mass deletion, impossible travel) proven to alert.

## 🏗 Architecture

```
Activity + Entra (logs)   →   Log Analytics (workspace)   →   Sentinel (KQL rules)   →   Incidents (alerts)
```
*Detection-as-code in KQL.*

## ✅ Prerequisites

- Azure subscription + Log Analytics (Project 9)
- Sentinel enabled on the workspace

## ⚙️ Setup

Sentinel sits on a Log Analytics workspace (from Project 9).

```bash
az sentinel onboarding-state create -g sec-baseline-rg --workspace-name sec-law --name default
```

## 🔨 Build it, step by step

### Step 1, Enable Sentinel + connect data

```bash
az sentinel onboarding-state create --resource-group sec-baseline-rg --workspace-name sec-law --name default
# In the portal: Sentinel -> Data connectors -> enable "Azure Activity" and "Microsoft Entra ID"
```

### Step 2, Write a KQL detection (privilege grant)

```bash
AzureActivity
| where OperationNameValue == "MICROSOFT.AUTHORIZATION/ROLEASSIGNMENTS/WRITE"
| where ActivityStatusValue == "Success"
| project TimeGenerated, Caller, Properties
// alerts when someone grants an RBAC role
```

### Step 3, Detect mass deletion + impossible travel

```bash
// Mass resource deletion in 10 min
AzureActivity
| where OperationNameValue endswith "DELETE"
| summarize count() by Caller, bin(TimeGenerated, 10m)
| where count_ > 10
```

### Step 4, Create the analytics rule + test

Save each query as a Scheduled analytics rule (Sentinel -> Analytics -> Create). Then generate the activity and confirm an incident is created.

## 🧪 Done when

- [ ] Activity + Entra logs are flowing into the workspace
- [ ] Granting an RBAC role raises a Sentinel incident
- [ ] Each KQL rule is documented with what it catches

## 🚀 Stretch goals

- Add a watchlist of admin accounts
- Build a workbook dashboard
- Automate response with a Logic App playbook

## 📦 Make it portfolio-ready

> Include the 3 KQL rules, an incident screenshot, and a note on KQL vs Sigma.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*