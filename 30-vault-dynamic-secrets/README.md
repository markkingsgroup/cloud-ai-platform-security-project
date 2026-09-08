# Dynamic Secrets with HashiCorp Vault

![](https://img.shields.io/badge/DevOps_/_DevSecOps-5FB878?style=flat-square) ![](https://img.shields.io/badge/Vault-334155?style=flat-square) ![](https://img.shields.io/badge/Docker-334155?style=flat-square) ![](https://img.shields.io/badge/AWS-334155?style=flat-square)

`Week 9` · Difficulty ★★★★☆ · ~8-10 hrs · *Zero to Job-Ready* portfolio project No. 30 of 36

> Run HashiCorp Vault and issue short-lived, auto-expiring credentials instead of static secrets.

## 🌍 The real-world problem

Static secrets in .env files and CI variables leak constantly and never expire. Vault issues dynamic secrets that are created on demand, scoped, and revoked automatically when the lease ends.

## 🎯 Project goals

- Run HashiCorp Vault and issue short-lived, auto-expiring credentials instead of static secrets.
- Have an app fetch a dynamic secret at runtime with nothing stored.
- Demonstrate the senior answer to "how do you manage credentials?"

## 🧠 Skills you will learn

- HashiCorp Vault administration
- Dynamic secrets engines (AWS/database)
- Leases, rotation and revocation
- Runtime secret retrieval (hvac)
- Eliminating static secrets

## 🛠 What you build

A Vault server with the AWS secrets engine issuing short-lived AWS credentials on demand, plus a demo app that fetches a credential at runtime and lets the lease auto-revoke it.

## 🏗 Architecture

```
App / Pipeline (needs creds)   →   Vault (secrets engine)   →   Lease (short-lived)   →   AWS / DB (dynamic cred)
```
*Credentials minted on demand and auto-revoked - nothing static to steal.*

## ✅ Prerequisites

- Docker
- An AWS lab account
- CLI comfort

## ⚙️ Setup

```bash
docker run --cap-add=IPC_LOCK -p 8200:8200 -e VAULT_DEV_ROOT_TOKEN_ID=root -d hashicorp/vault
export VAULT_ADDR=http://127.0.0.1:8200 && export VAULT_TOKEN=root
pip install hvac && vault status
```

Dev-mode Vault is in-memory and unsealed - learning only, never production.

## 🔨 Build it, step by step

### Step 1, Run Vault and set your environment

```bash
docker run --cap-add=IPC_LOCK -p 8200:8200 -e VAULT_DEV_ROOT_TOKEN_ID=root -d hashicorp/vault
export VAULT_ADDR=http://127.0.0.1:8200
export VAULT_TOKEN=root
vault status
```

Dev mode is for learning only - it is unsealed and in-memory. Never use it in production.

### Step 2, Enable the AWS secrets engine

```bash
vault secrets enable aws
vault write aws/config/root access_key=$AWS_KEY secret_key=$AWS_SECRET region=us-east-1
# a role that mints a least-privilege, short-lived user
vault write aws/roles/s3-reader credential_type=iam_user policy_document=@s3-read.json
```

### Step 3, Generate a short-lived credential on demand

```bash
vault read aws/creds/s3-reader
# Key            Value
# lease_duration 1h          <- expires by itself
# access_key     AKIA...
# secret_key     ...
# Vault auto-revokes the IAM user when the lease ends
```

Run it twice - you get two different, independently revocable credentials.

### Step 4, Fetch a dynamic secret from an app

```bash
import hvac
v = hvac.Client(url="http://127.0.0.1:8200", token="root")
creds = v.read("aws/creds/s3-reader")["data"]
# use creds["access_key"] / creds["secret_key"] for this run only - never stored
```

## 🧪 Done when

- [ ] Vault issues an AWS credential that expires on its own
- [ ] the app holds no static secret - it fetches one at runtime
- [ ] revoking the lease immediately kills the credential
- [ ] every issue/revoke is in the Vault audit log

## 🚀 Stretch goals

- Add AppRole auth so the app authenticates without a root token
- Enable the database secrets engine for dynamic DB users
- Wire Vault into the OIDC pipeline (Project 29) so CI gets dynamic creds too

## 📦 Make it portfolio-ready

> Diagram App/Pipeline -> Vault -> short-lived AWS credential. Explain leases and dynamic vs static secrets.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*