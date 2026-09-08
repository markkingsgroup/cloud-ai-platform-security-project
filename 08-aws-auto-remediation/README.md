# AWS Serverless Auto-Remediation Bot

![](https://img.shields.io/badge/AWS-FF9900?style=flat-square) ![](https://img.shields.io/badge/Lambda-334155?style=flat-square) ![](https://img.shields.io/badge/EventBridge-334155?style=flat-square) ![](https://img.shields.io/badge/Terraform-334155?style=flat-square)

`Week 8` · Difficulty ★★★★☆ · ~8-10 hrs · *Zero to Job-Ready* portfolio project No. 8 of 36

> Event-driven Lambdas that auto-revert public S3 and world-open security groups.

## 🌍 The real-world problem

Detection without response leaves an exposure window. When someone opens a bucket or an SG to the world, every minute matters.

## 🎯 Project goals

- Close the gap between detection and response with event-driven auto-remediation.
- Auto-revert public S3 and world-open security groups within seconds.
- Demonstrate proactive, automated security engineering, not just alerting.

## 🧠 Skills you will learn

- AWS Lambda + EventBridge (event-driven architecture)
- boto3 remediation actions
- Terraform for serverless infrastructure
- Loop-prevention and tight IAM scoping
- Audit logging of automated actions

## 🛠 What you build

EventBridge rules trigger Lambdas to re-apply S3 Block Public Access and revoke 0.0.0.0/0 ingress, logging each action.

## 🏗 Architecture

```
Risky API call (PutBucketAcl)   →   EventBridge (match)   →   Lambda (remediate)   →   Reverted (+ logged)
```
*Detection plus automatic response.*

## ✅ Prerequisites

- Lambda + IAM comfort
- Terraform
- Projects 3 + 5

## ⚙️ Setup

```bash
aws configure
mkdir auto-remediation && cd $_ && terraform init
```

Scope the EventBridge pattern + Lambda role tightly to avoid remediation loops.

## 🔨 Build it, step by step

### Step 1, Catch the dangerous event

```bash
event_pattern = jsonencode({ source=["aws.s3","aws.ec2"],
  detail={ eventName=["PutBucketAcl","PutBucketPolicy","AuthorizeSecurityGroupIngress"] }})
```

### Step 2, Remediate public S3

```bash
import boto3; s3=boto3.client("s3")
def fix(b): s3.put_public_access_block(Bucket=b,PublicAccessBlockConfiguration={"BlockPublicAcls":True,"IgnorePublicAcls":True,"BlockPublicPolicy":True,"RestrictPublicBuckets":True})
```

### Step 3, Guard against loops + log

Scope the EventBridge pattern and Lambda role tightly so the bot cannot remediate its own actions into a loop.

```bash
# write every remediation to DynamoDB with who/what/when
```

## 🧪 Done when

- [ ] A public bucket is reverted in seconds
- [ ] A 0.0.0.0/0 rule is revoked
- [ ] Every action is logged with the principal

## 🚀 Stretch goals

- Slack approve/deny for borderline cases
- Extend to unencrypted EBS
- Dry-run mode

## 📦 Make it portfolio-ready

> Include the diagram and a demo GIF; note the loop-prevention design.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*