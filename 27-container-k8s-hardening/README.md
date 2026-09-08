# Container & Kubernetes Hardening

![](https://img.shields.io/badge/Multi--Cloud-7B42BC?style=flat-square) ![](https://img.shields.io/badge/Docker-334155?style=flat-square) ![](https://img.shields.io/badge/Trivy-334155?style=flat-square) ![](https://img.shields.io/badge/Kubernetes-334155?style=flat-square) ![](https://img.shields.io/badge/Falco-334155?style=flat-square)

`Week 12` · Difficulty ★★★★☆ · ~8-12 hrs · *Zero to Job-Ready* portfolio project No. 27 of 36

> Harden an image, scan it, then secure a k3s cluster with RBAC, policies, and runtime detection.

## 🌍 The real-world problem

Images ship with CVEs and run as root; clusters default to permissive RBAC and no runtime detection. Container escapes turn that into host/cluster compromise.

## 🎯 Project goals

- Harden a container image, scan it clean, then secure a k3s cluster.
- Apply RBAC, Pod Security, network policy and kube-bench.
- Catch a shell-in-container at runtime with Falco.

## 🧠 Skills you will learn

- Docker image hardening (multi-stage, non-root)
- Trivy image/CVE scanning
- Kubernetes security (RBAC, Pod Security, NetworkPolicy)
- kube-bench CIS benchmarking
- Falco runtime threat detection

## 🛠 What you build

A hardened multi-stage image (Trivy-clean), plus a k3s cluster with non-root pods, network policies, kube-bench passing, and Falco catching a shell-in-container.

## 🏗 Architecture

```
Image (hardened)   →   Trivy (CVE scan)   →   k3s (RBAC + policies)   →   Falco (runtime detect)
```
*Image and cluster, both hardened.*

## ✅ Prerequisites

- Docker + Trivy
- A VM for k3s
- Projects 1 helps

## ⚙️ Setup

```bash
sudo apt install -y docker.io
wget -qO- https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo gpg --dearmor -o /usr/share/keyrings/trivy.gpg
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | sudo tee /etc/apt/sources.list.d/trivy.list
sudo apt update && sudo apt install -y trivy
```

k3s needs a dedicated VM; install kube-bench + Helm there.

## 🔨 Build it, step by step

### Step 1, Harden + scan the image

```bash
# multi-stage, non-root, slim
FROM python:3.11-slim AS b
WORKDIR /app; COPY requirements.txt .; RUN pip install --no-cache-dir -r requirements.txt
FROM python:3.11-slim
RUN useradd -r appuser; USER appuser
COPY --from=b /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY app.py .; ENTRYPOINT ["python","app.py"]
```

```bash
trivy image app:hardened --severity HIGH,CRITICAL
```

### Step 2, Stand up k3s + benchmark it

```bash
curl -sfL https://get.k3s.io | sh -
kubectl get nodes
# CIS benchmark the cluster
kube-bench run --targets master,node
```

### Step 3, Apply Pod Security + Network Policy

```bash
# Enforce restricted Pod Security Standard on a namespace
kubectl label ns default pod-security.kubernetes.io/enforce=restricted
# Default-deny network policy
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata: { name: default-deny, namespace: default }
spec: { podSelector: {}, policyTypes: [Ingress, Egress] }
EOF
```

### Step 4, Add Falco runtime detection

```bash
helm repo add falcosecurity https://falcosecurity.github.io/charts && helm install falco falcosecurity/falco
# Trigger a detection:
kubectl run test --image=alpine -- sleep 1d
kubectl exec -it test -- sh    # Falco alerts: "shell spawned in container"
```

## 🧪 Done when

- [ ] Hardened image has 0 CRITICAL CVEs and runs non-root
- [ ] kube-bench passes the key controls
- [ ] A shell-in-container raises a Falco alert

## 🚀 Stretch goals

- Run Trivy Operator in-cluster
- Add OPA Gatekeeper policies
- Enable audit logging shipped to your SIEM

## 📦 Make it portfolio-ready

> Include the CVE before/after, the kube-bench summary, and the Falco alert screenshot.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*