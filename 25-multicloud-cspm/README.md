# Multi-Cloud CSPM Dashboard

![](https://img.shields.io/badge/Multi--Cloud-7B42BC?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/AWS-334155?style=flat-square) ![](https://img.shields.io/badge/Azure-334155?style=flat-square) ![](https://img.shields.io/badge/GCP-334155?style=flat-square)

`Week 16` · Difficulty ★★★★☆ · ~10-14 hrs · *Zero to Job-Ready* portfolio project No. 25 of 36

> One scanner that finds public storage, open firewalls, and IAM risks across AWS, Azure, and GCP.

## 🌍 The real-world problem

Each cloud has its own console and its own way to be misconfigured. Security teams need one normalized view of posture across AWS, Azure, and GCP.

## 🎯 Project goals

- Build one scanner that finds public storage, open firewalls and IAM risks across AWS, Azure and GCP.
- Normalize findings into a single severity scale and one posture score.
- Produce a portfolio centerpiece that reads as genuinely senior work.

## 🧠 Skills you will learn

- Multi-cloud SDKs (boto3, Azure, GCP)
- Finding normalization and scoring
- Pluggable collector architecture
- HTML dashboard reporting
- Cross-cloud posture management

## 🛠 What you build

A pluggable scanner with one collector per cloud, normalized findings, and a single HTML dashboard with a per-cloud and overall score.

## 🏗 Architecture

```
AWS / Azure / GCP (collectors)   →   Normalizer (one schema)   →   Score (per cloud + overall)   →   Dashboard (one view)
```
*One posture view across three clouds.*

## ✅ Prerequisites

- Read-only credentials in all three clouds
- Projects 6, 12, 17 (reuse their collectors)
- `boto3`, `azure-mgmt-storage`, `google-cloud-storage`

## ⚙️ Setup

Configure read-only creds in all three clouds first.

```bash
pip install boto3 azure-identity azure-mgmt-storage google-cloud-storage
aws sts get-caller-identity && az account show && gcloud config get-value project
```

## 🔨 Build it, step by step

### Step 1, Define a normalized finding

```bash
from dataclasses import dataclass
@dataclass
class Finding:
    cloud: str; resource: str; check: str; severity: str  # CRITICAL/HIGH/MED/LOW
```

### Step 2, Wrap each cloud collector

Reuse your S3, Azure Storage, and GCS auditors - each returns a list of `Finding`.

```bash
def collect():
    out=[]
    out += aws_storage_findings()     # from Project 6
    out += azure_storage_findings()   # from Project 12
    out += gcp_storage_findings()     # from Project 17
    return out
```

### Step 3, Score per cloud + overall

```bash
from collections import Counter
def score(findings):
    by_cloud=Counter(f.cloud for f in findings if f.severity in ("CRITICAL","HIGH"))
    print("High/critical by cloud:",dict(by_cloud))
    grade = "A" if not by_cloud else "C" if sum(by_cloud.values())<5 else "F"
    print("Overall posture:",grade)
```

### Step 4, Render one HTML dashboard

```bash
# group findings by cloud, color by severity, show per-cloud cards + a combined table
open("cspm.html","w").write(render(findings))
```

## 🧪 Done when

- [ ] Findings appear from all three clouds in one report
- [ ] Severities are normalized across clouds
- [ ] The dashboard shows a per-cloud and overall score

## 🚀 Stretch goals

- Add IAM + firewall collectors per cloud
- Schedule it and store history
- Compare your output to a commercial CSPM (Wiz/Prowler/ScoutSuite)

## 📦 Make it portfolio-ready

> This is a portfolio centerpiece - lead with the dashboard screenshot and the architecture (3 collectors -> normalizer -> dashboard).

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*