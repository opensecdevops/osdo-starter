# ✅ Quick Reference - OSDO Acciones Versionadas

Guía rápida para usar, actualizar y publicar acciones de OSDO.

---

## 🚀 Usar en Tu Proyecto (2 minutos)

### Opción A: Framework Completo
```yaml
jobs:
  osdo:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      node-version: '22'
      test-coverage-threshold: '80'
      enable-sca: true
```

### Opción B: Acciones Individuales
```yaml
- uses: opensecdevops/osdo-workflow-template/.github/actions/setup-osdo@setup-osdo/v1.0.0
- uses: opensecdevops/osdo-workflow-template/.github/actions/security-scan@security-scan/v1.0.0
```

📖 **Guía completa**: [docs/USAGE_IN_OTHER_REPOS.md](USAGE_IN_OTHER_REPOS.md)

---

## 📦 Acciones Disponibles

| Acción | Versión | Link |
|--------|---------|------|
| setup-osdo | v1.0.0 | `@setup-osdo/v1.0.0` |
| test-quality | v1.0.0 | `@test-quality/v1.0.0` |
| security-scan | v1.0.0 | `@security-scan/v1.0.0` |
| build-security | v1.0.0 | `@build-security/v1.0.0` |
| security-quality-gate | v1.0.0 | `@security-quality-gate/v1.0.0` |
| compliance-report | v1.0.0 | `@compliance-report/v1.0.0` |

---

## 📋 Publicar Nueva Versión (5 minutos)

### Paso 1: Hacer Cambios
```bash
git checkout -b feature/tu-cambio
# Editar .github/actions/security-scan/action.yml
git commit -m "feat(security-scan): agregar nueva feature"
git push origin feature/tu-cambio
```

### Paso 2: Crear PR y Obtener Aprobación

### Paso 3: Mergear a Main
```bash
# Mergear en GitHub
```

### Paso 4: Publicar Release
1. Ve a **Actions** → **🚀 Publish Release**
2. Click en **Run workflow**
3. Selecciona:
   - **action_name**: `security-scan`
   - **version**: `1.1.0`
   - **is_breaking_change**: `false`
4. Click en **Run workflow**

✅ ¡Listo! Disponible en `@security-scan/v1.1.0`

📖 **Guía completa**: [docs/PUBLISHING_GUIDE.md](docs/PUBLISHING_GUIDE.md)

---

## 🎬 Hacer Repositorio Público

1. **Completar checklist** en [docs/FINAL_SETUP.md](docs/FINAL_SETUP.md)
2. **GitHub Settings** → **Make public**
3. **Publicar v1.0.0** de cada acción
4. **Compartir documentación**

📖 **Pasos detallados**: [docs/FINAL_SETUP.md](docs/FINAL_SETUP.md)

---

## 📊 Matriz de Decisión

```
¿Qué quiero hacer?

├─ Usar acciones en mi proyecto
│  └─ Lee: USAGE_IN_OTHER_REPOS.md
│
├─ Publicar nueva versión
│  └─ Lee: PUBLISHING_GUIDE.md
│
├─ Hacer público el repo
│  └─ Lee: FINAL_SETUP.md
│
├─ Entender versionado
│  └─ Lee: CHANGELOG.md
│
└─ Ver todo en un resumen
   └─ Lee: RESUMEN_EJECUTIVO.md
```

---

## 🔧 Ejemplos por Lenguaje

### Node.js
```yaml
uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
with:
  node-version: '20'
  python-version: 'false'
  build-command: 'npm run build'
```

### Python
```yaml
uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
with:
  python-version: '3.11'
  node-version: 'false'
  build-command: 'pip install -e . && pytest'
```

### Java
```yaml
uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
with:
  java-version: '17'
  node-version: 'false'
  build-command: 'mvn clean package'
```

### Docker
```yaml
uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
with:
  enable-container-scan: true
  sbom-format: 'both'
  build-command: 'docker build -t myapp:latest .'
```

---

## 📚 Documentación Rápida

| Documento | Leer Si... |
|-----------|-----------|
| [docs/INDEX.md](docs/INDEX.md) | Necesitas navegar toda la documentación |
| [RESUMEN_EJECUTIVO.md](docs/RESUMEN_EJECUTIVO.md) | Quieres entender qué se implementó |
| [USAGE_IN_OTHER_REPOS.md](docs/USAGE_IN_OTHER_REPOS.md) | Vas a usar acciones en tu proyecto ⭐ |
| [PUBLISHING_GUIDE.md](docs/PUBLISHING_GUIDE.md) | Vas a publicar nuevas versiones ⭐ |
| [FINAL_SETUP.md](docs/FINAL_SETUP.md) | Vas a hacer público el repo |
| [CHANGELOG.md](CHANGELOG.md) | Necesitas ver cambios por versión |
| [README.md](README.md) | Necesitas overview general |

---

## 🎯 Casos de Uso Rápidos

### "Quiero seguridad en mi proyecto Node.js"
```yaml
# .github/workflows/security.yml
jobs:
  security:
    uses: opensecdevops/osdo-workflow-template/.github/workflows/osdo-framework.yml@main
    with:
      node-version: '22'
      test-coverage-threshold: '85'
```

### "Necesito solo análisis de seguridad"
```yaml
- uses: opensecdevops/osdo-workflow-template/.github/actions/security-scan@security-scan/v1.0.0
  with:
    results-dir: '.osdo/results'
    enable-sast: true
    enable-sca: true
```

### "Quiero hacer reportes de compliance"
```yaml
- uses: opensecdevops/osdo-workflow-template/.github/actions/compliance-report@compliance-report/v1.0.0
  with:
    artifacts-path: 'artifacts'
    report-format: 'markdown'
```

---

## ⚡ Tips

1. **Usa versiones específicas** - `@v1.0.0` en lugar de `@main`
2. **Monitorea releases** - Suscríbete a GitHub releases
3. **Contribuye mejoras** - Open source = mejora para todos
4. **Documenta tu uso** - Ayuda a otros a entender tu workflow

---

## 🔗 Links Importantes

- [Repositorio](https://github.com/opensecdevops/osdo-workflow-template)
- [Releases](https://github.com/opensecdevops/osdo-workflow-template/releases)
- [Issues](https://github.com/opensecdevops/osdo-workflow-template/issues)

---

## 📞 Soporte

- **Pregunta sobre uso**: [USAGE_IN_OTHER_REPOS.md](docs/USAGE_IN_OTHER_REPOS.md)
- **Pregunta sobre publicación**: [PUBLISHING_GUIDE.md](docs/PUBLISHING_GUIDE.md)
- **Pregunta sobre setup público**: [FINAL_SETUP.md](docs/FINAL_SETUP.md)
- **Bug report**: Abre un issue en GitHub

---

**Última actualización**: 2025-12-14  
**¿Necesitas ayuda?** → Ve a [docs/INDEX.md](docs/INDEX.md)
