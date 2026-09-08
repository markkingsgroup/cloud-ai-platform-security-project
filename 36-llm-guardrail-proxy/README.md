# LLM Guardrail Proxy & PII Redactor

![](https://img.shields.io/badge/AI_Security-D97757?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/FastAPI-334155?style=flat-square) ![](https://img.shields.io/badge/Regex-334155?style=flat-square)

`Week 11` · Difficulty ★★★☆☆ · ~7-9 hrs · *Zero to Job-Ready* portfolio project No. 36 of 36

> A proxy in front of any LLM that redacts PII, blocks injection, and caps cost.

## 🌍 The real-world problem

When employees paste data into LLMs, secrets and PII leak to third parties. Companies need a gateway that scrubs sensitive data in and validates responses out.

## 🎯 Project goals

- Build a proxy in front of any LLM that redacts PII, blocks injection and caps cost.
- Scrub sensitive data in both directions, request and response.
- Ship the control layer every company adding AI actually needs.

## 🧠 Skills you will learn

- FastAPI guardrail proxy design
- PII / secret redaction (regex, Presidio)
- Injection screening + output validation
- Cost capping and audit logging
- Treating the LLM as an untrusted boundary

## 🛠 What you build

A FastAPI proxy with PII redaction (in + out), injection screening, and a token/cost budget.

## 🏗 Architecture

```
User input (+ PII)   →   Guardrail Proxy (redact / screen)   →   LLM API (third party)   →   Output (redacted)
```
*A control layer for any LLM.*

## ✅ Prerequisites

- Python + FastAPI
- An LLM API key (env var)

## ⚙️ Setup

```bash
python3 -m venv venv && source venv/bin/activate
pip install fastapi uvicorn anthropic
export ANTHROPIC_API_KEY="sk-ant-..."
```

## 🔨 Build it, step by step

### Step 1, Build the PII redactor

```bash
import re
PII={"EMAIL":r"[\w.+-]+@[\w-]+\.[\w.-]+","SSN":r"\b\d{3}-\d{2}-\d{4}\b","CARD":r"\b(?:\d[ -]*?){13,16}\b","AWSKEY":r"AKIA[0-9A-Z]{16}"}
def redact(t):
    found=[]
    for k,p in PII.items():
        t,n=re.subn(p,f"[{k}_REDACTED]",t)
        if n: found.append((k,n))
    return t,found
```

### Step 2, Screen injection + budget

```bash
INJ=["ignore previous","disregard","you are now","exfiltrate"]
def risky(t): return [p for p in INJ if p in t.lower()]
```

### Step 3, Wire the proxy

```bash
from fastapi import FastAPI,HTTPException; from pydantic import BaseModel; import anthropic
app=FastAPI(); client=anthropic.Anthropic()
class Q(BaseModel): text:str
@app.post("/ask")
def ask(q:Q):
    if risky(q.text): raise HTTPException(400,"blocked: injection")
    clean,found=redact(q.text)
    r=client.messages.create(model="claude-haiku-4-5-20251001",max_tokens=300,messages=[{"role":"user","content":clean}])
    out,_=redact(r.content[0].text)
    return {"reply":out,"redacted_in":found}
```

## 🧪 Done when

- [ ] Emails, SSNs, cards, AWS keys are redacted in + out
- [ ] Injection patterns return 400
- [ ] Cost cap halts calls past budget

## 🚀 Stretch goals

- Swap regex for Microsoft Presidio
- Audit-log every redaction
- Make it a drop-in base-URL proxy

## 📦 Make it portfolio-ready

> Show a redacted request and the trust boundary (employee -> proxy -> third-party LLM).

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*