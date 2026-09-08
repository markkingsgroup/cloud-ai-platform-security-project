# AWS IaC Security Scanner

![](https://img.shields.io/badge/AWS-FF9900?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/Checkov-334155?style=flat-square) ![](https://img.shields.io/badge/CI/CD-334155?style=flat-square)

`Week 6` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 4 of 36

> A Checkov wrapper that emits a severity-sorted HTML report and a CI gate.

## 🌍 The real-world problem

Raw scanner output is noisy. Teams need prioritized findings, a readable report, and an automated gate that blocks insecure infra from merging.

## 🎯 Project goals

- Turn noisy scanner output into a prioritized, readable security report.
- Build a CI gate that blocks insecure infrastructure from ever merging.
- Demonstrate shift-left security working in a real pipeline.

## 🧠 Skills you will learn

- Python (JSON parsing, reporting)
- Static analysis of IaC with Checkov
- CI/CD gating with GitHub Actions
- Severity triage and HTML reporting
- Shift-left security practice

## 🛠 What you build

A CLI that runs Checkov, parses JSON, emits color-coded HTML, and fails CI on CRITICAL.

## 🏗 Architecture

```
Terraform dir (infra)   →   Checkov (scan)   →   Parser (severity)   →   Report (+ CI gate)
```
*Prioritized, gating report.*

## ✅ Prerequisites

- Python
- `pip install checkov`
- A Terraform dir (reuse Project 3)

## ⚙️ Setup

```bash
python3 -m venv venv && source venv/bin/activate
pip install checkov && checkov --version
```

## 🔨 Build it, step by step

### Step 1, Run Checkov, capture JSON

```bash
import subprocess,json
def scan(p): return json.loads(subprocess.run(["checkov","-d",p,"-o","json","--quiet"],capture_output=True,text=True).stdout or "{}")
```

### Step 2, Bucket by severity

```bash
def by_sev(r):
    sev={"CRITICAL":[],"HIGH":[],"MEDIUM":[],"LOW":[]}
    for c in r.get("results",{}).get("failed_checks",[]):
        sev.setdefault(c.get("severity","MEDIUM"),[]).append(c)
    return sev
```

### Step 3, Gate the build

```bash
import sys
sev=by_sev(scan("."))
print({k:len(v) for k,v in sev.items()})
sys.exit(1 if sev.get("CRITICAL") else 0)
```

### Step 4, Wire into GitHub Actions

```bash
# .github/workflows/iac.yml
on: [pull_request]
jobs:
  scan: { runs-on: ubuntu-latest, steps: [
    {uses: actions/checkout@v4},
    {run: "pip install checkov && python scanner.py"} ]}
```

## 🧪 Done when

- [ ] A bad TF file produces CRITICAL rows
- [ ] Exit code 1 on CRITICAL (fails CI)
- [ ] HTML report sorted by severity

## 🚀 Stretch goals

- Add tfsec as a second engine
- Post a PR comment
- Support CloudFormation + K8s

## 📦 Make it portfolio-ready

> Embed the HTML report screenshot and a blocked-PR screenshot; explain "shift-left".

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*