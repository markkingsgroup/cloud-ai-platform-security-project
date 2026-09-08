# AWS Secure Account Baseline (Terraform)

![](https://img.shields.io/badge/AWS-FF9900?style=flat-square) ![](https://img.shields.io/badge/Terraform-334155?style=flat-square) ![](https://img.shields.io/badge/AWS-334155?style=flat-square) ![](https://img.shields.io/badge/Checkov-334155?style=flat-square)

`Week 8` · Difficulty ★★★☆☆ · ~8-10 hrs · *Zero to Job-Ready* portfolio project No. 3 of 36

> IaC that enforces a CIS-aligned security baseline on any AWS account.

## 🌍 The real-world problem

Clicking settings in the console is error-prone and not repeatable. Teams need a baseline as code applied identically to every account.

## 🎯 Project goals

- Codify a CIS-aligned AWS account baseline so security is the default, not an afterthought.
- Apply the identical secure configuration to any account with a single command.
- Gate the infrastructure-as-code with Checkov so the baseline is scanned before it ships.

## 🧠 Skills you will learn

- Terraform / Infrastructure as Code
- AWS security services (CloudTrail, GuardDuty, Config, IAM)
- IaC scanning with Checkov
- CIS AWS Benchmark mapping
- Repeatable, reviewable cloud provisioning

## 🛠 What you build

A Terraform module enabling CloudTrail (encrypted), GuardDuty, Config, S3 Block Public Access, and a password policy - validated by Checkov.

## 🏗 Architecture

```
Terraform (baseline as code)   →   Checkov (IaC gate)   →   AWS (CloudTrail / GuardDuty / Config)
```
*Infra as code, validated before it ships.*

## ✅ Prerequisites

- Terraform + AWS CLI
- A lab AWS account with a $5 billing alarm

## ⚙️ Setup

```bash
sudo apt install -y terraform awscli
aws configure   # lab admin (setup only)
terraform -version && aws sts get-caller-identity
```

Lab account only. Set a $5 billing alarm; `terraform destroy` when done.

## 🔨 Build it, step by step

### Step 1, Password policy + provider

```bash
provider "aws" { region = "us-east-1" }
resource "aws_iam_account_password_policy" "p" {
  minimum_password_length=14
  require_symbols=true; require_numbers=true; require_uppercase_characters=true
  password_reuse_prevention=5; max_password_age=90
}
```

### Step 2, Encrypted CloudTrail + locked bucket

```bash
resource "aws_s3_bucket" "logs" { bucket = "trail-${data.aws_caller_identity.me.account_id}" }
resource "aws_s3_bucket_public_access_block" "l" { bucket=aws_s3_bucket.logs.id
  block_public_acls=true; block_public_policy=true; ignore_public_acls=true; restrict_public_buckets=true }
resource "aws_cloudtrail" "t" { name="org-trail"; s3_bucket_name=aws_s3_bucket.logs.id
  is_multi_region_trail=true; enable_log_file_validation=true }
```

### Step 3, Detective controls

```bash
resource "aws_guardduty_detector" "g" { enable = true }
```

### Step 4, Scan IaC, then apply

```bash
pip install checkov && checkov -d . --compact   # fix every CRITICAL/HIGH first
terraform init && terraform plan && terraform apply
```

Run `terraform destroy` when finished to avoid charges.

## 🧪 Done when

- [ ] `checkov -d .` shows 0 failed checks
- [ ] CloudTrail status is logging
- [ ] GuardDuty enabled

## 🚀 Stretch goals

- Add Security Hub + CIS standard
- Make it a reusable module
- Plan in GitHub Actions on every PR

## 📦 Make it portfolio-ready

> Include the architecture diagram, CIS mapping, and Checkov "0 failed" output.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*