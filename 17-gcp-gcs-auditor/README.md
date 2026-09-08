# GCS Bucket Public-Exposure Auditor

![](https://img.shields.io/badge/GCP-4285F4?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/GCP_SDK-334155?style=flat-square) ![](https://img.shields.io/badge/GCS-334155?style=flat-square)

`Week 15` · Difficulty ★★☆☆☆ · ~5-7 hrs · *Zero to Job-Ready* portfolio project No. 17 of 36

> Scans Cloud Storage buckets for allUsers/allAuthenticatedUsers and missing uniform access.

## 🌍 The real-world problem

GCS buckets granting allUsers are public to the internet. Without uniform bucket-level access, per-object ACLs make exposure hard to reason about.

## 🎯 Project goals

- Scan Cloud Storage buckets for allUsers / allAuthenticatedUsers exposure.
- Flag ACL-based (non-uniform) buckets that make exposure hard to reason about.
- Complete the trio of cloud storage-leak auditors (S3, Blob, GCS).

## 🧠 Skills you will learn

- Python with the GCP Storage SDK
- GCS IAM and bucket ACLs
- Public-exposure detection
- Uniform vs fine-grained access models
- Cross-cloud storage auditing

## 🛠 What you build

A google-cloud-storage script that flags public IAM members and ACL-based (non-uniform) buckets.

## 🏗 Architecture

```
GCS buckets (inventory)   →   Auditor (allUsers / uniform)   →   Report (public exposure)
```
*The GCS bucket-leak check.*

## ✅ Prerequisites

- GCP project + `pip install google-cloud-storage`
- Storage Admin Viewer / Viewer role

## ⚙️ Setup

```bash
pip install google-cloud-storage
gcloud auth application-default login
```

## 🔨 Build it, step by step

### Step 1, List buckets

```bash
from google.cloud import storage
client=storage.Client()
buckets=list(client.list_buckets())
```

### Step 2, Check public IAM members

```bash
for b in buckets:
    policy=b.get_iam_policy(requested_policy_version=3)
    public=[m for binding in policy.bindings for m in binding["members"]
            if m in ("allUsers","allAuthenticatedUsers")]
    if public: print("PUBLIC:",b.name,public)
```

### Step 3, Flag non-uniform access

```bash
for b in buckets:
    b.reload()
    if not b.iam_configuration.uniform_bucket_level_access_enabled:
        print("ACL-BASED (risky):",b.name)
```

## 🧪 Done when

- [ ] A bucket with allUsers is flagged PUBLIC
- [ ] Non-uniform buckets are reported
- [ ] Read-only audit

## 🚀 Stretch goals

- Add a guarded remediation flag
- Feed results into the multi-cloud CSPM
- Check object-level ACLs for flagged buckets

## 📦 Make it portfolio-ready

> Explain allUsers vs allAuthenticatedUsers and why uniform access matters; show remediation commands.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*