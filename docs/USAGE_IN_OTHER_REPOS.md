# 🚀 Usando OSDO Actions en Otros Repositorios

Esta guía muestra cómo integrar las acciones de OSDO en tus propios proyectos.

---

## Requisitos Previos

1. **Repositorio público** (o con GitHub Actions habilitadas)
2. **Permisos de workflow** en tu repositorio
3. **Actions habilitadas** en Settings → Actions

---

## Opción 1: Usar el Framework Completo (Recomendado)

Para un pipeline **seguridad e compliance completo**, usa el workflow reutilizable:

### 1.1 - Crear el archivo de workflow

Crea: `.github/workflows/osdo-security.yml`

```yaml
name: OSDO Security & Compliance

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]
  schedule:
    # Ejecutar análisis de seguridad semanalmente
    - cron: '0 2 * * 1'

permissions:
  contents: read
  security-events: write
  actions: read
  pull-requests: write

jobs:
  osdo-pipeline:
    name: OSDO Security & Compliance Pipeline
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    permissions:
      contents: read
      security-events: write
      actions: read
      pull-requests: write
    with:
      # Configuración de entorno
      node-version: '22'
      python-version: '3.11'
      
      # Control del pipeline
      run-tests: true
      run-security-scan: true
      run-compliance-report: true
      
      # Umbrales de calidad
      test-coverage-threshold: '80'
      fail-on-high-security: true
      
      # Configuración de tests
      enable-unit-tests: true
      enable-integration-tests: true
      enable-e2e-tests: false
      
      # Configuración de seguridad
      enable-sca: true
      enable-sast: true
      enable-secrets: true
      enable-container-scan: false
      
      # Configuración de build security
      enable-build-security: true
      sbom-format: 'both'
      build-command: 'npm run build'
      
      # Configuración de quality gate
      enable-quality-gate: true
      critical-threshold: '0'
      high-threshold: '10'
      secrets-threshold: '0'
      
      # Formato de reporte
      report-format: 'markdown'
```

### 1.2 - Hacer el repositorio público

Configurar el repositorio como **público**:

1. Ve a **Settings** → **General**
2. Scroll hacia abajo: **Danger Zone**
3. Click en **Make this repository public**
4. Confirmar

---

## Opción 2: Usar Actions Individuales

Si prefieres **control granular**, usa acciones específicas:

### 2.1 - Setup del entorno

```yaml
name: Setup OSDO Environment

on: [push]

jobs:
  setup:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup OSDO
        uses: opensecdevops/osdo-workflow-template/.github/actions/setup-osdo@setup-osdo/v1.0.0
        with:
          node-version: '22'
          python-version: '3.11'
          setup-docker: 'false'
```

### 2.2 - Security Scanning

```yaml
jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: Run Security Scans
        uses: opensecdevops/osdo-workflow-template/.github/actions/security-scan@security-scan/v1.0.0
        with:
          results-dir: '.osdo/results'
          enable-sast: true
          enable-sca: true
          enable-secrets: true
      
      - name: Upload to Security Tab
        uses: github/codeql-action/upload-sarif@v3
        if: always()
        with:
          sarif_file: ${{ steps.security.outputs.sarif-files }}
          category: 'osdo-scan'
```

### 2.3 - Test & Quality

```yaml
jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Test Quality Gate
        uses: opensecdevops/osdo-workflow-template/.github/actions/test-quality@test-quality/v1.0.0
        with:
          results-dir: '.osdo/results'
          test-coverage-threshold: '80'
          enable-unit-tests: true
          enable-integration-tests: true
          enable-e2e-tests: false
```

### 2.4 - Build Security

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Build Security
        uses: opensecdevops/osdo-workflow-template/.github/actions/build-security@build-security/v1.0.0
        with:
          sbom-format: 'both'
          build-command: 'npm run build'
```

### 2.5 - Compliance Report

```yaml
jobs:
  compliance:
    runs-on: ubuntu-latest
    needs: [security, quality]
    if: always()
    steps:
      - uses: actions/download-artifact@v4
        with:
          path: artifacts
      
      - name: Generate Report
        uses: opensecdevops/osdo-workflow-template/.github/actions/compliance-report@compliance-report/v1.0.0
        with:
          artifacts-path: 'artifacts'
          report-format: 'markdown'
          fail-on-high: true
```

---

## Patrones de Uso Comunes

### Proyecto Node.js

```yaml
jobs:
  osdo-pipeline:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      node-version: '20'
      python-version: 'false'
      java-version: 'false'
      go-version: 'false'
      php-version: 'false'
      enable-container-scan: false
      test-coverage-threshold: '85'
      build-command: 'npm run build'
```

### Proyecto Python

```yaml
jobs:
  osdo-pipeline:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      node-version: 'false'
      python-version: '3.11'
      java-version: 'false'
      go-version: 'false'
      php-version: 'false'
      enable-container-scan: false
      test-coverage-threshold: '80'
      build-command: 'pip install -e . && python -m pytest'
```

### Proyecto Java (Maven)

```yaml
jobs:
  osdo-pipeline:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      node-version: 'false'
      python-version: 'false'
      java-version: '17'
      go-version: 'false'
      php-version: 'false'
      enable-container-scan: false
      test-coverage-threshold: '75'
      build-command: 'mvn clean package'
```

### Proyecto Go

```yaml
jobs:
  osdo-pipeline:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      node-version: 'false'
      python-version: 'false'
      java-version: 'false'
      go-version: '1.21'
      php-version: 'false'
      enable-container-scan: false
      build-command: 'go build ./...'
```

### Proyecto con Docker

```yaml
jobs:
  osdo-pipeline:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      enable-container-scan: true
      sbom-format: 'both'
      build-command: 'docker build -t myapp:latest .'
```

---

## Permisos Requeridos

Para que las acciones funcionen correctamente, asegúrate de tener los permisos adecuados:

```yaml
permissions:
  contents: read              # Para checkout del código
  security-events: write      # Para subir SARIF a Security tab
  actions: read               # Para acceso a artifacts
  pull-requests: write        # Para comentarios en PRs
  checks: write               # Para crear check runs
```

---

## Salidas y Artifacts

Después de ejecutar el pipeline, tendrás:

### Artifacts Generados

- **test-results**: Resultados de tests en JSON
- **osdo-results**: Directorio de resultados (caché, reports, etc.)
- **sarif-files**: Archivos SARIF para GitHub Security tab

### Outputs Disponibles

```yaml
# Acceso en pasos posteriores
${{ needs.osdo-pipeline.outputs.compliance-status }}
${{ needs.osdo-pipeline.outputs.test-coverage }}
${{ needs.osdo-pipeline.outputs.vulnerabilities-found }}
${{ needs.osdo-pipeline.outputs.quality-gate-status }}
${{ needs.osdo-pipeline.outputs.build-verification }}
```

---

## Actualizar a Nuevas Versiones

Cuando publiquemos nuevas versiones de las acciones:

```yaml
# Versión específica
uses: opensecdevops/osdo-workflow-template/.github/actions/security-scan@security-scan/v1.1.0

# O rama (menos recomendado)
uses: opensecdevops/osdo-workflow-template/.github/actions/security-scan@main

# Verificar cambios
# https://github.com/opensecdevops/osdo-workflow-template/releases
```

---

## Troubleshooting

### Action Not Found

```
Error: Failed to download action
```

**Solución:**
1. Verifica que el repositorio es **público**
2. Comprueba la **referencia**: `opensecdevops/osdo-workflow-template`
3. Verifica el **tag de versión** existe

### Permisos Insuficientes

```
Error: Insufficient permissions
```

**Solución:**
Agrega permisos requeridos al workflow

### Timeout

```
Error: Operation timed out
```

**Solución:**
- Aumenta `timeout-minutes` en el job
- Verifica la configuración del `build-command`

---

## 📚 Recursos Adicionales

- [CHANGELOG](../CHANGELOG.md) - Cambios por versión
- [PUBLISHING_GUIDE](./PUBLISHING_GUIDE.md) - Cómo publicar nuevas versiones
- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [Workflow Syntax Reference](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)

---

## Consejos

1. **Versiona específicamente** - Usa versiones concretas (`v1.0.0`), no `main` o `latest`
2. **Monitorea cambios** - Suscríbete a releases del repositorio
3. **Testa cambios** - Prueba en rama `develop` antes de merged a `main`
4. **Documenta** - Actualiza tu README con el workflow utilizado

---

**Última actualización**: 2025-12-14
