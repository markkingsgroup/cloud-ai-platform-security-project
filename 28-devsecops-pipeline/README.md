# DevSecOps CI/CD Security Pipeline

![](https://img.shields.io/badge/Multi--Cloud-7B42BC?style=flat-square) ![](https://img.shields.io/badge/GitHub_Actions-334155?style=flat-square) ![](https://img.shields.io/badge/Trivy-334155?style=flat-square) ![](https://img.shields.io/badge/Bandit-334155?style=flat-square)

`Week 12` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 28 of 36

> A reference pipeline with four security gates on every pull request.

## 🌍 The real-world problem

Security bolted on after deployment is late and expensive. Mature teams run secrets, IaC, dependency, and code scans on every PR and block merges on critical findings.

## 🎯 Project goals

- Build a reference pipeline with four security gates on every pull request.
- Scan secrets, IaC, dependencies and code, and block merges on critical findings.
- Give a working answer to "show me your CI/CD security".

## 🧠 Skills you will learn

- GitHub Actions pipeline design
- Gitleaks (secrets), Checkov (IaC)
- Trivy (dependencies), Bandit (SAST)
- Branch protection + blocking gates
- End-to-end shift-left security

## 🛠 What you build

A sample repo with a workflow running Gitleaks, Checkov, Trivy, and Bandit, surfacing results in the Security tab.

## 🏗 Architecture

```
Pull Request (push)   →   4 Gates (secrets / IaC / deps / SAST)   →   Security Gate (block critical)   →   Merge (clean)
```
*Four scans on every PR.*

## ✅ Prerequisites

- A GitHub repo with Terraform + Python
- Basic YAML

## ⚙️ Setup

You need a GitHub repo with some Terraform and Python.

```bash
git init devsecops-demo && cd $_
mkdir -p .github/workflows infra
```

## 🔨 Build it, step by step

### Step 1, Secrets + IaC gates

```bash
jobs:
  secrets:
    steps: [{uses: actions/checkout@v4, with: {fetch-depth: 0}}, {uses: gitleaks/gitleaks-action@v2}]
  iac:
    steps: [{uses: actions/checkout@v4}, {uses: bridgecrewio/checkov-action@master, with: {directory: infra/}}]
```

### Step 2, Dependency + SAST gates

```bash
  deps:
    steps: [{uses: aquasecurity/trivy-action@master, with: {scan-type: fs, format: sarif, output: t.sarif}}]
  sast:
    steps: [{run: "pip install bandit && bandit -r . -f sarif -o b.sarif || true"}]
```

### Step 3, Blocking gate

```bash
  gate:
    needs: [secrets, iac, deps, sast]
    steps: [{run: "echo Review Security tab. Critical = no merge."}]
```

### Step 4, Prove each gate catches something

Commit a fake key, an open SG, a vulnerable dependency, and an eval(user_input) - watch all four light up, then fix them green.

## 🧪 Done when

- [ ] All four scanners run on a PR
- [ ] Findings appear in the Security tab
- [ ] A planted secret/CVE/misconfig is caught

## 🚀 Stretch goals

- Require the gate via branch protection
- Add CodeQL
- Post a PR summary comment

## 📦 Make it portfolio-ready

> Embed the four green checks and the Security tab; explain each gate in one line.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*