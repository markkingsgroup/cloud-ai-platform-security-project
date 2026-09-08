# AWS S3 Public-Exposure Auditor

![](https://img.shields.io/badge/AWS-FF9900?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/boto3-334155?style=flat-square) ![](https://img.shields.io/badge/S3-334155?style=flat-square)

`Week 6` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 6 of 36

> Scans every S3 bucket for public exposure, missing encryption, and no versioning.

## 🌍 The real-world problem

Misconfigured S3 has leaked billions of records. Public ACLs and permissive policies are easy to create and hard to spot across dozens of buckets.

## 🎯 Project goals

- Scan every S3 bucket in an account for public exposure, missing encryption and no versioning.
- Grade each bucket A-F so the risky ones jump out.
- Catch the exact misconfiguration behind headline data leaks.

## 🧠 Skills you will learn

- Python with boto3
- S3 security (Block Public Access, policies, encryption)
- Read-only auditing and grading
- Account-wide resource enumeration
- Cloud data-exposure detection

## 🛠 What you build

A boto3 scanner that grades each bucket A-F on public access, encryption, versioning, and logging.

## 🏗 Architecture

```
All S3 buckets (inventory)   →   Auditor (BPA / ACL / encryption)   →   Report (grade A-F)
```
*Read-only scan of every bucket.*

## ✅ Prerequisites

- Read-only S3 IAM user
- `pip install boto3`

## ⚙️ Setup

Create a read-only IAM user so the tool can never modify a bucket.

```bash
aws configure --profile s3audit
pip install boto3 && export AWS_PROFILE=s3audit
```

## 🔨 Build it, step by step

### Step 1, List buckets with a read-only client

Use an IAM principal with S3 read + GetBucketPublicAccessBlock only - least privilege for your own tools too.

```bash
import boto3,json
s3=boto3.client("s3")
buckets=[b["Name"] for b in s3.list_buckets()["Buckets"]]
```

### Step 2, Check Block Public Access + policy

```bash
def blocked(n):
    try: return all(s3.get_public_access_block(Bucket=n)["PublicAccessBlockConfiguration"].values())
    except: return False
def pol_public(n):
    try:
        p=json.loads(s3.get_bucket_policy(Bucket=n)["Policy"])
        return any(st.get("Principal")=="*" and st["Effect"]=="Allow" for st in p["Statement"])
    except: return False
```

### Step 3, Grade and report

```bash
def grade(n):
    pub=(not blocked(n)) and pol_public(n)
    g="F" if pub else "A"
    print(f"[{g}] {n}")
for b in buckets: grade(b)
```

## 🧪 Done when

- [ ] A public test bucket grades F
- [ ] A locked bucket grades A
- [ ] Runs read-only, never modifies

## 🚀 Stretch goals

- HTML + JSON output
- A guarded --remediate flag
- All regions + multiple accounts

## 📦 Make it portfolio-ready

> Show a report table + remediation steps per finding.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*