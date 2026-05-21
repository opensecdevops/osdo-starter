# CHANGELOG - OSDO Workflow Template

Todas las acciones y workflows están versionados siguiendo [Semantic Versioning](https://semver.org/).

## Convención de Versiones

Las acciones se publican con tags siguiendo el patrón:
```
action-name/vMAJOR.MINOR.PATCH
```

Ejemplos:
- `setup-osdo/v1.0.0` - Primera versión estable
- `setup-osdo/v1.1.0` - Bugfix o feature menor
- `security-scan/v2.0.0` - Cambio breaking (incompatible con v1.x)

---

## [1.0.0] - 2025-12-14

### 🎯 Initial Public Release

#### ✨ Nuevas Acciones
- **setup-osdo/v1.0.0** - Setup de entorno OSDO (Node, Python, Java, Go, PHP)
- **test-quality/v1.0.0** - Tests y análisis de cobertura
- **security-scan/v1.0.0** - Orquestador de análisis de seguridad (SAST, SCA, Secrets)
  - `security-scan/sast/v1.0.0` - Static Application Security Testing
  - `security-scan/sca/v1.0.0` - Software Composition Analysis
  - `security-scan/secrets/v1.0.0` - Detección de secretos
- **build-security/v1.0.0** - Build seguro y generación de SBOM
- **security-quality-gate/v1.0.0** - Evaluación de umbrales de seguridad
- **compliance-report/v1.0.0** - Generación de reportes de compliance

#### Workflows Principales
- **osdo-framework.yml** - Framework reutilizable (workflow_call)
- **osdo-complete-workflow.yml** - Implementación completa de ejemplo

#### Características Principales
- Composite actions modulares y reutilizables
- Soporte multi-lenguaje (Node.js, Python, Java, Go, PHP)
- Análisis de seguridad integral (SAST, SCA, Secrets, Container)
- Quality gates configurables
- SARIF compatible con GitHub Security tab
- Generación de SBOM (JSON y XML)
- Sistema de caché inteligente (70-75% mejora de performance)
- Reportes en múltiples formatos

#### Herramientas Incluidas

**Software Composition Analysis (SCA):**
- NPM Audit + audit-ci
- Safety (Python)
- OWASP Dependency-Check
- Govulncheck (Go)
- Local PHP Security Checker

**Detección de Secretos:**
- TruffleHog
- Gitleaks

**Static Application Security Testing (SAST):**
- Semgrep (multi-lenguaje)
- Bandit (Python)
- Gosec (Go)
- SpotBugs + Find-Sec-Bugs (Java)
- PHPStan (PHP)

**Análisis de Contenedores:**
- Trivy
- Hadolint

---

## Cómo Publicar Nuevas Versiones

### 1. Desarrollo Local

```bash
# Crear rama para cambios
git checkout -b feature/nueva-caracteristica

# Realizar cambios en una acción
# Ej: .github/actions/security-scan/action.yml

git add .
git commit -m "feat: agregar soporte para nueva herramienta de scanning"
```

### 2. Pull Request

```bash
# Pushear rama
git push origin feature/nueva-caracteristica

# Crear PR con descripción de cambios
# Incluir los siguientes detalles:
# - Acción afectada (security-scan)
# - Tipo de cambio (feat, fix, docs, refactor)
# - BREAKING CHANGE: si aplica
```

### 3. Aprobar y Mergear

Una vez aprobado:
```bash
# Mergear a main/develop
git merge feature/nueva-caracteristica
```

### 4. Crear Release

En GitHub:

1. **Ve a**: Releases → Draft a new release
2. **Tag**: `action-name/vX.Y.Z`
   - Ejemplo: `security-scan/v1.1.0`
3. **Título**: `Release: action-name v1.1.0`
4. **Descripción**: Incluir cambios principales
5. **Publicar**: Click en "Publish release"

---

## Cambios por Versión

### setup-osdo

#### v1.0.0 (2025-12-14)
- Initial release
- Soporte para Node.js, Python, Java, Go, PHP
- Setup de Docker Buildx
- Creación automática de directorios OSDO

### test-quality

#### v1.0.0 (2025-12-14)
- Initial release
- Ejecución de unit tests
- Ejecución de integration tests
- Análisis de cobertura de código

### security-scan

#### v1.0.0 (2025-12-14)
- Initial release
- Orquestación de SAST, SCA y detección de secretos
- Generación de reportes SARIF

##### sast/v1.0.0
- Semgrep para análisis multi-lenguaje
- Bandit para Python
- Gosec para Go
- SpotBugs para Java
- PHPStan para PHP

##### sca/v1.0.0
- NPM Audit
- Safety (Python)
- Dependency-Check
- Govulncheck (Go)
- PHP Security Checker

##### secrets/v1.0.0
- TruffleHog
- Gitleaks
- Detección de patrones de secretos

### build-security

#### v1.0.0 (2025-12-14)
- Initial release
- Build seguro
- Generación de SBOM (CycloneDX)
- Verificación de integridad

### security-quality-gate

#### v1.0.0 (2025-12-14)
- Initial release
- Evaluación de vulnerabilidades críticas/altas
- Verificación de cobertura
- Detección de secretos

### compliance-report

#### v1.0.0 (2025-12-14)
- Initial release
- Generación de reportes en Markdown, HTML y JSON
- Integración de resultados de todos los scans
- Estados: COMPLIANT, PARTIALLY_COMPLIANT, NON_COMPLIANT

---

## Guía de Actualización

### Bugfix (Patch: v1.0.0 → v1.0.1)
- Corregir comportamiento incorrecto
- No agregar features nuevas
- Compatible hacia atrás

### Feature Menor (Minor: v1.0.0 → v1.1.0)
- Agregar feature nueva
- No afecta uso existente
- Compatible hacia atrás

### Cambio Breaking (Major: v1.0.0 → v2.0.0)
- Cambio de inputs/outputs
- Rompe compatibilidad con v1.x
- Requiere actualización en workflows que la usan

---

## Notas

- Todas las acciones son **modulares e independientes**
- Se pueden versionar **por separado**
- Los workflows consume usando **referencias con tags**
- Compatible con GitHub Actions **público**

---

**Última actualización**: 2025-12-14
