# Capacidades de Seguridad de OSDO

## Resumen de cobertura por dominio

| Dominio | Estándar | Cobertura | Acciones OSDO |
|---------|----------|-----------|---------------|
| Aplicaciones Web | OWASP Top 10 2021 | 90% | osdo-sast, osdo-sca, osdo-secrets-scan, osdo-dast-scan |
| Apps Móviles | OWASP Mobile Top 10 2024 | 70% | osdo-mobile-scan, osdo-sca |
| Smart Contracts | OWASP SC Top 10 2023 | 60% | osdo-smart-contract-audit |
| GenAI/LLM | OWASP GenAI Top 10 2025 | 50% | osdo-llm-scan, osdo-sast, osdo-sca |
| Cadena de suministro | SLSA Level 3 | 100% | osdo-slsa-provenance, osdo-sbom, osdo-sign |
| Scorecard OSS | OpenSSF Scorecard | 85% | osdo-sast, osdo-sca, osdo-secrets-scan, osdo-fuzz |

## OWASP Top 10 2021 — Cobertura detallada

| Control | Descripción | Acción OSDO | Cobertura |
|---------|-------------|-------------|-----------|
| A01 | Broken Access Control | osdo-sast (reglas OWASP) | ✅ Automatizado |
| A02 | Cryptographic Failures | osdo-sast (weak-crypto rules) | ✅ Automatizado |
| A03 | Injection | osdo-sast (injection patterns) | ✅ Automatizado |
| A04 | Insecure Design | osdo-sast + osdo-dast-scan | ⚠️ Parcial (requiere revisión manual) |
| A05 | Security Misconfiguration | osdo-iac-scan, osdo-container-scan | ✅ Automatizado |
| A06 | Vulnerable Components | osdo-sca (OSV-Scanner, Grype) | ✅ Automatizado |
| A07 | Auth & Session Failures | osdo-sast + osdo-dast-scan | ✅ Automatizado |
| A08 | Software & Data Integrity | osdo-slsa-provenance, osdo-sbom | ✅ Automatizado |
| A09 | Security Logging Failures | osdo-sast (logging rules) | ⚠️ Parcial |
| A10 | SSRF | osdo-sast + osdo-dast-scan | ✅ Automatizado |

## SLSA Level 3 — Cobertura completa

| Requisito SLSA L3 | Acción OSDO |
|-------------------|-------------|
| Provenance generada automáticamente | `osdo-slsa-provenance` |
| Firma con clave efímera (Sigstore/Cosign) | `osdo-sign` |
| Registro en Rekor transparency log | `osdo-slsa-provenance` |
| SBOM generado y adjunto | `osdo-sbom` |
| Build en entorno aislado | GitHub Actions runners |

## Herramientas de seguridad integradas

### SAST (Análisis Estático)
- **Semgrep** — Reglas OWASP Top 10 + CWE Top 25 + p/security-audit
- **Bandit** — Python security linting
- **Gosec** — Go security checker
- **SpotBugs + Find-Sec-Bugs** — Java OWASP patterns
- **PHPStan** — PHP level-8 static analysis
- **ESLint Security** — JavaScript/TypeScript

### SCA (Análisis de Composición)
- **OSV-Scanner** — Base de datos de vulnerabilidades de Google
- **Grype** — Anchorage vulnerability scanner
- **OWASP Dependency-Check** — Java/Maven/Gradle
- **pip-audit** — Python packages
- **govulncheck** — Go modules
- **npm audit** — Node.js packages

### Detección de Secretos
- **Gitleaks** — Reglas para AWS, Azure, GitHub + reglas personalizadas
- **TruffleHog** — Búsqueda de secretos históricos en git

### Seguridad de Contenedores
- **Trivy** — CVEs en imágenes, layers, y dependencias
- **Hadolint** — Dockerfile security best practices
- **Grype** — Alternative container scanner

### IaC Security
- **Checkov** — Terraform, Kubernetes, Helm, Docker
- **KICS** — Terraform, Ansible, CloudFormation
- **tfsec** — Terraform security analysis

### Cloud Security
- **Prowler** — AWS, Azure y GCP benchmarks CIS

### DAST
- **OWASP ZAP** — Análisis dinámico de aplicaciones web

### Supply Chain
- **Syft** — SBOM generation (SPDX 2.3 + CycloneDX 1.5)
- **Cosign** — Firma de imágenes y artefactos
- **Sigstore** — Keyless signing infrastructure
