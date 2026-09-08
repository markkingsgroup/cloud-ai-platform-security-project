# Azure Secure Landing Zone

![](https://img.shields.io/badge/Azure-0089D6?style=flat-square) ![](https://img.shields.io/badge/Terraform-334155?style=flat-square) ![](https://img.shields.io/badge/Azure-334155?style=flat-square) ![](https://img.shields.io/badge/Azure_Policy-334155?style=flat-square)

`Week 13` · Difficulty ★★★☆☆ · ~8-10 hrs · *Zero to Job-Ready* portfolio project No. 9 of 36

> IaC that stands up a security-baselined Azure environment with guardrails.

## 🌍 The real-world problem

New Azure subscriptions have no guardrails: public storage allowed, no diagnostic logs, weak RBAC. A "landing zone" bakes in security before workloads arrive.

## 🎯 Project goals

- Stand up a security-baselined Azure environment with guardrails before workloads arrive.
- Enable Defender for Cloud and deny public storage with Azure Policy.
- Translate your AWS baseline skills into the Azure landing-zone pattern.

## 🧠 Skills you will learn

- Terraform for Azure (azurerm)
- Microsoft Defender for Cloud
- Azure Policy guardrails
- Log Analytics + central logging
- Microsoft Cloud Security Benchmark (MCSB)

## 🛠 What you build

Terraform that creates a resource group, enables Microsoft Defender for Cloud, ships Activity Logs to Log Analytics, and assigns Azure Policy to deny public storage.

## 🏗 Architecture

```
Terraform (baseline)   →   Resource Group (+ Log Analytics)   →   Defender (for Cloud)   →   Azure Policy (deny public)
```
*Guardrails before workloads arrive.*

## ✅ Prerequisites

- Azure free account
- Azure CLI (`az`) + Terraform
- A $0 budget alert set

## ⚙️ Setup

```bash
az login
az account set --subscription "<SUB_ID>"
mkdir azure-landing-zone && cd $_ && terraform init
```

Defender Standard tiers cost money - keep them on only while testing, then `terraform destroy`.

## 🔨 Build it, step by step

### Step 1, Authenticate and scaffold

```bash
az login
az account set --subscription "<SUB_ID>"
mkdir azure-landing-zone && cd $_ && terraform init
```

```bash
# providers.tf
terraform { required_providers { azurerm = { source="hashicorp/azurerm", version="~>3.0" } } }
provider "azurerm" { features {} }
```

### Step 2, Create the resource group + Log Analytics

```bash
resource "azurerm_resource_group" "rg" { name="sec-baseline-rg"; location="eastus" }
resource "azurerm_log_analytics_workspace" "law" {
  name="sec-law"; resource_group_name=azurerm_resource_group.rg.name
  location=azurerm_resource_group.rg.location; sku="PerGB2018"; retention_in_days=30
}
```

### Step 3, Enable Defender for Cloud

```bash
resource "azurerm_security_center_subscription_pricing" "vm" { tier="Standard"; resource_type="VirtualMachines" }
resource "azurerm_security_center_subscription_pricing" "storage" { tier="Standard"; resource_type="StorageAccounts" }
```

### Step 4, Assign an Azure Policy guardrail

Deny storage accounts that allow public blob access - a guardrail, not just an alert.

```bash
resource "azurerm_subscription_policy_assignment" "deny_public" {
  name="deny-public-blob"
  subscription_id="/subscriptions/<SUB_ID>"
  policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/4fa4b6c0-31ca-4c0d-b10d-24b96f62a751"
}
```

### Step 5, Apply and verify

```bash
terraform apply
az security pricing show -n StorageAccounts --query pricingTier
az policy assignment list --query "[].displayName"
```

Run `terraform destroy` when done. Defender Standard tiers incur cost - keep them on only while testing.

## 🧪 Done when

- [ ] Defender for Cloud shows Standard tier on VMs + Storage
- [ ] The deny-public-blob policy is assigned
- [ ] Creating a public storage account is denied

## 🚀 Stretch goals

- Add a Sentinel workspace
- Assign the full MCSB policy initiative
- Add a deny-rule for public IPs on NSGs

## 📦 Make it portfolio-ready

> Diagram the landing zone (RG -> Defender + Log Analytics + Policy). Map controls to the Microsoft Cloud Security Benchmark (MCSB).

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*