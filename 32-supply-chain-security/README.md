# Software Supply-Chain Security (SBOM + Signing)

![](https://img.shields.io/badge/DevOps_/_DevSecOps-5FB878?style=flat-square) ![](https://img.shields.io/badge/Syft-334155?style=flat-square) ![](https://img.shields.io/badge/Cosign-334155?style=flat-square) ![](https://img.shields.io/badge/SLSA-334155?style=flat-square) ![](https://img.shields.io/badge/GitHub_Actions-334155?style=flat-square)

`Week 9` · Difficulty ★★★★☆ · ~8-10 hrs · *Zero to Job-Ready* portfolio project No. 32 of 36

> Generate an SBOM, sign your container image, and verify provenance so deploys only run trusted artifacts.

## 🌍 The real-world problem

You can ship a perfectly scanned image and still get burned if an attacker swaps it or slips in a dependency. An SBOM makes the contents auditable; signing proves the artifact is yours and untampered.

## 🎯 Project goals

- Generate an SBOM, sign your container image and verify provenance.
- Make deploys run only trusted, untampered artifacts.
- Build the board-level skill that emerged after SolarWinds.

## 🧠 Skills you will learn

- SBOM generation with Syft
- Keyless image signing with Cosign / Sigstore
- SLSA provenance and attestation
- Deploy-time signature verification
- Supply-chain integrity

## 🛠 What you build

A CI pipeline that builds an image, generates an SBOM with Syft, signs the image keylessly with Cosign (via OIDC), and a deploy step that verifies the signature before anything runs.

## 🏗 Architecture

```
Build (image)   →   Syft (SBOM)   →   Cosign (keyless sign)   →   Verify (provenance)   →   Deploy (trusted only)
```
*Only signed, attested artifacts reach production.*

## ✅ Prerequisites

- Docker
- GitHub Actions
- Project 29 (OIDC) helps for keyless signing

## ⚙️ Setup

```bash
# install Syft (SBOM)
curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin
# install Cosign (signing)
sudo curl -sSfL -o /usr/local/bin/cosign https://github.com/sigstore/cosign/releases/latest/download/cosign-linux-amd64 && sudo chmod +x /usr/local/bin/cosign
syft version && cosign version
```

## 🔨 Build it, step by step

### Step 1, Generate an SBOM with Syft

```bash
syft app:ci -o spdx-json=sbom.spdx.json
# the SBOM lists every package + version in the image - your auditable bill of materials
```

### Step 2, Sign the image keylessly with Cosign

```bash
# keyless: Cosign uses an OIDC identity + the public Sigstore log - no private key to manage
COSIGN_EXPERIMENTAL=1 cosign sign app:ci
```

### Step 3, Verify the signature before deploy

```bash
COSIGN_EXPERIMENTAL=1 cosign verify app:ci
# a tampered or unsigned image FAILS verification:
cosign verify someones-image:latest   # error: no matching signatures
```

### Step 4, Gate the deploy and attest the SBOM

```bash
# attach the SBOM as a signed attestation
cosign attest --predicate sbom.spdx.json --type spdxjson app:ci
# deploy step runs only if: cosign verify app:ci  (exit 0)
```

This is SLSA in practice: provenance you can verify, so production only runs artifacts you built.

## 🧪 Done when

- [ ] an SBOM lists the image contents
- [ ] `cosign verify` succeeds for your signed image
- [ ] an unsigned or tampered image fails verification
- [ ] the deploy step blocks when verification fails

## 🚀 Stretch goals

- Enforce signature verification at the cluster with a Gatekeeper policy (ties to Project 31)
- Look the signature up in the Rekor transparency log
- Fail the build if the SBOM contains a denylisted license or package

## 📦 Make it portfolio-ready

> Diagram build -> SBOM (Syft) -> sign (Cosign) -> verify -> deploy. Explain SLSA provenance and keyless signing.

## 📝 What I learned

_Fill this in after you build it, three or four honest sentences on what clicked and what was hard. This is the section hiring managers actually read._

---

*Part of [Zero to Job-Ready: Cloud & AI Platform Security Engineer](../README.md) · MarkKings Group LLC*