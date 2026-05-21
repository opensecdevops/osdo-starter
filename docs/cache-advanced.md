# 🎛️ Configuración Avanzada de Caché para OSDO

Ejemplos y recetas para personalizar el comportamiento del caché en proyectos específicos.

## 📋 Tabla de Contenidos

1. [Configuración por Tipo de Proyecto](#configuración-por-tipo-de-proyecto)
2. [Estrategias de Caché Avanzadas](#estrategias-de-caché-avanzadas)
3. [Troubleshooting](#troubleshooting)
4. [Recetas Específicas](#recetas-específicas)

## Configuración por Tipo de Proyecto

### Monorepo con Múltiples Lenguajes

```yaml
# .github/workflows/security-monorepo.yml
name: 🏛️ Monorepo Security

on: [push, pull_request]

jobs:
  osdo:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      node-version: '20'
      python-version: '3.11'
      java-version: '17'
      go-version: '1.21'
      enable-sca: true
      enable-sast: true
      enable-secrets: true

# Caché personalizado para monorepo
  cache-strategy:
    runs-on: ubuntu-latest
    steps:
      # Caché por directorio de servicio
      - name: Cache service-a dependencies
        uses: actions/cache@v4
        with:
          path: |
            services/service-a/node_modules
            services/service-a/.npm
          key: ${{ runner.os }}-service-a-${{ hashFiles('services/service-a/package-lock.json') }}
      
      - name: Cache service-b dependencies
        uses: actions/cache@v4
        with:
          path: |
            services/service-b/target
            services/service-b/.m2
          key: ${{ runner.os }}-service-b-${{ hashFiles('services/service-b/pom.xml') }}
```

### Proyecto con Dependencias Pesadas

Para proyectos con dependencias grandes (>500MB), usar estrategia de caché separado:

```yaml
jobs:
  cache-heavy-dependencies:
    runs-on: ubuntu-latest
    steps:
      # Separar caché de código y dependencias
      - name: Cache code dependencies
        uses: actions/cache@v4
        with:
          path: node_modules
          key: ${{ runner.os }}-deps-${{ hashFiles('package-lock.json') }}
      
      # Caché separado para bases de datos grandes
      - name: Cache CVE database
        uses: actions/cache@v4
        with:
          path: ~/.m2/dependency-check-data
          key: cve-db-${{ github.run_number }}
          restore-keys: |
            cve-db-
        
      # Caché para herramientas compiladas
      - name: Cache compiled tools
        uses: actions/cache@v4
        with:
          path: |
            ~/go/bin
            ~/.local/bin
          key: ${{ runner.os }}-tools-${{ hashFiles('.tool-versions') }}
```

### Proyecto con CI/CD Frecuente

Para proyectos con muchos commits diarios:

```yaml
jobs:
  fast-ci:
    runs-on: ubuntu-latest
    steps:
      # Caché con tiempo de vida corto
      - name: Cache with TTL
        uses: actions/cache@v4
        with:
          path: ~/.cache
          key: ${{ runner.os }}-cache-${{ github.run_number }}
          restore-keys: |
            ${{ runner.os }}-cache-
          
      # Usar caché de workflow anterior
      - name: Restore from previous run
        uses: actions/cache/restore@v4
        with:
          path: .osdo/results
          key: results-${{ github.sha }}
          restore-keys: |
            results-${{ github.event.before }}
```

## Estrategias de Caché Avanzadas

### Caché Híbrido: Por Hash + Temporal

Combina precisión con frescura de datos:

```yaml
- name: Hybrid cache strategy
  uses: actions/cache@v4
  with:
    path: ~/.cache/security-tools
    # Clave primaria: específica por dependencies
    key: ${{ runner.os }}-sec-tools-${{ hashFiles('**/lock-files') }}-${{ github.run_number }}
    # Fallback 1: Mismo lock file, run anterior
    # Fallback 2: Misma versión de herramientas
    # Fallback 3: Cualquier caché reciente
    restore-keys: |
      ${{ runner.os }}-sec-tools-${{ hashFiles('**/lock-files') }}-
      ${{ runner.os }}-sec-tools-
```

### Caché por Branch

Mantener cachés separados para diferentes branches:

```yaml
- name: Branch-specific cache
  uses: actions/cache@v4
  with:
    path: ~/.cache
    key: ${{ runner.os }}-${{ github.ref_name }}-${{ hashFiles('**/*.lock') }}
    restore-keys: |
      ${{ runner.os }}-${{ github.ref_name }}-
      ${{ runner.os }}-main-
```

### Caché Incremental con Versionado

Para herramientas que soportan caché incremental:

```yaml
- name: PHPStan incremental cache
  uses: actions/cache@v4
  with:
    path: .phpstan_cache
    # Incluir hash de configuración para invalidar en cambios
    key: phpstan-${{ hashFiles('composer.lock', '.osdo/phpstan.neon', 'src/**/*.php') }}
    restore-keys: |
      phpstan-${{ hashFiles('composer.lock', '.osdo/phpstan.neon') }}
      phpstan-
```

### Caché Multi-Stage

Para pipelines con múltiples stages:

```yaml
jobs:
  stage1-sca:
    steps:
      - name: Cache SCA results
        uses: actions/cache@v4
        with:
          path: .osdo/sca-results
          key: sca-${{ github.sha }}
  
  stage2-sast:
    needs: stage1-sca
    steps:
      - name: Restore SCA cache
        uses: actions/cache/restore@v4
        with:
          path: .osdo/sca-results
          key: sca-${{ github.sha }}
      
      - name: Cache SAST results
        uses: actions/cache@v4
        with:
          path: .osdo/sast-results
          key: sast-${{ github.sha }}
```

## Troubleshooting

### Problema: Caché No Se Actualiza

**Síntoma**: Las dependencias nuevas no se instalan

**Solución 1**: Verificar que la clave incluya hash correcto
```yaml
# ❌ Incorrecto: clave estática
key: ${{ runner.os }}-deps

# ✅ Correcto: incluir hash
key: ${{ runner.os }}-deps-${{ hashFiles('package-lock.json') }}
```

**Solución 2**: Forzar invalidación del caché
```bash
# Opción A: Actualizar lock file
npm install --package-lock-only

# Opción B: Modificar workflow
# Añadir versión a la clave
key: v2-${{ runner.os }}-deps-${{ hashFiles('package-lock.json') }}
```

### Problema: Caché Demasiado Grande

**Síntoma**: GitHub Actions reporta límite de caché alcanzado

**Diagnóstico**:
```yaml
- name: Check cache size
  run: |
    echo "Checking cache sizes..."
    du -sh ~/.npm ~/.cache/pip ~/.m2/repository ~/go/pkg/mod
```

**Solución**: Limitar directorios cacheados
```yaml
# ❌ Cachea todo (puede ser grande)
path: |
  ~/.m2
  ~/.npm
  ~/.cache

# ✅ Cachea solo lo necesario
path: |
  ~/.m2/repository
  ~/.npm/_cacache
  ~/.cache/pip/http
```

### Problema: Caché Corrupto

**Síntoma**: Builds fallan después de restaurar caché

**Solución**: Implementar validación
```yaml
- name: Cache with validation
  uses: actions/cache@v4
  with:
    path: ~/.cache/tool
    key: ${{ runner.os }}-tool-${{ hashFiles('lock') }}

- name: Validate cache
  run: |
    if [ ! -f ~/.cache/tool/valid ]; then
      echo "Cache corrupted, cleaning..."
      rm -rf ~/.cache/tool
    fi

- name: Install tool
  run: install-tool

- name: Mark cache as valid
  run: touch ~/.cache/tool/valid
```

### Problema: Caché Cross-Platform

**Síntoma**: Caché de un OS no funciona en otro

**Solución**: Separar por sistema operativo
```yaml
strategy:
  matrix:
    os: [ubuntu-latest, macos-latest, windows-latest]

steps:
  - name: Cache dependencies
    uses: actions/cache@v4
    with:
      path: ~/.cache
      # Incluir OS en la clave
      key: ${{ matrix.os }}-deps-${{ hashFiles('lock') }}
```

## Recetas Específicas

### Receta 1: Caché de CVE Database (Dependency-Check)

Optimización crítica que ahorra 2-3 minutos:

```yaml
- name: Cache OWASP Dependency-Check DB
  uses: actions/cache@v4
  with:
    path: |
      ~/.m2/dependency-check-data
    # Estrategia: Actualizar diariamente pero reutilizar en el día
    key: dependency-check-db-${{ runner.os }}-${{ github.run_number }}
    restore-keys: |
      dependency-check-db-${{ runner.os }}-
    
- name: Run Dependency-Check with cached DB
  run: |
    mvn org.owasp:dependency-check-maven:check \
      -DdataDirectory=$HOME/.m2/dependency-check-data \
      -DautoUpdate=true
```

### Receta 2: Caché de Semgrep Rules

```yaml
- name: Cache Semgrep rules
  uses: actions/cache@v4
  with:
    path: |
      ~/.semgrep
      ~/.cache/semgrep
    # Actualizar cada día
    key: semgrep-${{ runner.os }}-${{ github.run_number }}
    restore-keys: |
      semgrep-${{ runner.os }}-

- name: Run Semgrep with cache
  env:
    SEMGREP_CACHE_DIR: ~/.cache/semgrep
  run: |
    semgrep scan --config auto --sarif -o results.sarif
```

### Receta 3: Caché de Go Binaries Compilados

```yaml
- name: Cache Go binaries
  uses: actions/cache@v4
  with:
    path: |
      ~/go/bin/gosec
      ~/go/bin/govulncheck
    key: go-sec-tools-${{ runner.os }}-v1

- name: Install Go security tools (if not cached)
  run: |
    if [ ! -f ~/go/bin/gosec ]; then
      go install github.com/securego/gosec/v2/cmd/gosec@latest
    fi
    if [ ! -f ~/go/bin/govulncheck ]; then
      go install golang.org/x/vuln/cmd/govulncheck@latest
    fi
```

### Receta 4: Caché de Docker Layers

Para Gitleaks y TruffleHog:

```yaml
- name: Set up Docker Buildx
  uses: docker/setup-buildx-action@v3

- name: Cache Docker layers
  uses: actions/cache@v4
  with:
    path: /tmp/.buildx-cache
    key: docker-${{ runner.os }}-${{ github.sha }}
    restore-keys: |
      docker-${{ runner.os }}-

- name: Pull and cache security tools
  run: |
    docker pull zricethezav/gitleaks:latest
    docker pull trufflesecurity/trufflehog:latest
```

### Receta 5: Caché Condicional por Tipo de Cambio

```yaml
- name: Detect changes
  id: changes
  uses: dorny/paths-filter@v2
  with:
    filters: |
      backend:
        - 'backend/**'
      frontend:
        - 'frontend/**'

- name: Cache backend dependencies
  if: steps.changes.outputs.backend == 'true'
  uses: actions/cache@v4
  with:
    path: backend/node_modules
    key: backend-${{ hashFiles('backend/package-lock.json') }}

- name: Cache frontend dependencies
  if: steps.changes.outputs.frontend == 'true'
  uses: actions/cache@v4
  with:
    path: frontend/node_modules
    key: frontend-${{ hashFiles('frontend/package-lock.json') }}
```

### Receta 6: Caché con Compresión

Para proyectos muy grandes:

```yaml
- name: Cache with compression
  uses: actions/cache@v4
  with:
    path: large-directory
    key: compressed-${{ hashFiles('**/*.lock') }}
    # GitHub Actions automáticamente comprime con gzip
    # Para mejor compresión, pre-comprimir:
    
- name: Pre-compress cache
  run: |
    tar czf cache.tar.gz large-directory
    
- name: Cache compressed file
  uses: actions/cache@v4
  with:
    path: cache.tar.gz
    key: compressed-${{ hashFiles('**/*.lock') }}
```

## 🎯 Best Practices Resumen

1. **Siempre incluir OS en la clave** para evitar conflictos cross-platform
2. **Usar restore-keys** para fallback inteligente
3. **Separar cachés por propósito** (build vs test vs security)
4. **Validar integridad** antes de usar caché restaurado
5. **Monitorear tamaños** para no exceder límites de GitHub
6. **Documentar estrategias** en el código con comentarios
7. **Testear invalidación** verificando que nuevas dependencias se instalen
8. **Usar caché condicional** para optimizar pipelines selectivos

## 📚 Referencias Adicionales

- [GitHub Actions Cache Limits](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows#usage-limits-and-eviction-policy)
- [Cache Action Documentation](https://github.com/actions/cache)
- [Docker Build Cache](https://docs.docker.com/build/cache/)
- [Go Module Cache](https://go.dev/ref/mod#module-cache)
- [Maven Local Repository](https://maven.apache.org/guides/introduction/introduction-to-repositories.html)

---

**Mantenido por**: OSDO Team  
**Última actualización**: 28 de noviembre de 2025
