# Linux Server Hardening Lab

![](https://img.shields.io/badge/Foundations-888888?style=flat-square) ![](https://img.shields.io/badge/Bash-334155?style=flat-square) ![](https://img.shields.io/badge/Ubuntu-334155?style=flat-square) ![](https://img.shields.io/badge/CIS-334155?style=flat-square)

`Week 4` · Difficulty ★★☆☆☆ · ~6-8 hrs · *Zero to Job-Ready* portfolio project No. 1 of 36

> Automated, CIS-aligned hardening of a fresh Ubuntu server with before/after evidence.

## 🌍 The real-world problem

Default Linux servers ship wide open: root SSH, no firewall, weak passwords, no audit logging. Most cloud servers are compromised through exactly these defaults.

## 🎯 Project goals

- Take a default, wide-open Ubuntu server and harden it to a CIS-style benchmark, automated and repeatable.
- Prove the improvement with a before/after audit instead of just claiming it.
- Produce a portfolio repo showing you can follow a security standard and document the evidence.

## 🧠 Skills you will learn

- Linux server administration and the command line
- SSH, firewall (ufw), fail2ban and auditd hardening
- Idempotent Bash automation
- Mapping controls to the CIS Benchmark
- Evidence-based verification (audit scripting)

## 🛠 What you build

A repo with an idempotent `harden.sh`, an `audit.py` verifier, a CIS-mapped checklist, and before/after screenshots.

## 🏗 Architecture

```
Fresh Ubuntu (insecure)   →   harden.sh (apply controls)   →   audit.py (verify)   →   Evidence (CIS map)
```
*Harden, then prove it.*

## ✅ Prerequisites

- Ubuntu 24.04 VM
- Basic Linux CLI
- GitHub account

## ⚙️ Setup

Use a throwaway Ubuntu VM you can break and rebuild.

```bash
sudo apt update && sudo apt install -y git ufw fail2ban auditd unattended-upgrades
mkdir -p ~/projects/linux-hardening-lab/{scripts,screenshots} && cd $_ && git init
```

Snapshot the VM ("clean-install") so you can roll back for a clean demo.

## 🔨 Build it, step by step

### Step 1, Snapshot the insecure baseline

Capture the weak defaults first so you can prove the improvement.

```bash
sshd -T | grep -E "permitrootlogin|passwordauthentication"
sudo ufw status
systemctl is-active fail2ban auditd 2>/dev/null
```

### Step 2, Write the hardening script

```bash
#!/bin/bash
set -euo pipefail
sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication no/' /etc/ssh/sshd_config
systemctl restart ssh
ufw default deny incoming; ufw default allow outgoing; ufw allow 22/tcp; ufw --force enable
```

Keep a second SSH session open while testing key-only login - locking yourself out is the classic mistake.

### Step 3, Add brute-force, audit and auto-patch controls

```bash
apt-get install -y fail2ban auditd unattended-upgrades
printf "[sshd]\nenabled=true\nmaxretry=5\nbantime=3600\n" > /etc/fail2ban/jail.local
echo "-w /etc/sudoers -p wa -k actions" >> /etc/audit/rules.d/hardening.rules
systemctl enable --now fail2ban auditd
```

### Step 4, Write the audit verifier and run it

```bash
import subprocess,re,sys
def check(d,c,p):
    out=subprocess.run(c,shell=True,capture_output=True,text=True).stdout
    ok=bool(re.search(p,out,re.I)); print(("PASS" if ok else "FAIL"),"|",d); return ok
r=[check("SSH root off","sshd -T|grep permitrootlogin","permitrootlogin no"),
   check("ufw active","ufw status","Status: active")]
sys.exit(0 if all(r) else 1)
```

## 🧪 Done when

- [ ] `audit.py` exits 0, all PASS
- [ ] SSH refuses password login
- [ ] ufw active with only 22/tcp

## 🚀 Stretch goals

- Run Lynis and report your hardening index
- Convert to an Ansible playbook
- Add a Vagrantfile

## 📦 Make it portfolio-ready

> Lead with an attack-surface before/after diagram, the CIS mapping table, and a screenshot of audit.py all-green.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*