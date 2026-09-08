# Entra ID Access Auditor

![](https://img.shields.io/badge/Azure-0089D6?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/Microsoft_Graph-334155?style=flat-square) ![](https://img.shields.io/badge/Entra_ID-334155?style=flat-square)

`Week 13` · Difficulty ★★★☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 10 of 36

> Audits Entra ID for users without MFA, stale apps, and risky privileged roles.

## 🌍 The real-world problem

Entra ID accumulates risk: accounts without MFA, guest users with broad access, stale app registrations with secrets, and too many Global Admins.

## 🎯 Project goals

- Audit Entra ID for users without MFA, stale app secrets and risky privileged roles.
- Find the identity weaknesses attackers use to get into Azure.
- Recommend PIM and just-in-time access to shrink standing privilege.

## 🧠 Skills you will learn

- Python with the Microsoft Graph API
- Entra ID (users, app registrations, service principals)
- Identity security auditing
- PIM and Conditional Access concepts
- OAuth app-registration token flows

## 🛠 What you build

A Microsoft Graph script reporting MFA gaps, privileged-role members, and expiring app credentials.

## 🏗 Architecture

```
Entra ID (tenant)   →   MS Graph (read)   →   Auditor (MFA / roles / secrets)   →   Report (identity risk)
```
*Identity is the Azure perimeter.*

## ✅ Prerequisites

- Entra ID tenant (free)
- An app registration with Graph read scopes
- `pip install msal requests`

## ⚙️ Setup

Register an app in Entra ID and grant it Graph read scopes (User.Read.All, Directory.Read.All, RoleManagement.Read.Directory).

```bash
pip install msal requests
# create a client secret on the app registration and store it in an env var
```

## 🔨 Build it, step by step

### Step 1, Register an app + get a token

```bash
# Grant the app: User.Read.All, Directory.Read.All, RoleManagement.Read.Directory
import msal
app=msal.ConfidentialClientApplication("<APP_ID>",authority="https://login.microsoftonline.com/<TENANT>",client_credential="<SECRET>")
tok=app.acquire_token_for_client(["https://graph.microsoft.com/.default"])["access_token"]
```

### Step 2, List privileged role members

```bash
import requests
H={"Authorization":"Bearer "+tok}
G="https://graph.microsoft.com/v1.0"
roles=requests.get(G+"/directoryRoles",headers=H).json()["value"]
for r in roles:
    if "Admin" in r["displayName"]:
        m=requests.get(G+f"/directoryRoles/{r['id']}/members",headers=H).json()["value"]
        print(r["displayName"],"->",[u.get("userPrincipalName") for u in m])
```

### Step 3, Flag stale app secrets

```bash
apps=requests.get(G+"/applications?$select=displayName,passwordCredentials",headers=H).json()["value"]
from datetime import datetime,timezone
for a in apps:
    for c in a.get("passwordCredentials",[]):
        exp=c.get("endDateTime")
        if exp and exp<datetime.now(timezone.utc).isoformat(): print("EXPIRED SECRET:",a["displayName"])
```

### Step 4, Report MFA registration gaps

```bash
# /reports/authenticationMethods/userRegistrationDetails (needs AuditLog.Read.All)
reg=requests.get(G+"/reports/authenticationMethods/userRegistrationDetails",headers=H).json()["value"]
for u in reg:
    if not u.get("isMfaRegistered"): print("NO MFA:",u.get("userPrincipalName"))
```

## 🧪 Done when

- [ ] Global Admin members are listed
- [ ] An expired app secret is flagged
- [ ] Users without MFA are reported

## 🚀 Stretch goals

- Flag guest users with privileged roles
- Check for Conditional Access gaps
- Schedule via an Azure Function

## 📦 Make it portfolio-ready

> Explain why too many Global Admins is dangerous; recommend PIM (Privileged Identity Management) for just-in-time roles.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*