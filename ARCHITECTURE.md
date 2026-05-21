# OSDO Architecture

Documento que explica la **arquitectura completa** del framework OSDO, cómo interactúan los tres repositorios y cómo usarlos según tus necesidades.

---

## 📚 Tabla de Contenidos

1. [Visión General](#visión-general)
2. [Los Tres Pilares](#los-tres-pilares)
3. [Flujo de Interacción](#flujo-de-interacción)
4. [Matriz de Decisión](#matriz-de-decisión)
5. [Escenarios de Uso](#escenarios-de-uso)
6. [Gobernanza](#gobernanza)
7. [Roadmap](#roadmap)

---

## Visión General

OSDO es un framework modular de seguridad DevOps basado en **tres capas independientes pero relacionadas**:

```
┌─────────────────────────────────────────────────────────────┐
│                     Tu Organización                         │
└────────────┬────────────────────────────────────┬───────────┘
             │                                    │
        ┌────▼─────────────────────┐      ┌──────▼──────────────┐
        │  Tus Proyectos           │      │  Tus Proyectos      │
        │  (usando workflows)      │      │  (usando actions)   │
        └────┬─────────────────────┘      └──────┬──────────────┘
             │                                    │
    ┌────────▼────────────────────────────────────▼────────┐
    │        OSDO Framework (Orchestrator)                 │
    │  osdo-workflow-template                             │
    │  - Workflows reutilizables                          │
    │  - Best practices preconfiguradas                    │
    │  - Composite actions                                │
    └────────────────┬─────────────────────────────────────┘
                     │
        ┌────────────┴────────────────┐
        │                             │
   ┌────▼────────────────┐    ┌──────▼───────────────┐
   │   OSDO Actions      │    │  OSDO Workflows      │
   │   (Granulares)      │    │  (Reutilizables)     │
   │                     │    │                      │
   │ Cada acción = 1     │    │ Cada workflow =      │
   │ herramienta         │    │ scenario específico  │
   │                     │    │                      │
   │ - osdo-sca          │    │ - osdo-framework     │
   │ - osdo-sast         │    │ - security-only      │
   │ - osdo-secrets      │    │ - compliance-only    │
   │ - osdo-container    │    │ - quality-gates      │
   │ - ... (más)         │    │ - ci-cd-integration  │
   └─────────────────────┘    └──────────────────────┘
```

---

##  Los Tres Pilares

### OSDO Actions (`osdo-actions`)

**Propósito:** Acciones **granulares e independientes**

**Características:**
- **Responsabilidad única** - Cada acción = 1 herramienta
- **Bajo acoplamiento** - Úsalas solas o combinadas
- **Altamente reutilizable** - En cualquier workflow
- **Versionado independiente** - Cada acción evoluciona a su ritmo
- **Control total** - Customiza exactamente lo que necesitas

**Monorepo de acciones:**
```
osdo-actions/
├── actions/
│   ├── osdo-sca/
│   ├── osdo-sast/
│   ├── osdo-secrets-scan/
│   ├── osdo-container-scan/
│   ├── osdo-iac-scan/
│   ├── osdo-sbom/
│   ├── osdo-sign/
│   ├── osdo-test-quality/
│   └── osdo-compliance-report/
```

**Cuándo usarlas:**
- Necesitas **máximo control**
- Quieres combinar **herramientas específicas**
- Tu CI/CD ya tiene **estructura propia**
- Necesitas **customización extrema**

**Ejemplo:**
```yaml
- uses: opensecdevops/osdo-actions/actions/osdo-sca@osdo-sca/v1.0.0
- uses: opensecdevops/osdo-actions/actions/osdo-sast@osdo-sast/v1.2.0
- uses: opensecdevops/osdo-actions/actions/osdo-sbom@osdo-sbom/v1.0.0
```

---

### OSDO Workflows (`osdo-workflows`)

**Propósito:** Workflows **pre-built para scenarios específicos**

**Características:**
- **Orquestación lista** - Workflows completos
- **Scenarios específicos** - No opinados, pero completos
- **Reutilizables** - Via `workflow_call`
- **Configurables** - Inputs para personalización
- **Componentizados** - Cada workflow = 1 scenario

**Workflows disponibles:**
```
osdo-workflows/
├── .github/workflows/
│   ├── osdo-framework.yml        # Framework completo
│   ├── security-only.yml         # Solo seguridad
│   ├── compliance-only.yml       # Solo compliance
│   ├── quality-gates.yml         # Validación de umbrales
│   └── ci-cd-security.yml        # Integración existente
```

**Cuándo usarlos:**
- Necesitas workflows **listos para usar**
- Tienes scenarios **específicos** (ej: solo seguridad)
- Quieres **modularidad media**
- Ya tienes CI/CD y quieres **agregar funcionalidad**

**Ejemplo:**
```yaml
jobs:
  security:
    uses: opensecdevops/osdo-workflows/.github/workflows/security-only.yml@main
```

---

### OSDO Framework (`osdo-workflow-template`)

**Propósito:** Framework **completo y opinado**

**Características:**
- **Orquestación completa** - Todo integrado
- **Best practices** - Configuración sana por defecto
- **Composite actions** - Acciones compostas del framework
- **Todo incluido** - Seguridad, tests, compliance, reportes
- **Security by default** - Máxima seguridad sin esfuerzo

**Contenido:**
```
osdo-workflow-template/
├── .github/
│   ├── workflows/
│   │   ├── osdo-framework.yml       # Workflow principal
│   │   ├── osdo-complete-workflow.yml  # Ejemplo
│   │   └── publish-release.yml      # Publicar releases
│   └── actions/
│       ├── setup-osdo/
│       ├── test-quality/
│       ├── security-scan/
│       ├── build-security/
│       ├── security-quality-gate/
│       └── compliance-report/
├── docs/
├── CHANGELOG.md
└── ...
```

**Cuándo usarlo:**
- Quieres **todo incluido**
- Buscas **best practices**
- Prefieres **menos configuración**
- Necesitas **opiniones sensatas por defecto**

**Ejemplo:**
```yaml
jobs:
  osdo:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      node-version: '22'
      test-coverage-threshold: '80'
```

---

## Flujo de Interacción

### Nivel 1: Workflow → Framework → Actions

```
Tu Proyecto
    ↓
    ├→ Llama a: osdo-workflow-template/.github/workflows/osdo-framework.yml
    │           ↓
    │           ├→ Usa actions: setup-osdo, test-quality, etc.
    │           │              ↓
    │           │              └→ Algunos usan actions de osdo-actions
    │           │
    │           └→ Orquesta: jobs, artifacts, outputs
    │
    └→ Recibe: outputs (compliance-status, vulnerabilities, etc.)
```

### Nivel 2: Workflow → Actions (Directo)

```
Tu Proyecto
    ↓
    ├→ Llama a: osdo-workflows/.github/workflows/security-only.yml
    │           ↓
    │           └→ Usa actions: osdo-sast, osdo-sca, etc. (de osdo-actions)
    │
    └→ Recibe: outputs
```

### Nivel 3: Actions (Directo)

```
Tu Proyecto
    ↓
    ├→ Llama a: osdo-actions/actions/osdo-sca
    │
    └→ Recibe: outputs
```

---

## Matriz de Decisión

Usa esta matriz para elegir qué usar según tu situación:

| Situación | Recomendación | Por qué |
|-----------|---------------|--------|
| **Soy nuevo en seguridad** | osdo-framework | Best practices, todo incluido, mínima configuración |
| **Tengo CI/CD existente** | osdo-actions | Máximo control, integración gradual |
| **Necesito solo seguridad** | osdo-workflows security-only | Modulado, sin tests/quality |
| **Necesito customización extrema** | osdo-actions directas | Control total, máxima flexibilidad |
| **Múltiples proyectos** | osdo-framework o osdo-workflows | Reutilización, consistencia |
| **Startup/MVP rápido** | osdo-framework | Más rápido, todo preconfigurado |
| **Entorno corporativo** | osdo-actions | Gobernanza, auditoría, control |

---

## Escenarios de Uso

### Scenario A: Proyecto Nuevo Node.js

```yaml
name: Security & Compliance

on: [push, pull_request]

jobs:
  osdo:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      node-version: '22'
      test-coverage-threshold: '85'
      enable-sca: true
      enable-sast: true
      enable-container-scan: false
```

**Beneficios:**
- Seguridad desde el día 1
- Tests automáticos
- Compliance reports
- Cero configuración adicional

---

### Scenario B: Integración con CI/CD Existente

```yaml
# .github/workflows/ci.yml (ACTUAL)
name: Build & Deploy

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm run build

  # AGREGADO: Security
  security-scan:
    uses: opensecdevops/osdo-workflows/.github/workflows/security-only.yml@main
    needs: build
```

**Beneficios:**
- No interfiere con CI/CD actual
- Se ejecuta en paralelo
- Fácil de remover si no funciona
- Adopción gradual

---

### Scenario C: Control Total Absoluto

```yaml
name: Custom Security Pipeline

on: [push]

jobs:
  sca:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: opensecdevops/osdo-actions/actions/osdo-sca@osdo-sca/v1.0.0
        with:
          enable-npm: true
          enable-python: false

  sast:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: opensecdevops/osdo-actions/actions/osdo-sast@osdo-sast/v1.2.0
        with:
          enable-semgrep: true
          enable-bandit: false

  # ... más acciones
```

**Beneficios:**
- Control granular absoluto
- Configuración exacta
- Máxima transparencia
- Fácil de auditar

---

### Scenario D: Múltiples Proyectos Heterogéneos

```yaml
# nodejs-project/.github/workflows/ci.yml
jobs:
  osdo:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      node-version: '22'

---

# python-project/.github/workflows/ci.yml
jobs:
  osdo:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      python-version: '3.11'

---

# java-project/.github/workflows/ci.yml
jobs:
  security:
    uses: opensecdevops/osdo-workflows/.github/workflows/security-only.yml@main
    with:
      enable-java: true
```

**Beneficios:**
- Consistencia entre proyectos
- Menos duplicación
- Updates centralizados
- Gobernanza mejorada

---

## Gobernanza

### Versionado

Cada componente tiene su propio versionado:

```
osdo-actions:
  - osdo-sca/v1.0.0
  - osdo-sast/v1.0.0
  - osdo-sast/v1.1.0  ← Nueva feature

osdo-workflows:
  - security-only.yml@v1.0.0
  - osdo-framework.yml@v1.0.0

osdo-workflow-template:
  - v1.0.0
  - v1.1.0
```

### Cambios Breaking

- **Nuevas herramientas:** MINOR bump
- **API changes:** MAJOR bump
- **Bugfixes:** PATCH bump

### Comunicación

1. **Issues** - Reportar problemas
2. **Discussions** - Proponer mejoras
3. **Pull Requests** - Contribuir código
4. **Releases** - Anuncios de cambios

---

## Roadmap

### Q1 2026

- [ ] Agregar DAST support
- [ ] Mejorar reportes
- [ ] Dashboard unificado

### Q2 2026

- [ ] License compliance
- [ ] Policy as Code
- [ ] SBOM validation

### Q3 2026

- [ ] ML-based vulnerability prediction
- [ ] Custom rules engine
- [ ] Enterprise integrations

---

## Comparativa Rápida

| Feature | osdo-actions | osdo-workflows | osdo-framework |
|---------|-------------|----------------|----------------|
| **Granularidad** | Máxima | Media | Mínima |
| **Facilidad uso** | Baja | Media | Máxima |
| **Customización** | Máxima | Media | Baja |
| **Setup time** | Largo | Medio | Corto |
| **Opiniones** | Ninguna | Algunas | Muchas |
| **Best practices** | No | Sí | Sí |
| **Escalabilidad** | Excelente | Buena | Buena |
| **Comunidad control** | Alto | Medio | Bajo |

---

## Elección Recomendada por Experiencia

### Principiante en DevSecOps
→ Usar **osdo-framework**
- Todo preconfigurado
- Best practices automáticas
- Menos decisiones

### Intermedio (Tiene CI/CD)
→ Usar **osdo-workflows**
- Modularidad
- Control moderado
- Flexible

### Experto (Control total)
→ Usar **osdo-actions** directamente
- Máxima granularidad
- Gobernanza custom
- Auditoría completa

---

## Cómo Contribuir

1. Elige componente (actions, workflows, o template)
2. Reporta issue o propone feature
3. Fork + develop
4. Pull Request
5. Review y merge

---

## 📚 Referencias

- [osdo-actions README](../osdo-actions/README.md)
- [osdo-workflows README](../osdo-workflows/README.md)
- [osdo-workflow-template README](../osdo-workflow-template/README.md)
- [CONTRIBUTING.md](./CONTRIBUTING.md)
- [INTEGRATION_GUIDE.md](./INTEGRATION_GUIDE.md)

---

**OSDO Architecture** - Framework modular para seguridad DevOps

Made with ❤️ by the OpenSecDevOps Community
