# Ejemplos de Uso del OSDO Framework

## 1. Proyecto Node.js con React

```yaml
# .github/workflows/ci.yml
name: 🛡️ CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  osdo-security:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    permissions:
      contents: read
      security-events: write
      actions: read
      pull-requests: write
    with:
      node-version: '20'
      test-coverage-threshold: '85'
      enable-container-scan: false
      enable-sca: true
      enable-sast: true
      enable-secrets: true
      report-format: 'markdown'
```

## 2. Proyecto Python con FastAPI

```yaml
# .github/workflows/security.yml
name: 🔒 Security Compliance

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 2 * * 1'

jobs:
  osdo-python:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    permissions:
      contents: read
      security-events: write
      actions: read
      pull-requests: write
    with:
      python-version: '3.11'
      node-version: 'false'
      test-coverage-threshold: '90'
      enable-sca: true
      enable-sast: true
      enable-secrets: true
      enable-build-security: true
      sbom-format: 'both'
      build-command: 'python -m build'
      enable-quality-gate: true
      critical-threshold: '0'
      high-threshold: '5'
```

## 3. Proyecto Full-Stack con Docker

```yaml
# .github/workflows/full-stack-security.yml
name: 🛡️ Full-Stack Security Pipeline

on:
  push:
    branches: [ main, develop, staging ]
  pull_request:
    branches: [ main ]

jobs:
  osdo-fullstack:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    permissions:
      contents: read
      security-events: write
      actions: read
      pull-requests: write
    with:
      node-version: '22'
      python-version: '3.11'
      test-coverage-threshold: '80'
      enable-container-scan: true
      enable-sca: true
      enable-sast: true
      enable-secrets: true
      enable-build-security: true
      sbom-format: 'both'
      enable-quality-gate: true
      critical-threshold: '0'
      report-format: 'html'
```

## 4. Configuración Personalizada

```yaml
# .osdo/config.yml en tu proyecto
test:
  coverage:
    minimum: 85
    exclude:
      - 'tests/**'
      - 'docs/**'

security:
  sca:
    severity_threshold: 'high'
    tools:
      node:
        enabled: true
      python:
        enabled: true
      java:
        enabled: true
  
  sast:
    rules:
      - 'security'
      - 'owasp-top-10'
      - 'cwe-top-25'
  
  secrets:
    enabled: true
    tools:
      - 'gitleaks'
      - 'trufflehog'

quality_gates:
  security_score:
    minimum: 90
  compliance:
    required_checks:
      - 'dependency_scan'
      - 'secrets_scan'
      - 'static_analysis'
```

## 5. Workflow con Deployment Gate

```yaml
name: 🚀 Deploy with OSDO Gate

on:
  push:
    branches: [ main ]

jobs:
  osdo-security:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    permissions:
      contents: read
      security-events: write
      actions: read
      pull-requests: write
    with:
      test-coverage-threshold: '85'
      fail-on-high-security: true

  deploy:
    needs: osdo-security
    if: needs.osdo-security.outputs.compliance-status == 'COMPLIANT'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Production
        run: |
          echo "Deploying with OSDO compliance: ${{ needs.osdo-security.outputs.compliance-status }}"
          echo "Security Score: ${{ needs.osdo-security.outputs.security-score }}"
          # Your deployment steps here
```

## 6. Proyecto Java con Maven

```yaml
# .github/workflows/java-security.yml
name: 🛡️ Java Security Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  osdo-java:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    permissions:
      contents: read
      security-events: write
      actions: read
      pull-requests: write
    with:
      java-version: '17'
      node-version: 'false'
      test-coverage-threshold: '80'
      enable-sca: true
      enable-sast: true
      enable-secrets: true
      enable-build-security: true
      sbom-format: 'both'
      build-command: 'mvn clean install'
      enable-quality-gate: true
      critical-threshold: '0'
      high-threshold: '3'
      report-format: 'markdown'
```

## 7. Proyecto Go

```yaml
# .github/workflows/go-security.yml
name: 🔒 Go Security & Compliance

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 3 * * 1'

jobs:
  osdo-go:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    permissions:
      contents: read
      security-events: write
      actions: read
      pull-requests: write
    with:
      go-version: '1.21'
      node-version: 'false'
      test-coverage-threshold: '85'
      enable-sca: true
      enable-sast: true
      enable-secrets: true
      enable-build-security: true
      sbom-format: 'json'
      build-command: 'go build ./...'
      enable-quality-gate: true
      critical-threshold: '0'
      high-threshold: '5'
      secrets-threshold: '0'
```

## 8. Proyecto PHP con Composer

```yaml
# .github/workflows/php-security.yml
name: 🔐 PHP Security Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  osdo-php:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    permissions:
      contents: read
      security-events: write
      actions: read
      pull-requests: write
    with:
      php-version: '8.2'
      node-version: 'false'
      test-coverage-threshold: '75'
      enable-sca: true
      enable-sast: true
      enable-secrets: true
      enable-build-security: true
      sbom-format: 'both'
      build-command: 'composer install --no-dev --optimize-autoloader'
      enable-quality-gate: true
      critical-threshold: '0'
      high-threshold: '5'
```

## 9. Proyecto Multi-lenguaje (Microservicios)

```yaml
# .github/workflows/microservices-security.yml
name: 🏛️ Microservices Security

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  osdo-microservices:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    permissions:
      contents: read
      security-events: write
      actions: read
      pull-requests: write
    with:
      # Habilitar múltiples lenguajes
      node-version: '20'
      python-version: '3.11'
      java-version: '17'
      go-version: '1.21'
      
      # Configuración de seguridad estricta
      test-coverage-threshold: '80'
      enable-container-scan: true
      enable-sca: true
      enable-sast: true
      enable-secrets: true
      enable-build-security: true
      sbom-format: 'both'
      
      # Quality Gate estricto
      enable-quality-gate: true
      critical-threshold: '0'
      high-threshold: '2'
      secrets-threshold: '0'
      
      report-format: 'html'
```
