# OSDO Integration Guide

> **Which OSDO component should I use?** This guide helps you choose the right entry point.

---

## 🎯 Decision Tree

```
Do you have existing CI/CD?
│
├─ NO → Use **osdo-workflow-template** (Framework)
│       → Full pipeline, secure defaults, minimal config
│
└─ YES
    │
    ├─ Want pre-built workflows?
    │   └─ YES → Use **osdo-workflows**
    │            → security-only.yml, compliance-only.yml
    │
    └─ Need granular control?
        └─ YES → Use **osdo-actions**
                 → Mix and match individual actions
```

---

## 📦 The Three Pillars

| Repository | Purpose | When to Use |
|------------|---------|-------------|
| **osdo-workflow-template** | Framework | New projects, quick setup |
| **osdo-workflows** | Reusable workflows | Add security to existing CI |
| **osdo-actions** | Granular actions | Maximum customization |

---

## 1. osdo-workflow-template (Framework)

**Best for:** New projects, teams wanting secure defaults

```yaml
# .github/workflows/osdo.yml
jobs:
  secure:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
```

**Included:** SAST, SCA, Secrets, SBOM, Quality Gates

---

## 2. osdo-workflows

**Best for:** Adding specific capabilities to existing pipelines

```yaml
jobs:
  security:
    uses: opensecdevops/osdo-workflows/.github/workflows/security-only.yml@main
  
  compliance:
    uses: opensecdevops/osdo-workflows/.github/workflows/compliance-only.yml@main
```

**Available:** security-only, compliance-only, quality-gates

---

## 3. osdo-actions

**Best for:** Custom pipelines, enterprise governance

```yaml
steps:
  - uses: opensecdevops/osdo-actions/osdo-sast@v1
  - uses: opensecdevops/osdo-actions/osdo-sca@v1
  - uses: opensecdevops/osdo-actions/osdo-mobile-scan@v1
  - uses: opensecdevops/osdo-actions/osdo-smart-contract-audit@v1
  - uses: opensecdevops/osdo-actions/osdo-llm-scan@v1
```

---

## 🔧 OSDO CLI

The fastest way to get started:

```bash
# Install
brew install opensecdevops/tap/osdo

# Generate pipeline
osdo pipeline generate --template security

# Available templates
osdo pipeline generate --template mobile   # Mobile apps
osdo pipeline generate --template web3     # Smart contracts
osdo pipeline generate --template genai    # LLM/AI apps
```

---

## 📊 Security Domains

| Domain | Action | Coverage |
|--------|--------|----------|
| Web | osdo-sast, osdo-sca | OWASP Top 10 |
| Mobile | osdo-mobile-scan | OWASP Mobile Top 10 |
| Web3 | osdo-smart-contract-audit | OWASP SC Top 10 |
| GenAI | osdo-llm-scan | OWASP GenAI Top 10 |

---

## 🔗 Links

- [osdo-workflow-template](https://github.com/opensecdevops/osdo-workflow-template)
- [osdo-workflows](https://github.com/opensecdevops/osdo-workflows)
- [osdo-actions](https://github.com/opensecdevops/osdo-actions)
- [OSDO CLI](https://github.com/opensecdevops/osdo-infra-cli)
