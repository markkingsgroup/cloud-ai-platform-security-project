# GCP Organization Security Baseline

![](https://img.shields.io/badge/GCP-4285F4?style=flat-square) ![](https://img.shields.io/badge/Terraform-334155?style=flat-square) ![](https://img.shields.io/badge/GCP-334155?style=flat-square) ![](https://img.shields.io/badge/Org_Policy-334155?style=flat-square)

`Week 15` · Difficulty ★★★☆☆ · ~8-10 hrs · *Zero to Job-Ready* portfolio project No. 14 of 36

> Terraform that applies org-level guardrails: deny public access, enforce CMEK, central logging.

## 🌍 The real-world problem

New GCP projects allow public buckets, default service accounts with broad rights, and scattered logs. Org Policy constraints fix this at the top so every project inherits the guardrails.

## 🎯 Project goals

- Apply org-level GCP guardrails: deny public access, enforce uniform bucket access, central logging.
- Make every new project inherit a secure baseline from the top.
- Set up the BigQuery log sink that powers GCP detection.

## 🧠 Skills you will learn

- Terraform for GCP (google provider)
- GCP Org Policy constraints
- Resource hierarchy (org/folder/project) inheritance
- Central log sinks to BigQuery
- CIS GCP Benchmark mapping

## 🛠 What you build

Terraform that sets org-policy constraints (no public IPs, uniform bucket access), and a central log sink to BigQuery.

## 🏗 Architecture

```
Terraform (org policy)   →   Constraints (no public / uniform)   →   Log Sink (to BigQuery)   →   Projects (inherit)
```
*Guardrails at the org level.*

## ✅ Prerequisites

- GCP free account + a project
- `gcloud` CLI + Terraform
- Org-level or project-level IAM as available

## ⚙️ Setup

```bash
gcloud auth application-default login
gcloud config set project <PROJECT_ID>
mkdir gcp-baseline && cd $_ && terraform init
```

Some constraints need org-level rights; apply project-level if that is all you have.

## 🔨 Build it, step by step

### Step 1, Authenticate + scaffold

```bash
gcloud auth application-default login
gcloud config set project <PROJECT_ID>
mkdir gcp-baseline && cd $_ && terraform init
```

```bash
provider "google" { project = "<PROJECT_ID>" region = "us-central1" }
```

### Step 2, Apply Org Policy constraints

```bash
resource "google_project_organization_policy" "no_public_ip" {
  project="<PROJECT_ID>"; constraint="compute.vmExternalIpAccess"
  list_policy { deny { all = true } }
}
resource "google_project_organization_policy" "uniform_bucket" {
  project="<PROJECT_ID>"; constraint="storage.uniformBucketLevelAccess"
  boolean_policy { enforced = true }
}
```

### Step 3, Central log sink to BigQuery

```bash
resource "google_logging_project_sink" "audit" {
  name="audit-sink"
  destination="bigquery.googleapis.com/projects/<PROJECT_ID>/datasets/security_logs"
  filter="logName:cloudaudit.googleapis.com"
}
```

### Step 4, Apply + verify

```bash
terraform apply
gcloud resource-manager org-policies list --project <PROJECT_ID>
```

Some org constraints need organization-level rights; if you only have a project, apply project-level policies and note the limitation.

## 🧪 Done when

- [ ] VM external-IP creation is denied by policy
- [ ] Uniform bucket-level access is enforced
- [ ] Audit logs are flowing to BigQuery

## 🚀 Stretch goals

- Add CMEK enforcement
- Restrict service-account key creation
- Add a deny on default networks

## 📦 Make it portfolio-ready

> Diagram org -> folder -> project inheritance. Map to the CIS GCP Benchmark.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*