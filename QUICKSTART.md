# 🚀 OSDO Quickstart - Zero to Secure in 5 Minutes

> **Transform any project into a security-first DevOps pipeline in under 5 minutes.**

---

## ⚡ 30-Second Start

```yaml
# .github/workflows/osdo.yml
name: OSDO Security
on: [push, pull_request]

jobs:
  secure:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
```

**Done.** Push and you have:
✅ SAST (Semgrep, Bandit)  
✅ SCA (Grype, OSV)  
✅ Secrets Detection  
✅ Quality Gates  

---

## 🎯 Choose Your Path

| I need... | Use This |
|-----------|----------|
| **Everything, fast** | → [Full Framework](#full-framework) |
| **Just security scanning** | → [Security Only](#security-only) |
| **Mobile app security** | → [Mobile Scan](#mobile-scan) |
| **Smart contract audit** | → [Web3 Security](#web3-security) |
| **LLM/AI app security** | → [GenAI Scan](#genai-scan) |

---

## Full Framework

```yaml
jobs:
  osdo:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      node-version: '22'
      test-coverage-threshold: '80'
      enable-sast: true
      enable-sca: true
      enable-secrets: true
```

---

## Security Only

```yaml
jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: opensecdevops/osdo-actions/osdo-sast@v1
      - uses: opensecdevops/osdo-actions/osdo-sca@v1
      - uses: opensecdevops/osdo-actions/osdo-secrets-scan@v1
```

---

## Mobile Scan

```yaml
jobs:
  mobile:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: opensecdevops/osdo-actions/osdo-mobile-scan@v1
        with:
          app-path: "./app.apk"  # or .ipa
          platform: "android"    # or ios
          check-ssl-pinning: true
          check-obfuscation: true
```

**Covers:** OWASP Mobile Top 10

---

## Web3 Security

```yaml
jobs:
  contracts:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: opensecdevops/osdo-actions/osdo-smart-contract-audit@v1
        with:
          contracts-path: "./contracts"
          scanners: "slither,mythril"
          check-reentrancy: true
```

**Covers:** OWASP Smart Contract Top 10, Slither, Mythril

---

## GenAI Scan

```yaml
jobs:
  llm:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: opensecdevops/osdo-actions/osdo-llm-scan@v1
        with:
          source-path: "."
          check-prompt-injection: true
          check-pii-exposure: true
          check-model-serialization: true
```

**Covers:** OWASP GenAI Top 10, Prompt Injection, PII Leaks

---

## 📊 Coverage Matrix

| Standard | Coverage | Actions |
|----------|----------|---------|
| OWASP Web Top 10 | 90% | sast, sca, secrets, dast |
| OWASP Mobile Top 10 | 70% | mobile-scan |
| OWASP Smart Contract Top 10 | 60% | smart-contract-audit |
| OWASP GenAI Top 10 | 40% | llm-scan |
| SLSA Level 3 | 100% | slsa-provenance, sbom |
| OpenSSF Scorecard | 85% | all actions |

---

## 🔗 Next Steps

1. **[Full Documentation](https://docs.opensecdevops.org)**
2. **[Actions Reference](https://github.com/opensecdevops/osdo-actions)**
3. **[Compliance Checklists](./docs/CERTIFICATION_CHECKLISTS.md)**

---

<p align="center">
  <strong>Open SecDevOps</strong> • Secure by Default • 2025
</p>
