# AWS IAM Least-Privilege Auditor

![](https://img.shields.io/badge/AWS-FF9900?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/boto3-334155?style=flat-square) ![](https://img.shields.io/badge/IAM-334155?style=flat-square)

`Week 8` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 7 of 36

> Finds over-permissioned users, unused keys, missing MFA, and wildcard policies.

## 🌍 The real-world problem

IAM accumulates risk: admin handed out "temporarily", keys nobody rotates, users without MFA, Action:*/Resource:* policies.

## 🎯 Project goals

- Find privilege creep: over-permissioned users, unused keys, missing MFA, wildcard policies.
- Rank identities by risk so remediation is prioritized.
- Show you can audit the blast radius before an attacker exploits it.

## 🧠 Skills you will learn

- Python with boto3
- AWS IAM model, policies and credential reports
- Least-privilege and privilege-creep analysis
- Detecting wildcard (Action:* Resource:*) grants
- Risk ranking and remediation guidance

## 🛠 What you build

A boto3 auditor that scores each user on MFA, key age, and wildcard permissions.

## 🏗 Architecture

```
IAM + policies (credential report)   →   Auditor (MFA / keys / wildcards)   →   Risk Score (ranked)
```
*Find privilege creep first.*

## ✅ Prerequisites

- IAM read + GenerateCredentialReport
- `pip install boto3`

## ⚙️ Setup

```bash
pip install boto3
aws iam generate-credential-report   # first call returns STARTED
```

## 🔨 Build it, step by step

### Step 1, Pull the credential report

```bash
import boto3,time,csv,io
iam=boto3.client("iam"); iam.generate_credential_report(); time.sleep(3)
rows=list(csv.DictReader(io.StringIO(iam.get_credential_report()["Content"].decode())))
```

### Step 2, Flag MFA + stale keys

```bash
from datetime import datetime,timezone
for u in rows:
    if u["mfa_active"]=="false" and u["password_enabled"]=="true": print("NO MFA:",u["user"])
```

### Step 3, Detect wildcard policies

```bash
import json
def wild(user):
    for p in iam.list_attached_user_policies(UserName=user)["AttachedPolicies"]:
        v=iam.get_policy(PolicyArn=p["PolicyArn"])["Policy"]["DefaultVersionId"]
        doc=iam.get_policy_version(PolicyArn=p["PolicyArn"],VersionId=v)["PolicyVersion"]["Document"]
        for st in doc["Statement"]:
            if st.get("Effect")=="Allow" and "*" in str(st.get("Action")) and st.get("Resource")=="*":
                print("WILDCARD:",user,p["PolicyName"])
```

## 🧪 Done when

- [ ] A no-MFA user is flagged
- [ ] A 90+ day key is reported
- [ ] AdministratorAccess shows WILDCARD

## 🚀 Stretch goals

- Suggest least-privilege from Access Analyzer
- CSV export
- Weekly Lambda schedule

## 📦 Make it portfolio-ready

> Show the ranked risk table + a remediation playbook.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*