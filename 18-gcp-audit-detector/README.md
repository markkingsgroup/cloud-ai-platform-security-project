# GCP Cloud Audit Logs Threat Detector

![](https://img.shields.io/badge/GCP-4285F4?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/BigQuery-334155?style=flat-square) ![](https://img.shields.io/badge/Audit_Logs-334155?style=flat-square)

`Week 16` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 18 of 36

> Query Cloud Audit Logs in BigQuery to detect privilege grants, key creation, and recon.

## 🌍 The real-world problem

GCP Audit Logs are rich but huge. Detection engineering on GCP means querying them (often via a BigQuery sink) for the events that signal compromise.

## 🎯 Project goals

- Query Cloud Audit Logs in BigQuery to detect IAM grants, key creation and recon.
- Write SQL detections over a log sink, GCP-style detection engineering.
- Build the GCP counterpart to your Sentinel/KQL work.

## 🧠 Skills you will learn

- BigQuery and SQL detections
- GCP Cloud Audit Logs structure
- Python BigQuery client
- Detection engineering across clouds
- Time-bounded, cost-aware querying

## 🛠 What you build

SQL detections over an Audit Logs BigQuery sink: IAM grants, SA-key creation, and bucket-permission changes.

## 🏗 Architecture

```
Cloud Audit Logs (sink)   →   BigQuery (store)   →   SQL detections (IAM / keys)   →   Alerts (threats)
```
*GCP detection in BigQuery SQL.*

## ✅ Prerequisites

- Audit log sink to BigQuery (Project 14)
- `pip install google-cloud-bigquery`

## ⚙️ Setup

Needs the audit-log BigQuery sink from Project 14.

```bash
pip install google-cloud-bigquery
bq ls security_logs   # confirm the dataset exists
```

## 🔨 Build it, step by step

### Step 1, Detect IAM policy grants

```bash
-- IAM bindings added in the last 24h
SELECT timestamp, protopayload_auditlog.authenticationInfo.principalEmail AS who,
       protopayload_auditlog.methodName AS method
FROM `security_logs.cloudaudit_googleapis_com_activity`
WHERE protopayload_auditlog.methodName = "SetIamPolicy"
  AND timestamp > TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 24 HOUR)
```

### Step 2, Detect service-account key creation

```bash
SELECT timestamp, protopayload_auditlog.authenticationInfo.principalEmail AS who
FROM `security_logs.cloudaudit_googleapis_com_activity`
WHERE protopayload_auditlog.methodName = "google.iam.admin.v1.CreateServiceAccountKey"
```

### Step 3, Run from Python + alert

```bash
from google.cloud import bigquery
bq=bigquery.Client()
for row in bq.query(open("detections/iam_grant.sql").read()):
    print("IAM GRANT:",row.who,row.method)
```

## 🧪 Done when

- [ ] Granting an IAM role appears in the IAM-grant detection
- [ ] Creating an SA key is detected
- [ ] Each query is documented with its threat

## 🚀 Stretch goals

- Schedule queries with Cloud Scheduler
- Push alerts to Slack
- Map detections to MITRE ATT&CK

## 📦 Make it portfolio-ready

> Include the SQL detections and a note comparing BigQuery+Audit Logs to Sentinel/KQL and CloudTrail.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*