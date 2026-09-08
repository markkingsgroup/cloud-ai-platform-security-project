# Multi-Cloud IaC Security Pipeline

![](https://img.shields.io/badge/Multi--Cloud-7B42BC?style=flat-square) ![](https://img.shields.io/badge/Checkov-334155?style=flat-square) ![](https://img.shields.io/badge/tfsec-334155?style=flat-square) ![](https://img.shields.io/badge/GitHub_Actions-334155?style=flat-square)

`Week 16` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 26 of 36

> A CI pipeline that scans Terraform for AWS, Azure, and GCP with multiple engines.

## 🌍 The real-world problem

Terraform spans clouds, and one scanner misses things another catches. A robust gate runs multiple engines across every cloud's IaC on each PR.

## 🎯 Project goals

- Run Checkov + tfsec across AWS, Azure and GCP Terraform on every pull request.
- Block a misconfiguration in any cloud before it merges.
- Extend the DevSecOps gate across the whole cloud estate.

## 🧠 Skills you will learn

- GitHub Actions across multiple clouds
- Checkov + tfsec (two-engine scanning)
- SARIF reporting + the GitHub Security tab
- Multi-cloud IaC structure
- Defense-in-depth scanning

## 🛠 What you build

A GitHub Actions workflow running Checkov + tfsec across aws/, azure/, and gcp/ Terraform folders, with a merged SARIF report.

## 🏗 Architecture

```
aws / azure / gcp TF (infra)   →   Checkov + tfsec (two engines)   →   SARIF (merged)   →   Gate (block critical)
```
*Secure every cloud's IaC on each PR.*

## ✅ Prerequisites

- Terraform for all 3 clouds (reuse Projects 3, 9, 14)
- A GitHub repo

## ⚙️ Setup

```bash
git init multicloud-iac && cd $_
mkdir -p infra/{aws,azure,gcp} .github/workflows
# drop your Project 3/9/14 Terraform into each folder
```

## 🔨 Build it, step by step

### Step 1, Structure the repo by cloud

```bash
infra/
  aws/    main.tf
  azure/  main.tf
  gcp/    main.tf
.github/workflows/iac.yml
```

### Step 2, Run Checkov across all clouds

```bash
- uses: bridgecrewio/checkov-action@master
  with: { directory: infra/, framework: terraform, output_format: sarif, output_file_path: checkov.sarif }
```

### Step 3, Add tfsec as a second engine

```bash
- uses: aquasecurity/tfsec-sarif-action@v0.1.4
  with: { sarif_file: tfsec.sarif }
```

### Step 4, Upload merged results + gate

```bash
- uses: github/codeql-action/upload-sarif@v3
  with: { sarif_file: checkov.sarif }
- uses: github/codeql-action/upload-sarif@v3
  with: { sarif_file: tfsec.sarif }
# fail the job if either engine reports a CRITICAL
```

## 🧪 Done when

- [ ] Both engines run on AWS, Azure, and GCP folders
- [ ] Findings appear in the GitHub Security tab
- [ ] A planted misconfig in any cloud fails the PR

## 🚀 Stretch goals

- Add a matrix per cloud for parallel runs
- Add OPA/Conftest policy checks
- Generate a combined HTML report artifact

## 📦 Make it portfolio-ready

> Show the matrix run and explain why two engines beat one. Note cloud-specific checks each catches.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*