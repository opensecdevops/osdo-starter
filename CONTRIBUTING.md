# 🤝 CONTRIBUTING Guide

Gracias por considerar contribuir a **OSDO** (Open SecDevOps). Este documento explica cómo contribuir código, reportar issues y colaborar con la comunidad.

---

## 📚 Tabla de Contenidos

1. [Código de Conducta](#código-de-conducta)
2. [¿Cómo Contribuir?](#cómo-contribuir)
3. [Reportar Issues](#reportar-issues)
4. [Proponer Features](#proponer-features)
5. [Proceso de Pull Request](#proceso-de-pull-request)
6. [Estándares de Código](#estándares-de-código)
7. [Testing](#testing)
8. [Documentación](#documentación)
9. [Versionado](#versionado)
10. [Resolución de Conflictos](#resolución-de-conflictos)

---

## 📜 Código de Conducta

Este proyecto adhiere a un Código de Conducta que esperamos que todos los contribuidores respeten:

### Esperamos:
- ✅ **Respeto** - Hacia todos, independientemente de su experiencia, género, identidad
- ✅ **Inclusión** - Bienvenida a contribuidores de cualquier background
- ✅ **Profesionalismo** - Comunicación constructiva y enfocada en problemas
- ✅ **Colaboración** - Ayudarse mutuamente a mejorar

### No toleramos:
- ❌ Acoso, discriminación, o lenguaje ofensivo
- ❌ Ataques personales
- ❌ Comercialización no autorizada
- ❌ Violación de privacidad

**Reporte:** Si observas violación del código, contacta a: [conduct@opensecdevops.org]

---

## 🚀 ¿Cómo Contribuir?

### Nivel 1: Reportar Issues ⭐⭐
**Esfuerzo:** 5-10 minutos  
**Requisitos:** Ninguno especial  
**Impacto:** Alto (ayuda a identificar problemas)

Reporta bugs, sugiere mejoras, plantea preguntas.

---

### Nivel 2: Mejorar Documentación ⭐⭐⭐
**Esfuerzo:** 15-30 minutos  
**Requisitos:** Comprensión del tema  
**Impacto:** Muy Alto (ayuda a otros)

Corrige typos, mejora ejemplos, añade claridad.

---

### Nivel 3: Bugfixes ⭐⭐⭐⭐
**Esfuerzo:** 30-60 minutos  
**Requisitos:** Experiencia en GitHub Actions  
**Impacto:** Crítico (sistema más confiable)

Arregla bugs reportados con tests.

---

### Nivel 4: Nuevas Features ⭐⭐⭐⭐⭐
**Esfuerzo:** 2-8 horas  
**Requisitos:** Experiencia avanzada  
**Impacto:** Transformacional (nuevas capacidades)

Propone y desarrolla nuevas funcionalidades.

---

## 🐛 Reportar Issues

### Paso 1: Busca Issues Existentes

Antes de crear un issue, verifica si ya existe:

1. Va a [Issues](../issues)
2. Busca palabras clave del problema
3. Lee issues cerrados también (puede tener solución)

---

### Paso 2: Crea el Issue

Usa el template correspondiente:

#### 🐛 Bug Report

```markdown
## Descripción
Descripción clara del bug.

## Pasos para Reproducir
1. Hice esto
2. Luego esto
3. Esperaba esto, pero pasó aquello

## Entorno
- OS: [ej: macOS 13.1]
- GitHub Actions: [ej: ubuntu-latest]
- Action version: [ej: osdo-sca@osdo-sca/v1.0.0]
- Node/Python version: [si aplica]

## Logs
```
[Incluye logs completos aquí]
```

## Solución Sugerida
[Opcional: tu idea de cómo arreglarlo]
```

#### ✨ Feature Request

```markdown
## Descripción
¿Qué funcionalidad deseas?

## Caso de Uso
¿Por qué la necesitas?

## Ejemplo
¿Cómo se vería?

## Alternativas Consideradas
¿Hay otra forma de hacerlo?
```

#### ❓ Question/Discussion

```markdown
## Pregunta
¿Qué deseas saber?

## Contexto
Información relevante.

## Ya intentaste esto?
¿Qué probaste?
```

---

### Paso 3: Labeling

Después de crear el issue, los mantainers añadirán etiquetas:

- `bug` - Problema confirmado
- `feature` - Solicitud de funcionalidad
- `documentation` - Mejora de docs
- `help-wanted` - Buscamos contribuidores
- `good-first-issue` - Bueno para principiantes
- `priority-high` - Urgente
- `priority-low` - Cuando tengamos tiempo

---

## ✨ Proponer Features

### Paso 1: Abre una Discussion

Features grandes deben discutirse primero:

1. Va a [Discussions](../discussions)
2. Categoría: **Ideas**
3. Describe tu feature

---

### Paso 2: Espera Feedback

Los maintainers responderán con:
- Viabilidad técnica
- Compatibilidad con roadmap
- Sugerencias de implementación

---

### Paso 3: PR cuando aprueban

Una vez aprobado, procede con el PR (ver sección siguiente).

---

## 🔀 Proceso de Pull Request

### Paso 1: Fork y Clone

```bash
# Fork el repositorio en GitHub
# Clone tu fork
git clone https://github.com/TU_USERNAME/osdo-workflow-template.git
cd osdo-workflow-template

# Agrega upstream remoto
git remote add upstream https://github.com/opensecdevops/osdo-workflow-template.git
```

---

### Paso 2: Crea rama

```bash
# Actualiza main
git fetch upstream
git checkout main
git merge upstream/main

# Crea rama para tu feature
git checkout -b fix/nombre-del-fix
# o
git checkout -b feature/nombre-de-feature
```

**Convención de nombres:**
```
fix/    → para bugfixes
feature/→ para nuevas features
docs/   → para documentación
test/   → para tests
chore/  → para mantenimiento
```

---

### Paso 3: Realiza cambios

```bash
# Realiza cambios
# Commit con mensajes claros

git add .
git commit -m "fix: corrige issue #123 en osdo-sca

- Cambio específico 1
- Cambio específico 2
- Resuelve #123"
```

**Convención de commits:**
```
feat:   Nueva funcionalidad
fix:    Arreglo de bug
docs:   Solo cambios de documentación
style:  Cambios que no afectan el código (formatting, etc.)
refactor: Cambios que no arreglan bugs ni agregan features
perf:   Mejoras de performance
test:   Agregar tests
chore:  Cambios en build, dependencies, etc.
```

---

### Paso 4: Keep in sync

```bash
# Actualiza con cambios del upstream
git fetch upstream
git rebase upstream/main

# Si hay conflictos, resuélvelos
# Luego: git rebase --continue
```

---

### Paso 5: Push y crea PR

```bash
git push origin feature/mi-feature
```

Luego:
1. Va a tu fork en GitHub
2. Click en "Compare & pull request"
3. Completa el template del PR

---

### Paso 6: PR Template

```markdown
## Descripción
Qué cambios hace este PR y por qué.

## Tipo de Cambio
- [ ] Bug fix (cambio no-breaking que arregla issue)
- [ ] Feature (cambio no-breaking que agrega funcionalidad)
- [ ] Breaking change (arreglo o feature que causaría breaking)
- [ ] Cambio de documentación

## Testing
Cómo testear:
1. Paso 1
2. Paso 2
3. Espera resultado X

## Checklists

### Código
- [ ] Sigo los estándares de estilo
- [ ] Realicé self-review de mi código
- [ ] Agregué comentarios para partes complejas
- [ ] Actualicé documentación si aplica

### Testing
- [ ] Los tests existentes pasan
- [ ] Agregué nuevos tests si aplica
- [ ] Mi código tiene coverage >80%

### Documentation
- [ ] Actualicé README si aplica
- [ ] Actualicé CHANGELOG si aplica
- [ ] Documenté nuevos inputs/outputs
- [ ] Agregué ejemplos si aplica

## Issues Relacionados
Resuelve #(issue number)

## Screenshots (si aplica)
[Opcional]

## Notas Adicionales
[Algo que los reviewers deban saber]
```

---

### Paso 7: Review y Merge

1. **Los maintainers revisan** - Pueden pedir cambios
2. **Haces cambios si es necesario** - Commit y push
3. **Los tests deben pasar** - Automáticamente
4. **Merge** - Un maintainer hace merge

---

## 📋 Estándares de Código

### YAML (GitHub Actions)

```yaml
# ✅ BIEN
name: Security Scan
description: Escanea el repositorio en busca de vulnerabilidades
author: OpenSecDevOps

inputs:
  enable-sca:
    description: Habilita escaneo SCA
    required: false
    default: 'true'

outputs:
  vulnerabilities-found:
    description: Número de vulnerabilidades encontradas
    value: ${{ steps.scan.outputs.vulnerabilities-found }}

runs:
  using: composite
  steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Run scan
      id: scan
      shell: bash
      run: |
        # Logic here
        echo "vulnerabilities-found=42" >> "$GITHUB_OUTPUT"
```

### Shell Scripts

```bash
#!/bin/bash
# ✅ BIEN

set -euo pipefail  # Exit on error, undefined vars, pipe failures

# Use meaningful variable names
SCAN_RESULT_FILE="scan-results.json"
VULNERABILITY_THRESHOLD=5

# Add comments for non-obvious logic
if [[ "${ENABLE_SCA}" == "true" ]]; then
    run_sca_scan
fi

# Use functions for repeated code
run_scan() {
    local scan_type="$1"
    echo "Running ${scan_type} scan..."
    # Logic
}

# Error handling
if ! run_scan "sca"; then
    echo "Error: SCA scan failed" >&2
    exit 1
fi
```

### Documentación

```markdown
# ✅ BIEN

## osdo-sca

Description clara en 1-2 líneas.

### Use Case
Cuándo usarla.

### Inputs
| Input | Required | Description |
|-------|----------|-------------|
| `enable-npm` | No | Enable npm audit |

### Outputs
| Output | Description |
|--------|-------------|
| `vulnerabilities-found` | Total vulnerabilities |

### Example
```yaml
- uses: opensecdevops/osdo-actions/actions/osdo-sca@osdo-sca/v1.0.0
  with:
    enable-npm: true
```

### Advanced Usage
Para casos más complejos.
```

---

## 🧪 Testing

### Antes de hacer PR

```bash
# 1. Ejecuta los tests locales
./scripts/test.sh

# 2. Valida YAML
yamllint .github/workflows/

# 3. Valida shell scripts
shellcheck scripts/*.sh

# 4. Prueba tu acción
act -l  # lista workflows
act -j mi-workflow  # ejecuta workflow localmente
```

### Cómo escribir tests

```yaml
# .github/workflows/test-mi-action.yml
name: Test My Action

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Test action
        uses: ./.github/actions/osdo-sca
        with:
          enable-npm: true
          
      - name: Verify output
        run: |
          if [[ -f "scan-results.json" ]]; then
              echo "✅ Test passed"
          else
              echo "❌ Test failed"
              exit 1
          fi
```

---

## 📚 Documentación

### Archivos a actualizar cuando contribuyes

1. **README.md** - Si agregaste feature nueva
2. **CHANGELOG.md** - Describe el cambio
3. **Comentarios en código** - Código complejo
4. **action.yml/action.yaml** - Inputs/outputs

### Formato de CHANGELOG

```markdown
## [1.1.0] - 2025-01-15

### Added
- Soporte para Python 3.12
- Nuevo output: `report-url`

### Fixed
- Bug en parseo de npm results

### Changed
- Mejorado performance en 20%

### Security
- Actualizado dependency insegura

[1.1.0]: https://github.com/opensecdevops/osdo-actions/releases/tag/osdo-sca/v1.1.0
```

---

## 🏷️ Versionado

OSDO usa Semantic Versioning: `MAJOR.MINOR.PATCH`

### Cuándo incrementar

- **MAJOR**: Breaking change (ej: cambio en input/output)
- **MINOR**: Nueva funcionalidad compatible
- **PATCH**: Bugfix

### Cómo publicar nueva versión

1. **Actualiza CHANGELOG.md**
2. **Actualiza version en action.yml**
3. **Crea git tag**: `git tag osdo-sca/v1.1.0`
4. **Push tags**: `git push origin osdo-sca/v1.1.0`
5. **Los GitHub Releases se crean automáticamente**

---

## ⚖️ Resolución de Conflictos

### Conflicto en review

Si un reviewer solicita cambios pero no estás de acuerdo:

1. **Respeta su perspectiva** - Explica tu punto de vista
2. **Busca consenso** - Puede haber mejor solución
3. **Escala si es necesario** - Contacta a maintainers
4. **Aprende** - Incluso si no estás de acuerdo

### Conflicto de código

Si hay merge conflict:

```bash
# Actualiza rama
git fetch upstream
git rebase upstream/main

# Resuelve conflictos
# Edita archivos conflictivos
git add .
git rebase --continue
git push -f origin mi-rama
```

---

## 🎓 Recursos

### Para Aprenders
- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [YAML Tutorial](https://yaml.org/)
- [Bash Guide](https://www.gnu.org/software/bash/manual/)

### Herramientas Útiles
- [act](https://github.com/nektos/act) - Corre workflows localmente
- [yamllint](https://github.com/adrienverge/yamllint) - Valida YAML
- [shellcheck](https://www.shellcheck.net/) - Chequea scripts bash

---

## ❓ Preguntas?

1. **Abre una Discussion** - Para preguntas generales
2. **Comenta en Issues** - Para problemas específicos
3. **Contacta a Maintainers** - Para temas urgentes

---

## 🎉 Gracias por Contribuir!

Tu contribución, sin importar el tamaño, es **valuada y apreciada**.

---

**Made with ❤️ by the OpenSecDevOps Community**
