# 🚀 Optimización de Caché en OSDO Framework

Este documento describe las estrategias de caché implementadas para optimizar los tiempos de ejecución de los escáneres de seguridad.

## 📊 Impacto del Caché

### Tiempos Estimados de Mejora

| Escáner | Sin Caché | Con Caché | Mejora |
|---------|-----------|-----------|--------|
| npm audit-ci | ~45s | ~10s | **78%** |
| safety (Python) | ~30s | ~8s | **73%** |
| OWASP Dependency-Check | ~180s | ~45s | **75%** |
| govulncheck (Go) | ~60s | ~15s | **75%** |
| Semgrep | ~120s | ~30s | **75%** |
| gosec | ~40s | ~12s | **70%** |
| PHPStan | ~90s | ~25s | **72%** |
| Gitleaks | ~20s | ~8s | **60%** |
| TruffleHog | ~25s | ~10s | **60%** |

**Ahorro promedio total**: ~5-8 minutos por ejecución completa

## 🗂️ Estrategias de Caché por Herramienta

### 1. Software Composition Analysis (SCA)

#### Node.js / npm
```yaml
- name: Cache npm global packages
  uses: actions/cache@v4
  with:
    path: ~/.npm
    key: ${{ runner.os }}-npm-global-${{ hashFiles('package-lock.json') }}
```
**Cachea**: Paquetes globales de npm (audit-ci, sarif-formatter)  
**Invalidación**: Cuando cambia `package-lock.json`  
**Ubicación**: `~/.npm`

#### Python / pip
```yaml
- name: Cache pip packages
  uses: actions/cache@v4
  with:
    path: |
      ~/.cache/pip
      ~/.local/bin
    key: ${{ runner.os }}-pip-sca-${{ hashFiles('requirements.txt') }}
```
**Cachea**: Paquetes pip (safety, bandit, semgrep)  
**Invalidación**: Cuando cambia `requirements.txt`  
**Ubicación**: `~/.cache/pip` y `~/.local/bin`

#### Java / Maven
```yaml
- name: Cache Maven packages
  uses: actions/cache@v4
  with:
    path: |
      ~/.m2/repository
      ~/.m2/wrapper
    key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
```
**Cachea**: Dependencias Maven y plugins  
**Invalidación**: Cuando cambia `pom.xml`  
**Ubicación**: `~/.m2/repository`

#### OWASP Dependency-Check
```yaml
- name: Cache Dependency-Check data
  uses: actions/cache@v4
  with:
    path: ~/.m2/dependency-check-data
    key: ${{ runner.os }}-dependency-check-${{ github.run_id }}
    restore-keys: |
      ${{ runner.os }}-dependency-check-
```
**Cachea**: Base de datos de CVE (descarga ~200MB)  
**Invalidación**: Por ejecución (pero restaura del caché más reciente)  
**Ubicación**: `~/.m2/dependency-check-data`  
**Nota**: Esta es la caché más impactante (ahorra 2-3 minutos)

#### Go / govulncheck
```yaml
- name: Cache Go modules
  uses: actions/cache@v4
  with:
    path: |
      ~/go/pkg/mod
      ~/go/bin
    key: ${{ runner.os }}-go-${{ hashFiles('go.sum') }}
```
**Cachea**: Módulos Go y binarios compilados  
**Invalidación**: Cuando cambia `go.sum`  
**Ubicación**: `~/go/pkg/mod` y `~/go/bin`

#### PHP / Composer
```yaml
- name: Cache Composer packages
  uses: actions/cache@v4
  with:
    path: |
      ~/.composer/cache
      vendor
    key: ${{ runner.os }}-composer-${{ hashFiles('composer.lock') }}
```
**Cachea**: Paquetes Composer y directorio vendor  
**Invalidación**: Cuando cambia `composer.lock`  
**Ubicación**: `~/.composer/cache` y `vendor/`

### 2. Static Application Security Testing (SAST)

#### Semgrep
```yaml
- name: Cache Semgrep
  uses: actions/cache@v4
  with:
    path: |
      ~/.semgrep
      ~/.cache/semgrep
    key: ${{ runner.os }}-semgrep-${{ github.run_id }}
    restore-keys: |
      ${{ runner.os }}-semgrep-
```
**Cachea**: Reglas de Semgrep descargadas  
**Invalidación**: Por ejecución (reglas actualizadas frecuentemente)  
**Ubicación**: `~/.semgrep` y `~/.cache/semgrep`

#### gosec (Go)
```yaml
- name: Cache Go tools
  uses: actions/cache@v4
  with:
    path: |
      ~/go/bin
      ~/go/pkg/mod
    key: ${{ runner.os }}-go-tools-${{ hashFiles('go.sum') }}
```
**Cachea**: Binario de gosec compilado  
**Invalidación**: Cuando cambia `go.sum`  
**Ubicación**: `~/go/bin`

#### SpotBugs (Java)
```yaml
- name: Cache Maven for SAST
  uses: actions/cache@v4
  with:
    path: ~/.m2/repository
    key: ${{ runner.os }}-maven-sast-${{ hashFiles('**/pom.xml') }}
```
**Cachea**: Plugin de SpotBugs y dependencias  
**Invalidación**: Cuando cambia `pom.xml`  
**Ubicación**: `~/.m2/repository`

#### PHPStan
```yaml
- name: Cache PHPStan
  uses: actions/cache@v4
  with:
    path: .phpstan_cache
    key: ${{ runner.os }}-phpstan-${{ hashFiles('composer.lock', '.osdo/phpstan.neon') }}
```
**Cachea**: Resultados de análisis de PHPStan  
**Invalidación**: Cuando cambia `composer.lock` o `phpstan.neon`  
**Ubicación**: `.phpstan_cache/`  
**Nota**: PHPStan tiene caché incremental muy eficiente

### 3. Secrets Detection

#### Gitleaks & TruffleHog (Docker)
```yaml
- name: Cache Docker images
  uses: actions/cache@v4
  with:
    path: ~/docker-cache
    key: ${{ runner.os }}-docker-secrets-${{ github.run_id }}
```
**Cachea**: Imágenes Docker pre-descargadas  
**Invalidación**: Por ejecución  
**Ubicación**: `~/docker-cache`  
**Nota**: Docker usa su propio layer cache

## 🔄 Estrategias de Invalidación

### Por Hash de Archivo (Más Común)
```yaml
key: ${{ runner.os }}-tool-${{ hashFiles('lock-file') }}
```
- ✅ **Ventaja**: Caché preciso, se invalida solo cuando cambian dependencias
- ⚠️ **Consideración**: Genera nueva caché por cada cambio en lock files

### Por Run ID (Para Datos Volátiles)
```yaml
key: ${{ runner.os }}-tool-${{ github.run_id }}
restore-keys: |
  ${{ runner.os }}-tool-
```
- ✅ **Ventaja**: Siempre usa caché más reciente
- ⚠️ **Consideración**: Se actualiza en cada run pero reutiliza datos previos

### Por Fecha (Para Datos de CVE)
```yaml
key: ${{ runner.os }}-cve-db-${{ github.run_number }}-${{ github.run_attempt }}
restore-keys: |
  ${{ runner.os }}-cve-db-
```
- ✅ **Ventaja**: Balance entre frescura y performance
- ⚠️ **Consideración**: Bueno para bases de datos de vulnerabilidades

## 📈 Mejores Prácticas

### 1. Orden de Caché
Siempre colocar las cachés **antes** de instalar herramientas:
```yaml
steps:
  - uses: actions/checkout@v4
  - name: Cache dependencies     # ✅ Primero caché
  - name: Install tool            # ✅ Luego instalación
  - name: Run scan                # ✅ Finalmente ejecución
```

### 2. Restore Keys
Usar `restore-keys` para fallback:
```yaml
key: ${{ runner.os }}-maven-${{ hashFiles('pom.xml') }}
restore-keys: |
  ${{ runner.os }}-maven-
```
Si no existe match exacto, usa el más reciente con el prefijo.

### 3. Separar Cachés por Propósito
```yaml
# ❌ Evitar caché única gigante
key: ${{ runner.os }}-all-tools-${{ hashFiles('**/*') }}

# ✅ Mejor: Cachés específicas
- pip-sca-${{ hashFiles('requirements.txt') }}
- pip-sast-${{ hashFiles('requirements.txt') }}
- maven-${{ hashFiles('pom.xml') }}
```

### 4. Monitorear Tamaño de Caché
GitHub Actions limita caché por repositorio:
- **Límite**: 10 GB total
- **Política**: LRU (Least Recently Used) después del límite
- **Recomendación**: Mantener cachés individuales < 500 MB

### 5. Caché Condicional
Solo crear caché si el archivo existe:
```yaml
- name: Cache Maven
  if: hashFiles('pom.xml') != ''
  uses: actions/cache@v4
```

## 🔍 Debugging de Caché

### Ver Hits/Misses de Caché
GitHub Actions muestra en logs:
```
Cache restored from key: linux-maven-abc123def
Cache hit: true
```

### Comandos Útiles para Análisis Local

#### Ver tamaño de directorios cacheados
```bash
# npm
du -sh ~/.npm

# pip
du -sh ~/.cache/pip

# Maven
du -sh ~/.m2/repository

# Go
du -sh ~/go/pkg/mod

# Composer
du -sh ~/.composer/cache
```

#### Limpiar cachés locales
```bash
# npm
npm cache clean --force

# pip
pip cache purge

# Maven
mvn dependency:purge-local-repository

# Go
go clean -modcache

# Composer
composer clear-cache
```

## 🎯 Optimizaciones Adicionales

### 1. Caché de Compilación (Build Cache)
```yaml
- name: Cache build artifacts
  uses: actions/cache@v4
  with:
    path: |
      target/
      build/
      dist/
    key: ${{ runner.os }}-build-${{ hashFiles('src/**') }}
```

### 2. Caché de Imágenes Docker
```yaml
- name: Set up Docker Buildx
  uses: docker/setup-buildx-action@v3

- name: Cache Docker layers
  uses: actions/cache@v4
  with:
    path: /tmp/.buildx-cache
    key: ${{ runner.os }}-buildx-${{ github.sha }}
    restore-keys: |
      ${{ runner.os }}-buildx-
```

### 3. Caché de Resultados SARIF (Opcional)
```yaml
- name: Cache previous SARIF results
  uses: actions/cache@v4
  with:
    path: .osdo/results/
    key: sarif-${{ github.sha }}
    restore-keys: |
      sarif-
```
**Nota**: Útil para comparar resultados entre ejecuciones.

## 📊 Monitoreo de Performance

### Métricas a Seguir

1. **Cache Hit Rate**: % de veces que se usa caché
2. **Time Saved**: Diferencia de tiempo con/sin caché
3. **Cache Size**: Tamaño actual vs límite
4. **Invalidation Rate**: Frecuencia de invalidación

### Dashboard Recomendado
```yaml
# En cada workflow, agregar:
- name: Cache Statistics
  run: |
    echo "📊 Cache Statistics"
    echo "Cache Hit: ${{ steps.cache.outputs.cache-hit }}"
    echo "Cache Key: ${{ steps.cache.outputs.cache-primary-key }}"
```

## 🔒 Seguridad del Caché

### ⚠️ No Cachear
- ❌ Secretos o credenciales
- ❌ Tokens de acceso
- ❌ Archivos `.env` con datos sensibles
- ❌ Claves privadas

### ✅ Seguro Cachear
- ✅ Dependencias públicas
- ✅ Herramientas compiladas
- ✅ Bases de datos de CVE
- ✅ Reglas de análisis

## 🎓 Referencias

- [GitHub Actions Cache Documentation](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows)
- [actions/cache Repository](https://github.com/actions/cache)
- [Cache Best Practices](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows#best-practices-for-using-cache)
- [Managing Cache](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows#managing-caches)

## 📝 Resumen de Implementación

### SCA Action
- ✅ npm global packages
- ✅ pip packages
- ✅ Maven repository
- ✅ OWASP Dependency-Check data
- ✅ Go modules
- ✅ Composer cache

### SAST Action
- ✅ Semgrep rules and cache
- ✅ pip packages for Python tools
- ✅ Go tools (gosec)
- ✅ Maven for SpotBugs
- ✅ Composer for PHPStan
- ✅ PHPStan incremental cache

### Secrets Action
- ✅ Docker images pre-pull
- ✅ Docker layer cache

---

**Resultado**: Reducción promedio de **70-75%** en tiempo de ejecución de escáneres de seguridad.
