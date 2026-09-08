# Keyless CI/CD Deploy Pipeline (GitHub Actions + OIDC)

![](https://img.shields.io/badge/DevOps_/_DevSecOps-5FB878?style=flat-square) ![](https://img.shields.io/badge/GitHub_Actions-334155?style=flat-square) ![](https://img.shields.io/badge/OIDC-334155?style=flat-square) ![](https://img.shields.io/badge/AWS-334155?style=flat-square) ![](https://img.shields.io/badge/Terraform-334155?style=flat-square)

`Week 5` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 29 of 36

> A production-style CI/CD pipeline that deploys to AWS with zero long-lived keys, using GitHub OIDC.

## 🌍 The real-world problem

The most common CI/CD breach is a long-lived cloud key pasted into a repo or a CI secret, then leaked by a bot within minutes. OIDC removes the secret entirely: the pipeline proves its identity and gets a short-lived role at runtime.

## 🎯 Project goals

- Build a CI/CD pipeline that deploys to AWS with zero long-lived keys, using GitHub OIDC.
- Gate every change behind a pull request and a passing check.
- Answer the interview question "how does your pipeline auth to the cloud?" with proof.

## 🧠 Skills you will learn

- GitHub Actions CI/CD
- OIDC federation to AWS (keyless auth)
- Terraform plan/apply gating
- Branch protection + least-privilege roles
- Secretless pipeline design

## 🛠 What you build

A GitHub Actions workflow that assumes an IAM role via OIDC and runs `terraform plan` on every PR and `apply` on merge, behind branch protection - with no access keys anywhere.

## 🏗 Architecture

```
GitHub Actions (on: PR)   →   OIDC token (short-lived)   →   AWS IAM Role (assume-role)   →   Terraform (plan / apply)
```
*No static keys - the pipeline proves its identity at runtime.*

## ✅ Prerequisites

- A GitHub repo
- An AWS lab account
- Terraform basics (reuse Project 3)

## ⚙️ Setup

A GitHub repo with Terraform and an AWS lab account.

```bash
git init oidc-deploy && cd $_
mkdir -p .github/workflows infra
aws sts get-caller-identity   # setup only - the pipeline itself uses no static keys
```

## 🔨 Build it, step by step

### Step 1, Register GitHub as an OIDC provider in AWS

Add the GitHub token issuer as an OIDC identity provider, then create a role only your repo can assume.

```bash
aws iam create-open-id-connect-provider --url https://token.actions.githubusercontent.com --client-id-list sts.amazonaws.com
# Role trust condition scopes it to your repo + branch:
#   "token.actions.githubusercontent.com:sub": "repo:ORG/REPO:ref:refs/heads/main"
```

### Step 2, Let the workflow request a short-lived role

```bash
# .github/workflows/deploy.yml
permissions:
  id-token: write     # REQUIRED for OIDC
  contents: read
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::ACCT:role/gha-deploy
          aws-region: us-east-1
```

### Step 3, Plan on every PR, apply only on merge

```bash
      - run: terraform init
      - run: terraform plan          # on pull_request
      # apply runs only on push to main, after the plan is reviewed
      - if: github.ref == 'refs/heads/main'
        run: terraform apply -auto-approve
```

### Step 4, Lock it down

- Require a PR + a passing check before merge (branch protection).
- Scope the gha-deploy role to exactly the actions Terraform needs - least privilege.
- Pin the trust policy to one repo and branch so no other repo can assume it.

Never add AWS_ACCESS_KEY_ID / AWS_SECRET_ACCESS_KEY as repo secrets - the whole point is that there is no static key.

## 🧪 Done when

- [ ] the workflow assumes the role and runs with NO static keys stored anywhere
- [ ] `aws sts get-caller-identity` in the job shows the assumed role
- [ ] plan runs on a PR; apply only runs after merge to main
- [ ] the trust policy is scoped to your repo + branch

## 🚀 Stretch goals

- Add GitHub Environments with a manual approval gate before apply
- Scope a separate role per environment (dev/prod)
- Add the same OIDC pattern for Azure (federated credentials) and GCP (workload identity federation)

## 📦 Make it portfolio-ready

> Diagram GitHub Actions -> OIDC token -> AWS role -> Terraform. Explain the trust condition and why there is no secret to leak.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*