# Zero to Job-Ready, Cloud & AI Platform Security Engineer

![projects](https://img.shields.io/badge/projects-36_+_capstone-D4AF37?style=flat-square) ![clouds](https://img.shields.io/badge/AWS_·_Azure_·_GCP-multi--cloud-4285F4?style=flat-square) ![focus](https://img.shields.io/badge/DevSecOps_+_AI_Security-0a0a0a?style=flat-square)

A portfolio of **36 hands-on security projects plus a multi-cloud AI capstone**, built across a 20-week, depth-first program. Each project solves a real-world security problem and ships with an architecture diagram, step-by-step build, a verification checklist, and a "what I learned" writeup.

> **The README standard (applied to every project):** one-line purpose · architecture · controls mapped to CIS / OWASP / NIST · a STRIDE threat-model table · a 3-command quick start · a 2-5 min demo · "what I learned."

## Projects by track

### Foundations

| # | Project | Week | Difficulty | What it proves |
|---|---|---|---|---|
| 1 | [Linux Server Hardening Lab](01-linux-hardening-lab/) | Week 4 | ★★☆☆☆ | Every cloud VM is a Linux box. Proving you can harden one to a benchmark is day-one work. |
| 2 | [Phishing URL & Email Analyzer](02-phishing-analyzer/) | Week 3 | ★★☆☆☆ | SOC analysts triage phishing all day. Automating the first pass is immediately relatable work. |

### AWS

| # | Project | Week | Difficulty | What it proves |
|---|---|---|---|---|
| 3 | [AWS Secure Account Baseline (Terraform)](03-aws-baseline-terraform/) | Week 8 | ★★★☆☆ | 80% of cloud breaches are misconfigurations. This proves you prevent them - and automate it. |
| 4 | [AWS IaC Security Scanner](04-aws-iac-scanner/) | Week 6 | ★★★☆☆ | "I scan every IaC change in CI" ends interviews positively. This backs it up. |
| 5 | [AWS CloudTrail Log Analyzer](05-aws-cloudtrail-analyzer/) | Week 7 | ★★★☆☆ | SOC analysts do exactly this triage for hours. Scripting it is hire-worthy. |
| 6 | [AWS S3 Public-Exposure Auditor](06-aws-s3-auditor/) | Week 6 | ★★★☆☆ | Public buckets cause headline breaches. Finding them across an account is first-week cloud-sec work. |
| 7 | [AWS IAM Least-Privilege Auditor](07-aws-iam-auditor/) | Week 8 | ★★★☆☆ | Privilege creep turns one phished user into full compromise. Auditing it is core work. |
| 8 | [AWS Serverless Auto-Remediation Bot](08-aws-auto-remediation/) | Week 8 | ★★★★☆ | Auto-remediation separates reactive from proactive security - real platform-engineering output. |

### Azure

| # | Project | Week | Difficulty | What it proves |
|---|---|---|---|---|
| 9 | [Azure Secure Landing Zone](09-azure-landing-zone/) | Week 13 | ★★★☆☆ | Azure is half the enterprise market. A landing zone with policy guardrails is exactly what cloud teams build. |
| 10 | [Entra ID Access Auditor](10-azure-entra-auditor/) | Week 13 | ★★★☆☆ | Identity is the Azure perimeter. Auditing Entra ID (formerly Azure AD) is a core Azure security skill. |
| 11 | [Defender for Cloud Secure-Score Hardener](11-azure-defender-hardener/) | Week 13 | ★★★☆☆ | Secure Score is how Azure teams measure posture. Driving it up with documented fixes is exactly the job. |
| 12 | [Azure Storage Public-Exposure Auditor](12-azure-storage-auditor/) | Week 14 | ★★★☆☆ | The Azure equivalent of the S3 leak - public blob containers are a frequent breach source. |
| 13 | [Azure Monitor to Sentinel Detection Lab](13-azure-sentinel-detection/) | Week 14 | ★★★★☆ | Sentinel is the leading cloud SIEM. Writing KQL detections is a sought-after blue-team skill. |

### GCP

| # | Project | Week | Difficulty | What it proves |
|---|---|---|---|---|
| 14 | [GCP Organization Security Baseline](14-gcp-org-baseline/) | Week 15 | ★★★☆☆ | GCP secures at the organization/folder level. Knowing org policies + log sinks is distinctive GCP skill. |
| 15 | [GCP IAM Least-Privilege Auditor](15-gcp-iam-auditor/) | Week 15 | ★★★☆☆ | GCP IAM has unique footguns (primitive roles, SA keys). Auditing them shows real GCP depth. |
| 16 | [GCP Security Command Center Hardener](16-gcp-scc-hardener/) | Week 15 | ★★★☆☆ | SCC is GCP's native CSPM. Driving findings to zero with documented fixes is the GCP analyst job. |
| 17 | [GCS Bucket Public-Exposure Auditor](17-gcp-gcs-auditor/) | Week 15 | ★★☆☆☆ | The GCP equivalent of the S3/Blob leak - public GCS buckets are a common breach vector. |
| 18 | [GCP Cloud Audit Logs Threat Detector](18-gcp-audit-detector/) | Week 16 | ★★★☆☆ | GCP detection means BigQuery + Audit Logs. Writing SQL detections is the GCP blue-team skill. |

### Windows / AD

| # | Project | Week | Difficulty | What it proves |
|---|---|---|---|---|
| 19 | [Windows Server 2019/2022 Security Hardening](19-win-server-hardening/) | Week 17 | ★★★☆☆ | Windows runs most of the enterprise. Hardening a server to a benchmark is bread-and-butter blue-team work. |
| 20 | [Windows 10/11 Client Hardening & Endpoint Security](20-win-client-hardening/) | Week 17 | ★★★☆☆ | Endpoints are where ransomware and phishing land first. Hardening the Windows 10/11 client fleet is core endpoint-security work. |
| 21 | [Active Directory Security Audit & Hardening](21-ad-security-audit/) | Week 18 | ★★★★☆ | Active Directory is the #1 enterprise target. Finding and closing AD attack paths is a high-demand skill. |
| 22 | [Group Policy Baselines + Windows LAPS](22-gpo-security-baseline/) | Week 18 | ★★★☆☆ | Group Policy is how Windows fleets are configured at scale. Baselines + LAPS kill two of the most-abused attack paths. |
| 23 | [AD Attack & Detection Lab](23-ad-attack-detection/) | Week 18 | ★★★★☆ | Purple-team AD work - running the attack AND building the detection - is exactly what detection engineers do. |
| 24 | [Sysmon + Windows Event Threat Hunting](24-windows-sysmon-hunting/) | Week 18 | ★★★☆☆ | Windows host telemetry is where most enterprise attacks are caught. Threat hunting it is a core SOC skill. |

### DevOps / DevSecOps

| # | Project | Week | Difficulty | What it proves |
|---|---|---|---|---|
| 29 | [Keyless CI/CD Deploy Pipeline (GitHub Actions + OIDC)](29-oidc-cicd-pipeline/) | Week 5 | ★★★☆☆ | "How does your pipeline authenticate to the cloud?" - "Short-lived OIDC, no stored keys" ends the question well. |
| 30 | [Dynamic Secrets with HashiCorp Vault](30-vault-dynamic-secrets/) | Week 9 | ★★★★☆ | Dynamic secrets are the senior answer to "how do you manage credentials?" - there is no static secret to leak. |
| 31 | [Kubernetes Policy-as-Code (OPA Gatekeeper)](31-k8s-opa-gatekeeper/) | Week 12 | ★★★★☆ | Admission control with policy-as-code is exactly how platform teams keep a cluster compliant at scale. |
| 32 | [Software Supply-Chain Security (SBOM + Signing)](32-supply-chain-security/) | Week 9 | ★★★★☆ | Post-SolarWinds, supply-chain integrity (SBOM + signing) is a board-level ask - and a rare skill at the junior level. |
| 33 | [Terraform Drift Detection & Auto-Remediation](33-terraform-drift-detection/) | Week 9 | ★★★☆☆ | Config drift silently re-opens the holes IaC closed. Catching drift on a schedule is real platform-reliability work. |

### Multi-Cloud

| # | Project | Week | Difficulty | What it proves |
|---|---|---|---|---|
| 25 | [Multi-Cloud CSPM Dashboard](25-multicloud-cspm/) | Week 16 | ★★★★☆ | Real enterprises are multi-cloud. A single posture dashboard across all three is genuinely senior work. |
| 26 | [Multi-Cloud IaC Security Pipeline](26-multicloud-iac-pipeline/) | Week 16 | ★★★☆☆ | Teams write Terraform for all three clouds. One pipeline that secures all of it is exactly what they need. |
| 27 | [Container & Kubernetes Hardening](27-container-k8s-hardening/) | Week 12 | ★★★★☆ | Containers and K8s run everywhere across clouds. Hardening them is core platform-security work. |
| 28 | [DevSecOps CI/CD Security Pipeline](28-devsecops-pipeline/) | Week 12 | ★★★☆☆ | "Show me your CI/CD security" is a real interview question. This is a working answer. |

### AI Security

| # | Project | Week | Difficulty | What it proves |
|---|---|---|---|---|
| 34 | [LLM Prompt Injection Playground](34-llm-prompt-injection/) | Week 10 | ★★★☆☆ | Offensive AND defensive thinking with a live attack/fix demo - rare at the junior level. |
| 35 | [MCP Security Lab](35-mcp-security-lab/) | Week 11 | ★★★☆☆ | MCP is bleeding-edge. Accessible content on it makes you a thought leader. |
| 36 | [LLM Guardrail Proxy & PII Redactor](36-llm-guardrail-proxy/) | Week 11 | ★★★☆☆ | Every company adding AI needs this control layer. It shows you treat AI as an attack surface. |

### 🏆 Capstone

| Project | Weeks | What it proves |
|---|---|---|
| [Multi-Cloud AI Security Command Center](37-capstone-multicloud-ai/) | Weeks 19-20 | This is what commercial CNAPP/Security-Copilot products do. Building an open-source version across three clouds at once is a portfolio-defining capstone. |

## Skills demonstrated across the portfolio

`Linux server administration and the command line` · `SSH, firewall (ufw), fail2ban and auditd hardening` · `Idempotent Bash automation` · `Mapping controls to the CIS Benchmark` · `Evidence-based verification (audit scripting)` · `Python parsing and scoring logic` · `Regular expressions and lexical URL analysis` · `Email header + SPF/DKIM/DMARC authentication analysis` · `Phishing tradecraft and indicators of compromise` · `Safe handling of malicious artifacts` · `Terraform / Infrastructure as Code` · `AWS security services (CloudTrail, GuardDuty, Config, IAM)` · `IaC scanning with Checkov` · `CIS AWS Benchmark mapping` · `Repeatable, reviewable cloud provisioning` · `Python (JSON parsing, reporting)` · `Static analysis of IaC with Checkov` · `CI/CD gating with GitHub Actions` · `Severity triage and HTML reporting` · `Shift-left security practice` · `Python with boto3` · `AWS CloudTrail and API-event analysis` · `Detection logic and anomaly flagging` · `Log triage at scale` · `Read-only, least-privilege tooling` · `S3 security (Block Public Access, policies, encryption)` · `Read-only auditing and grading` · `Account-wide resource enumeration` · `Cloud data-exposure detection` · `AWS IAM model, policies and credential reports` · `Least-privilege and privilege-creep analysis` · `Detecting wildcard (Action:* Resource:*) grants` · `Risk ranking and remediation guidance` · `AWS Lambda + EventBridge (event-driven architecture)` · `boto3 remediation actions` · `Terraform for serverless infrastructure` · `Loop-prevention and tight IAM scoping` · `Audit logging of automated actions` · `Terraform for Azure (azurerm)` · `Microsoft Defender for Cloud`

## Certifications earned on this path

- **AWS Certified Cloud Practitioner** (CLF-C02), Week 8
- **CompTIA Security+** (SY0-701), Week 20

---
*Built by following the Zero to Job-Ready program · MarkKings Group LLC*