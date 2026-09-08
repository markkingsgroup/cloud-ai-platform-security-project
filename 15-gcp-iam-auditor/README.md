# GCP IAM Least-Privilege Auditor

![](https://img.shields.io/badge/GCP-4285F4?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/GCP_SDK-334155?style=flat-square) ![](https://img.shields.io/badge/IAM-334155?style=flat-square)

`Week 15` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 15 of 36

> Finds primitive roles, over-privileged service accounts, and user-managed SA keys.

## 🌍 The real-world problem

GCP projects accumulate Owner/Editor primitive roles, service accounts with broad rights, and long-lived user-managed SA keys - the top GCP attack paths.

## 🎯 Project goals

- Flag GCP-specific footguns: primitive roles, over-privileged service accounts, user-managed SA keys.
- Surface the top GCP lateral-movement paths before they are abused.
- Show real GCP depth, not just transferred AWS knowledge.

## 🧠 Skills you will learn

- Python + gcloud / GCP IAM
- Primitive vs predefined vs custom roles
- Service-account key risk analysis
- Reading and parsing IAM policy bindings
- GCP least-privilege auditing

## 🛠 What you build

A Cloud Asset / IAM script that flags primitive-role bindings, privileged SAs, and old SA keys.

## 🏗 Architecture

```
IAM policy (bindings)   →   Auditor (primitive roles / SA keys)   →   Report (least-privilege gaps)
```
*GCP IAM footguns, found.*

## ✅ Prerequisites

- GCP project + `gcloud`
- `pip install google-cloud-iam google-cloud-asset`
- Viewer + Security Reviewer roles

## ⚙️ Setup

```bash
pip install google-cloud-iam google-cloud-asset
gcloud auth application-default login
gcloud config set project <PROJECT_ID>
```

## 🔨 Build it, step by step

### Step 1, Read the IAM policy

```bash
gcloud projects get-iam-policy <PROJECT_ID> --format=json > policy.json
```

```bash
import json
pol=json.load(open("policy.json"))
for b in pol["bindings"]:
    if b["role"] in ("roles/owner","roles/editor"):
        print("PRIMITIVE ROLE:",b["role"],"->",b["members"])
```

### Step 2, Flag privileged service accounts

```bash
# Service accounts with owner/editor are a lateral-movement risk
for b in pol["bindings"]:
    if b["role"] in ("roles/owner","roles/editor"):
        for m in b["members"]:
            if m.startswith("serviceAccount:"): print("PRIVILEGED SA:",m)
```

### Step 3, Find user-managed SA keys

```bash
gcloud iam service-accounts list --format="value(email)" | while read SA; do
  gcloud iam service-accounts keys list --iam-account "$SA" --managed-by=user \
    --format="value(name,validAfterTime)"; done
# user-managed keys never expire by default -> rotate or remove
```

## 🧪 Done when

- [ ] A user/SA with Owner or Editor is flagged
- [ ] User-managed SA keys are listed
- [ ] Read-only audit, no changes

## 🚀 Stretch goals

- Use Policy Analyzer for who-can-access
- Suggest predefined-role replacements
- Schedule via Cloud Function

## 📦 Make it portfolio-ready

> Explain primitive vs predefined vs custom roles and why SA keys are dangerous.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*