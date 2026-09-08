# Phishing URL & Email Analyzer

![](https://img.shields.io/badge/Foundations-888888?style=flat-square) ![](https://img.shields.io/badge/Python-334155?style=flat-square) ![](https://img.shields.io/badge/Regex-334155?style=flat-square) ![](https://img.shields.io/badge/APIs-334155?style=flat-square)

`Week 3` · Difficulty ★★☆☆☆ · ~5-7 hrs · *Zero to Job-Ready* portfolio project No. 2 of 36

> A Python tool that scores suspicious URLs and email headers for phishing indicators.

## 🌍 The real-world problem

Phishing is the #1 initial-access vector. Analysts manually inspect URLs and headers for lookalike domains, risky TLDs, SPF/DKIM failures - repetitive and perfect to automate.

## 🎯 Project goals

- Automate the first-pass triage a SOC analyst does on suspicious URLs and emails.
- Score real indicators, lookalike domains, risky TLDs, SPF/DKIM/DMARC failures, into a clear verdict.
- Ship a safe, defensive tool that inspects strings and metadata without ever visiting a link.

## 🧠 Skills you will learn

- Python parsing and scoring logic
- Regular expressions and lexical URL analysis
- Email header + SPF/DKIM/DMARC authentication analysis
- Phishing tradecraft and indicators of compromise
- Safe handling of malicious artifacts

## 🛠 What you build

A CLI that scores a URL or .eml file and lists the exact indicators it found.

## 🏗 Architecture

```
URL / .eml (input)   →   Parser (domain + headers)   →   Checks (lexical / SPF-DKIM)   →   Risk Score (LOW/MED/HIGH)
```
*Inspected as data, never visited.*

## ✅ Prerequisites

- Python basics
- `pip install requests tldextract`

## ⚙️ Setup

```bash
python3 -m venv venv && source venv/bin/activate
pip install requests tldextract
mkdir phishing-analyzer && cd $_ && git init
```

## 🔨 Build it, step by step

### Step 1, Parse the URL into parts

```bash
import tldextract
def parse(u):
    e=tldextract.extract(u)
    return {"sub":e.subdomain,"domain":e.domain,"tld":e.suffix,"full":u}
```

### Step 2, Score lexical red flags

```bash
RISKY={"zip","xyz","top","tk","gq"}; BRANDS=["paypal","microsoft","apple","bank","login"]
def score(u):
    s,why=0,[]
    if u["tld"] in RISKY: s+=2; why.append("risky TLD")
    if any(b in u["sub"] for b in BRANDS): s+=2; why.append("brand in subdomain")
    if u["full"].count("-")>=4: s+=1; why.append("many hyphens")
    return s,why
```

### Step 3, Parse email auth headers

```bash
import email
def hdr(path):
    m=email.message_from_file(open(path)); f=[]
    if m.get("Reply-To","").split("@")[-1]!=m.get("From","").split("@")[-1]: f.append("Reply-To mismatch")
    for x in ["spf=fail","dkim=fail","dmarc=fail"]:
        if x in m.get("Authentication-Results",""): f.append(x)
    return f
```

### Step 4, Assemble a verdict

```bash
def verdict(url):
    u=parse(url); s,w=score(u)
    lvl="HIGH" if s>=4 else "MED" if s>=2 else "LOW"
    print(f"Risk: {lvl} ({s})"); [print("  -",r) for r in w]
```

Never *visit* the URLs - inspect the string and metadata only.

## 🧪 Done when

- [ ] google.com scores LOW
- [ ] A lookalike .xyz scores HIGH with reasons
- [ ] An .eml with SPF fail is flagged

## 🚀 Stretch goals

- Add VirusTotal enrichment
- Batch CSV mode
- Tiny Flask UI

## 📦 Make it portfolio-ready

> Include 3 sample analyses (clean/medium/high) and a defensive-use disclaimer.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*