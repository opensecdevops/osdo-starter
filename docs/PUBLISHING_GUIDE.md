# 📦 Guía de Versionado y Publicación de Releases

Esta guía explica cómo versionar las acciones de GitHub y publicar nuevas releases de manera consistente.

## 🎯 Conceptos Base

### Versionado Semántico

Seguimos [Semantic Versioning 2.0.0](https://semver.org/):

```
MAJOR.MINOR.PATCH

Ejemplos:
- 1.0.0 → Versión inicial
- 1.1.0 → Nueva feature (backwards compatible)
- 1.1.1 → Bugfix (backwards compatible)
- 2.0.0 → Cambio breaking (incompatible con 1.x)
```

### Tags de Release

Cada action se etiqueta independientemente:

```
setup-osdo/v1.0.0        ← Primera versión de setup-osdo
security-scan/v1.0.0     ← Primera versión de security-scan
setup-osdo/v1.1.0        ← Actualización menor de setup-osdo
security-scan/v2.0.0     ← Cambio mayor de security-scan
```

---

## 🔄 Workflow de Publicación

### Paso 1: Desarrollo y Testing

```bash
# Crea rama de feature
git checkout -b feature/tu-feature-aqui

# Realiza cambios (ej: mejorar security-scan)
# .github/actions/security-scan/action.yml
# .github/actions/security-scan/entrypoint.sh
# etc.

# Commit con mensaje descriptivo
git add .
git commit -m "feat(security-scan): agregar soporte para nueva herramienta X"
```

### Paso 2: Pull Request y Review

```bash
# Pushea rama
git push origin feature/tu-feature-aqui

# Abre PR en GitHub
# Incluir en descripción:
# - Acción afectada
# - Tipo de cambio (feature, fix, docs, refactor)
# - Impacto en versionado (patch, minor, major)
# - Ejemplo: "Afecta: security-scan → Tipo: feature → Versión: v1.1.0"
```

### Paso 3: Mergear a Main

Una vez aprobado el PR:

```bash
# Mergear en GitHub (squash o regular merge)
git pull origin main
```

### Paso 4: Crear Release en GitHub

En el sitio de GitHub:

1. Ve a **Releases** → **Draft a new release**

2. **Choose a tag** (o crea uno nuevo):
   - Ejemplo: `security-scan/v1.1.0`
   - Patrón: `{action-name}/v{MAJOR}.{MINOR}.{PATCH}`

3. **Release title**:
   - Ejemplo: `Release: security-scan v1.1.0`

4. **Description** (incluir):
   ```markdown
   ## Cambios Principales

   - ✨ Agregado soporte para Trivy v0.50.0
   - 🐛 Corregido timeout en Docker scanning
   - 📚 Actualizada documentación

   ## Impacto

   - ✅ Backwards compatible (minor release)
   - 📈 Performance mejorada 15%

   ## Upgrade

   ```yaml
   - uses: opensecdevops/osdo-workflow-template/.github/actions/security-scan@security-scan/v1.1.0
   ```
   ```

5. Click en **Publish release**

---

## 📋 Checklist de Release

Antes de publicar un release:

- [ ] Todos los cambios commiteados en `main`
- [ ] PR aprobado y mergeado
- [ ] Cambios testeados localmente
- [ ] CHANGELOG.md actualizado
- [ ] `action.yml` versión correcta (si aplica)
- [ ] README.md de la acción actualizado
- [ ] Tag sigue patrón: `action-name/vX.Y.Z`
- [ ] Descripción de release clara y concisa

---

## 🔀 Casos Especiales

### Múltiples Acciones Modificadas

Si afectas **varias acciones en un solo PR**:

```
PR: "refactor: mejorar validación en múltiples acciones"

Acciones afectadas:
- setup-osdo: v1.0.0 → v1.1.0 (minor)
- security-scan: v1.0.0 → v1.1.0 (minor)

Publicar 2 releases separadas:
1. setup-osdo/v1.1.0
2. security-scan/v1.1.0
```

### Hotfix Urgente

Si necesitas hotfix en versión publicada:

```bash
# Crear rama basada en el tag
git checkout -b hotfix/security-scan-v1.0.1 security-scan/v1.0.0

# Hacer cambios
git add .
git commit -m "fix(security-scan): resolver vulnerability en parsing"

# Crear PR
git push origin hotfix/security-scan-v1.0.1

# Mergear y crear release
# Tag: security-scan/v1.0.1
```

### Actción Nueva

```bash
# Desarrollo en rama
git checkout -b feature/nueva-accion-monitoring

# Agregar directorio y archivos
mkdir -p .github/actions/monitoring
# Crear action.yml, entrypoint.sh, etc.

# Commit y PR
git commit -m "feat: agregar nueva action de monitoring"

# Release primera versión
# Tag: monitoring/v1.0.0
```

---

## 🔗 Cómo Usar desde Otros Repositorios

Una vez publicado el release, otros repos pueden usar:

```yaml
# .github/workflows/security.yml
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      # Versión específica
      - uses: opensecdevops/osdo-workflow-template/.github/actions/security-scan@security-scan/v1.1.0
      
      # O usar rama (menos recomendado, puede cambiar)
      - uses: opensecdevops/osdo-workflow-template/.github/actions/security-scan@main
```

---

## 📊 Ejemplos de Mensajes de Commit

### Feature Nuevo
```
feat(security-scan): agregar soporte para Trivy container scanning

- Integrado Trivy como herramienta de scanning de contenedores
- Agregados inputs para configurar severidad mínima
- Generadas salidas en formato SARIF compatible
- Documentación actualizada

Fixes #123
```

### Bugfix
```
fix(setup-osdo): corregir timeout en setup de Docker Buildx

- Aumentado timeout de 5 a 10 minutos
- Agregado retry logic
- Mejorados logs de error

Fixes #456
```

### Refactor
```
refactor(compliance-report): mejorar generación de reportes

- Simplificado código de generación
- Mejorada performance 20%
- Mantenida compatibilidad backwards

BREAKING CHANGE: input 'report-path' ahora es requerido
```

---

## 🚀 Automatización Futura

**Próxima versión**: Crear GitHub Actions workflow para automatizar:

- [ ] Validar cambios en `action.yml`
- [ ] Ejecutar tests antes de release
- [ ] Auto-generar release notes desde commits
- [ ] Validar tags antes de publicar
- [ ] Actualizar README con nuevas versiones

---

## 📚 Referencias

- [Semantic Versioning](https://semver.org/)
- [GitHub Actions - Using Release Management](https://docs.github.com/en/actions/learn-github-actions)
- [Conventional Commits](https://www.conventionalcommits.org/)

---

**Última actualización**: 2025-12-14
