# GCP Security Command Center Hardener

![](https://img.shields.io/badge/GCP-4285F4?style=flat-square) ![](https://img.shields.io/badge/gcloud-334155?style=flat-square) ![](https://img.shields.io/badge/SCC-334155?style=flat-square) ![](https://img.shields.io/badge/Org_Policy-334155?style=flat-square)

`Week 15` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 16 of 36

> Use Security Command Center to find and remediate misconfigurations across a GCP project.

## 🌍 The real-world problem

SCC surfaces vulnerabilities and misconfigurations, but teams need to triage and remediate them - public buckets, open firewall rules, default service accounts.

## 🎯 Project goals

- Use Security Command Center to find and remediate misconfigurations across a GCP project.
- Drive active findings down to near zero with documented fixes.
- Run the GCP native-CSPM remediation loop end to end.

## 🧠 Skills you will learn

- gcloud and Security Command Center
- GCP native CSPM triage
- Firewall and bucket remediation
- Mapping SCC findings to CIS GCP
- Before/after posture reporting

## 🛠 What you build

A walkthrough that lists SCC findings, remediates the top categories, and shows the finding count drop.

## 🏗 Architecture

```
Security Command Center (findings)   →   Triage (top categories)   →   Remediate (fixes)   →   Findings (drop to 0)
```
*GCP native CSPM, driven to zero.*

## ✅ Prerequisites

- GCP project with SCC (Standard tier is free)
- `gcloud` + Security Center Admin Viewer

## ⚙️ Setup

```bash
gcloud auth login
gcloud services enable securitycenter.googleapis.com
gcloud scc findings list <PROJECT_ID> --filter="state=\"ACTIVE\"" --limit 5
```

## 🔨 Build it, step by step

### Step 1, List active findings

```bash
gcloud scc findings list <ORG_OR_PROJECT> \
  --filter="state=\"ACTIVE\"" \
  --format="table(category,resourceName)"
```

### Step 2, Remediate the common categories

- PUBLIC_BUCKET_ACL -> remove allUsers/allAuthenticatedUsers
- OPEN_FIREWALL -> restrict source ranges from 0.0.0.0/0
- DEFAULT_SERVICE_ACCOUNT_USED -> use a dedicated minimal SA

```bash
# Lock a public firewall rule
gcloud compute firewall-rules update <RULE> --source-ranges=10.0.0.0/8
```

### Step 3, Re-scan and capture the delta

```bash
gcloud scc findings list <ORG_OR_PROJECT> --filter="state=\"ACTIVE\"" --format="value(category)" | sort | uniq -c
```

Screenshot the active-finding count before and after.

## 🧪 Done when

- [ ] Active SCC findings decrease
- [ ] Public bucket + open firewall findings are resolved
- [ ] Each fix is documented

## 🚀 Stretch goals

- Export findings to BigQuery
- Auto-remediate one category with a Cloud Function
- Compare SCC vs AWS Security Hub vs Defender

## 📦 Make it portfolio-ready

> Map SCC finding categories to the CIS GCP Benchmark; show the before/after count.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*