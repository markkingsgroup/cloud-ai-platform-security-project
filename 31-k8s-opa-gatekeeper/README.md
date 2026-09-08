# Kubernetes Policy-as-Code (OPA Gatekeeper)

![](https://img.shields.io/badge/DevOps_/_DevSecOps-5FB878?style=flat-square) ![](https://img.shields.io/badge/Kubernetes-334155?style=flat-square) ![](https://img.shields.io/badge/OPA_Gatekeeper-334155?style=flat-square) ![](https://img.shields.io/badge/Rego-334155?style=flat-square)

`Week 12` · Difficulty ★★★★☆ · ~8-10 hrs · *Zero to Job-Ready* portfolio project No. 31 of 36

> Enforce security policy at the Kubernetes admission point - deny non-compliant pods before they ever run.

## 🌍 The real-world problem

Scanning a manifest catches problems late. Admission control stops a non-compliant pod - privileged, running as root, from an untrusted registry - from ever being created in the first place.

## 🎯 Project goals

- Enforce security policy at the Kubernetes admission point.
- Deny privileged, root and untrusted-registry pods before they ever run.
- Show how platform teams keep a cluster compliant at scale.

## 🧠 Skills you will learn

- Kubernetes admission control
- OPA Gatekeeper + Rego policy-as-code
- Pod Security and registry allow-listing
- Constraint templates and audit mode
- Policy testing with conftest

## 🛠 What you build

OPA Gatekeeper on a k3s cluster with constraint templates that deny privileged pods, enforce non-root, and require a trusted image registry - proven by a deploy that gets blocked.

## 🏗 Architecture

```
kubectl apply (pod spec)   →   Admission webhook (intercept)   →   Gatekeeper (Rego policy)   →   Decision (allow / deny)
```
*Non-compliant pods are rejected before they ever run.*

## ✅ Prerequisites

- A k3s/Kubernetes cluster (reuse Project 27)
- Helm
- kubectl

## ⚙️ Setup

Reuse the k3s cluster from Project 27.

```bash
helm repo add gatekeeper https://open-policy-agent.github.io/gatekeeper/charts && helm repo update
kubectl get nodes
```

## 🔨 Build it, step by step

### Step 1, Install Gatekeeper

```bash
helm repo add gatekeeper https://open-policy-agent.github.io/gatekeeper/charts
helm install gatekeeper gatekeeper/gatekeeper --namespace gatekeeper-system --create-namespace
kubectl get pods -n gatekeeper-system
```

### Step 2, Write a ConstraintTemplate in Rego

```bash
# block any privileged container
apiVersion: templates.gatekeeper.sh/v1
kind: ConstraintTemplate
metadata: { name: k8sdenyprivileged }
spec:
  crd: { spec: { names: { kind: K8sDenyPrivileged } } }
  targets:
    - target: admission.k8s.gatekeeper.sh
      rego: |
        package k8sdenyprivileged
        violation[{"msg": msg}] {
          c := input.review.object.spec.containers[_]
          c.securityContext.privileged == true
          msg := "privileged containers are not allowed"
        }
```

### Step 3, Apply a Constraint and watch it deny

```bash
kubectl apply -f - <<EOF
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sDenyPrivileged
metadata: { name: no-privileged }
spec: { match: { kinds: [{ apiGroups: [""], kinds: ["Pod"] }] } }
EOF
# now try a privileged pod -> admission webhook DENIES it
kubectl run bad --image=nginx --privileged=true   # Error: admission denied
```

### Step 4, Add non-root and trusted-registry policies

- Require runAsNonRoot=true on every container.
- Allow images only from your registry (e.g. starts with ghcr.io/your-org/).
- Run constraints in audit mode first to find existing violations, then enforce.

## 🧪 Done when

- [ ] a privileged pod is denied at admission
- [ ] a pod that runs as root is denied
- [ ] an image from an untrusted registry is denied
- [ ] a compliant pod is admitted normally

## 🚀 Stretch goals

- Run every policy in audit mode and report existing violations
- Add a require-resource-limits constraint
- Test the Rego in CI with conftest before it reaches the cluster

## 📦 Make it portfolio-ready

> Diagram kubectl apply -> admission webhook -> Gatekeeper (Rego) -> allow/deny. Explain policy-as-code vs scanning.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*