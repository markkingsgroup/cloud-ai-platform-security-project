# AWS CloudTrail Log Analyzer

![](https://img.shields.io/badge/AWS-FF9900?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/boto3-334155?style=flat-square) ![](https://img.shields.io/badge/CloudTrail-334155?style=flat-square)

`Week 7` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 5 of 36

> Surfaces risky AWS activity - root usage, IAM changes, off-hours and new-geo logins.

## 🌍 The real-world problem

CloudTrail records every API call but the raw log is enormous. Finding the security needles by hand is slow.

## 🎯 Project goals

- Turn a firehose of CloudTrail API logs into a short, useful triage list.
- Surface the security needles: root usage, IAM changes, off-hours and new-geo logins.
- Build the kind of detection tool a cloud SOC runs every day.

## 🧠 Skills you will learn

- Python with boto3
- AWS CloudTrail and API-event analysis
- Detection logic and anomaly flagging
- Log triage at scale
- Read-only, least-privilege tooling

## 🛠 What you build

A boto3 tool that fetches events and reports top calls, logins, IAM changes, and anomalies.

## 🏗 Architecture

```
CloudTrail (events)   →   boto3 fetch (window)   →   Analyzer (rank / risk)   →   Report (triage)
```
*Huge log -> short triage list.*

## ✅ Prerequisites

- CloudTrail enabled
- IAM read access
- `pip install boto3`

## ⚙️ Setup

```bash
pip install boto3
aws configure --profile readonly
export AWS_PROFILE=readonly
aws cloudtrail describe-trails --query "trailList[].Name"
```

## 🔨 Build it, step by step

### Step 1, Fetch a window

```bash
import boto3
from datetime import datetime,timedelta
ct=boto3.client("cloudtrail")
def fetch(h=24):
    e=datetime.utcnow(); ev=[]
    for pg in ct.get_paginator("lookup_events").paginate(StartTime=e-timedelta(hours=h),EndTime=e):
        ev+=pg["Events"]
    return ev
```

### Step 2, Rank API calls

```bash
from collections import Counter
def top(ev): [print(c,n) for n,c in Counter(e["EventName"] for e in ev).most_common(10)]
```

### Step 3, Flag high-risk + anomalies

```bash
import json
def risky(ev):
    for e in ev:
        if e["EventSource"]=="iam.amazonaws.com": print("IAM CHANGE:",e["EventName"])
        if e.get("Username")=="root": print("ROOT USED:",e["EventName"])
        d=json.loads(e["CloudTrailEvent"])
        if e["EventName"]=="ConsoleLogin" and e["EventTime"].hour<6: print("Off-hours login")
```

## 🧪 Done when

- [ ] A console login appears
- [ ] A test IAM user create/delete shows IAM CHANGE
- [ ] Root activity is flagged

## 🚀 Stretch goals

- Geolocate source IPs
- Daily Slack summary
- Map events to ATT&CK

## 📦 Make it portfolio-ready

> Show anonymized sample output; note which findings to escalate.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*