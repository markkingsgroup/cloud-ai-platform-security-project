# Terraform Drift Detection & Auto-Remediation

![](https://img.shields.io/badge/DevOps_/_DevSecOps-5FB878?style=flat-square) ![](https://img.shields.io/badge/Terraform-334155?style=flat-square) ![](https://img.shields.io/badge/GitHub_Actions-334155?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square)

`Week 9` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 33 of 36

> Detect when live cloud config drifts away from your Terraform state, then alert and revert it.

## 🌍 The real-world problem

Someone clicks a change in the console and now reality no longer matches your code - the secure baseline quietly erodes. Drift detection catches it; remediation restores it.

## 🎯 Project goals

- Detect when live cloud config drifts away from Terraform state.
- Alert Slack on drift and open a revert PR to restore the baseline.
- Catch the silent erosion of the secure baseline IaC set up.

## 🧠 Skills you will learn

- Terraform plan + detailed-exitcode
- Scheduled GitHub Actions jobs
- Drift detection and reconciliation
- Slack alerting and revert automation
- Platform reliability practice

## 🛠 What you build

A scheduled GitHub Actions job that runs `terraform plan` in detect-only mode, parses the result, alerts Slack on drift, and opens a revert PR to restore the baseline.

## 🏗 Architecture

```
Schedule (cron)   →   terraform plan (detect drift)   →   Diff (drifted resources)   →   Slack / PR (alert + revert)
```
*Drift from the secure baseline is caught and reverted automatically.*

## ✅ Prerequisites

- Terraform-managed infra (reuse Project 3)
- A GitHub repo
- A Slack webhook

## ⚙️ Setup

Point this at infra Terraform already manages (Project 3).

```bash
git init drift-detect && cd $_
export SLACK_WEBHOOK_URL="https://hooks.slack.com/services/..."
terraform -version
```

## 🔨 Build it, step by step

### Step 1, Schedule a detect-only plan

```bash
# .github/workflows/drift.yml
on:
  schedule:
    - cron: "0 * * * *"     # hourly
jobs:
  drift:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: terraform init
      - run: terraform plan -detailed-exitcode -no-color
```

### Step 2, Detect drift from the exit code

```bash
# terraform plan -detailed-exitcode returns:
#   0 = no changes (no drift)
#   2 = changes present (DRIFT!)
#   1 = error
# capture it:
terraform plan -detailed-exitcode || echo "exit=$?"
```

### Step 3, Alert Slack on drift

```bash
import json, os, urllib.request
def alert(resources):
    msg = {"text": "Drift detected: " + ", ".join(resources)}
    req = urllib.request.Request(os.environ["SLACK_WEBHOOK_URL"],
        data=json.dumps(msg).encode(), headers={"Content-Type":"application/json"})
    urllib.request.urlopen(req)
```

### Step 4, Open an auto-revert PR

On drift, restore the baseline by re-applying the code - either open a PR for review or apply directly for low-risk resources.

```bash
terraform apply -auto-approve   # restores live infra to match the code
# or: open a PR that re-asserts the Terraform so a human approves the revert
```

Auto-apply only for low-risk resources; for anything sensitive, alert and open a PR instead of reverting blind.

## 🧪 Done when

- [ ] a manual console change is detected as drift (exit code 2)
- [ ] Slack receives a clear alert listing the drifted resources
- [ ] a revert restores the Terraform baseline
- [ ] no drift means the job stays silent

## 🚀 Stretch goals

- Log drift over time to show a posture trend
- Auto-apply only allow-listed low-risk resources
- Extend drift detection to your Azure and GCP Terraform state

## 📦 Make it portfolio-ready

> Diagram schedule -> terraform plan -> drift? -> Slack + revert PR. Explain -detailed-exitcode.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*