# OSDO Starter — Template para Proyectos Seguros

> 🚀 **Forkea este repositorio** para iniciar un proyecto nuevo con DevSecOps completamente preconfigurado desde el día uno.

[![OSDO Security](https://img.shields.io/badge/powered%20by-OSDO-0066cc)](https://opensecdevops.com)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue)](LICENSE)
[![OpenSSF Scorecard](https://api.securityscorecards.dev/projects/github.com/opensecdevops/osdo-starter/badge)](https://securityscorecards.dev)

---

## ¿Qué es esto?

**osdo-starter** es un GitHub Template Repository que provee:

- ✅ Pipeline de seguridad completo preconfigurado (CI/CD + DevSecOps)
- ✅ Configuración con valores por defecto sensatos (`.osdo/config.yml`)
- ✅ Integración automática con las 22 OSDO Actions y 10 OSDO Workflows
- ✅ Quality gates, umbrales de severidad y reportes de cumplimiento listos para usar

---

## Quick Start (4 pasos)

### 1. Crear tu proyecto desde este template

```bash
# En GitHub: click "Use this template" → "Create a new repository"
# O con GitHub CLI:
gh repo create mi-proyecto --template opensecdevops/osdo-starter --private
cd mi-proyecto
```

### 2. Configura `.osdo/config.yml`

Ajusta los umbrales según las necesidades de tu proyecto:

```yaml
security:
  quality_gate:
    security_score: 70     # Score mínimo OSDO (0-100)
    coverage: 80           # Cobertura de tests mínima (%)
    critical_vulns: 0      # Vulnerabilidades críticas permitidas
    high_vulns: 5          # Vulnerabilidades altas permitidas
```

### 3. Configura los secrets de GitHub

Ve a *Settings → Secrets and variables → Actions*:

| Secret | Descripción | Requerido |
|---|---|---|
| `SONAR_TOKEN` | Token de SonarQube/SonarCloud | Recomendado |
| `OSDO_APP_TOKEN` | Token de la OSDO App | Opcional |
| `CONTAINER_REGISTRY` | URL del registro de contenedores | Si usas Docker |

### 4. Push y observa el pipeline

```bash
echo "# Mi Proyecto Seguro" >> README.md
git add . && git commit -m "feat: initial project from osdo-starter"
git push origin main
```

El pipeline se ejecuta automáticamente. Ve a *Actions* para ver el progreso.

---

## Arquitectura del framework OSDO

```
┌─────────────────────────────────────────────────────────┐
│  osdo-starter  ← ESTÁS AQUÍ                            │
│  Template de proyecto — configuración opinionada         │
│  .osdo/config.yml  •  osdo-workflow.yml                  │
└────────────────────┬────────────────────────────────────┘
                     │ llama a (workflow_call)
                     ▼
┌─────────────────────────────────────────────────────────┐
│  opensecdevops/osdo-workflows                           │
│  10 workflows reutilizables por dominio de seguridad     │
│  osdo-framework  •  osdo-container-security             │
│  osdo-supply-chain  •  osdo-dast  •  osdo-api-security  │
└────────────────────┬────────────────────────────────────┘
                     │ usa
                     ▼
┌─────────────────────────────────────────────────────────┐
│  opensecdevops/osdo-actions  (22 actions)               │
│  Primitivos DevSecOps reutilizables                      │
│  osdo-sast  •  osdo-sca  •  osdo-secrets-scan           │
│  osdo-sbom  •  osdo-container-scan  •  osdo-sign  •...  │
└─────────────────────────────────────────────────────────┘
```

---

## Estructura del repositorio

```
osdo-starter/
├── .github/
│   └── workflows/
│       ├── osdo-workflow.yml    # Pipeline principal (delega a osdo-workflows)
│       ├── osdo-ci.yml          # CI interno de este template
│       └── osdo-sync.yml        # Sincronización de versiones cross-repo
├── .osdo/
│   ├── config.yml               # ← Configuración principal (edita esto)
│   ├── semgrep.yml              # Reglas Semgrep personalizadas
│   ├── gitleaks.toml            # Config Gitleaks
│   ├── phpstan.neon             # Config PHPStan
│   └── audit-ci.json            # Umbrales audit-ci
└── docs/
    ├── ARCHITECTURE.md
    ├── INTEGRATION_GUIDE.md
    └── QUICK_START.md
```

---

## Usar acciones individuales (modo avanzado)

Si necesitas más control, referencia las OSDO Actions directamente:

```yaml
# .github/workflows/mi-pipeline.yml
jobs:
  sast:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: opensecdevops/osdo-actions/actions/osdo-sast@osdo-sast/v2
        with:
          language: typescript
          fail-on-finding: true
          results-dir: .osdo/results

  sca:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: opensecdevops/osdo-actions/actions/osdo-sca@osdo-sca/v2
        with:
          scanners: osv,grype
          fail-on-critical: true

  sbom:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: opensecdevops/osdo-actions/actions/osdo-sbom@osdo-sbom/v2
        with:
          format: both
          upload-artifact: true
```

---

## Añadir dominios de seguridad específicos

```yaml
# Seguridad de contenedores
container-security:
  uses: opensecdevops/osdo-workflows/.github/workflows/osdo-container-security.yml@v2
  with:
    image-name: myapp
    sign-image: true
  secrets: inherit

# DAST / análisis dinámico
dast:
  uses: opensecdevops/osdo-workflows/.github/workflows/osdo-dast.yml@v2
  with:
    target-url: https://staging.myapp.com
  secrets: inherit
```

---

## Recursos

| Recurso | Enlace |
|---|---|
| Documentación completa | [opensecdevops.com/docs](https://opensecdevops.com/docs) |
| OSDO Actions (22) | [opensecdevops/osdo-actions](https://github.com/opensecdevops/osdo-actions) |
| OSDO Workflows (10) | [opensecdevops/osdo-workflows](https://github.com/opensecdevops/osdo-workflows) |
| CLI (`@osdo/cli`) | `npm install -g @osdo/cli` |
| OSDO App | [app.opensecdevops.com](https://app.opensecdevops.com) |
| Reportar issues | [GitHub Issues](https://github.com/opensecdevops/osdo-starter/issues) |

---

*OSDO Starter · Apache 2.0 · [opensecdevops.com](https://opensecdevops.com)*
