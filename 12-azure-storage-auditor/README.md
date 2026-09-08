# Azure Storage Public-Exposure Auditor

![](https://img.shields.io/badge/Azure-0089D6?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/Azure_SDK-334155?style=flat-square) ![](https://img.shields.io/badge/Storage-334155?style=flat-square)

`Week 14` · Difficulty ★★★☆☆ · ~5-7 hrs · *Zero to Job-Ready* portfolio project No. 12 of 36

> Scans every storage account for public blob access, HTTP, and missing encryption.

## 🌍 The real-world problem

Azure storage accounts can allow anonymous public blob access and insecure HTTP. Across many accounts this is easy to miss and routinely leaks data.

## 🎯 Project goals

- Scan every storage account for public blob access, HTTP and weak network rules.
- Grade accounts so the Azure equivalent of the S3 leak surfaces fast.
- Reuse your auditing pattern on a third storage platform.

## 🧠 Skills you will learn

- Python with the Azure SDK
- Azure Storage security (public access, secure transfer)
- Network-rule and private-endpoint review
- Read-only multi-account scanning
- Cross-cloud auditing patterns

## 🛠 What you build

An Azure SDK scanner that grades each storage account on public access, secure transfer, and network rules.

## 🏗 Architecture

```
Storage accounts (inventory)   →   Auditor (public / HTTP / network)   →   Report (grade A-F)
```
*The Azure blob-leak check.*

## ✅ Prerequisites

- Azure subscription
- `pip install azure-identity azure-mgmt-storage`
- Reader role on the subscription

## ⚙️ Setup

```bash
pip install azure-identity azure-mgmt-storage
az login   # DefaultAzureCredential will use this session
```

## 🔨 Build it, step by step

### Step 1, Authenticate with the SDK

```bash
from azure.identity import DefaultAzureCredential
from azure.mgmt.storage import StorageManagementClient
cred=DefaultAzureCredential()
sc=StorageManagementClient(cred,"<SUB_ID>")
```

### Step 2, Enumerate accounts + risky settings

```bash
for acct in sc.storage_accounts.list():
    issues=[]
    if acct.allow_blob_public_access: issues.append("PUBLIC BLOB ACCESS")
    if not acct.enable_https_traffic_only: issues.append("HTTP allowed")
    if acct.network_rule_set and acct.network_rule_set.default_action=="Allow": issues.append("open network rules")
    g="F" if "PUBLIC BLOB ACCESS" in str(issues) else ("A" if not issues else "C")
    print(f"[{g}] {acct.name}: {issues or 'ok'}")
```

### Step 3, Check container-level anonymous access

```bash
# For flagged accounts, list containers with public access:
from azure.mgmt.storage import StorageManagementClient
# blob_services.containers.list(rg, account) -> container.public_access in (Blob, Container)
```

## 🧪 Done when

- [ ] An account with public blob access grades F
- [ ] An HTTP-allowed account is flagged
- [ ] Read-only - never modifies an account

## 🚀 Stretch goals

- Add a guarded remediation flag
- Cross-subscription scan
- Export to the multi-cloud CSPM (Project 19)

## 📦 Make it portfolio-ready

> Show the grade table + remediation (disable public access, enforce HTTPS, set default-deny network rules).

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*