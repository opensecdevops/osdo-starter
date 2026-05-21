# Migración: osdo-workflow-template → osdo-starter

Este repositorio fue renombrado de **`osdo-workflow-template`** a **`osdo-starter`** como parte de la consolidación del framework OSDO v2.

---

## ¿Por qué el renombramiento?

El nombre anterior (`osdo-workflow-template`) describía la implementación, no el propósito.
`osdo-starter` comunica directamente que es **el punto de partida** para nuevos proyectos.

Además, v2 consolida el framework en tres capas claramente diferenciadas:

| Repositorio | Tipo | Uso |
|---|---|---|
| `opensecdevops/osdo-actions` | Librería de Actions | Referencia directa en pipelines |
| `opensecdevops/osdo-workflows` | Librería de Workflows | Referencia via `workflow_call` |
| `opensecdevops/osdo-starter` | Template | Forkear para nuevos proyectos ← **ESTÁS AQUÍ** |

---

## Cambios para usuarios que vienen de osdo-workflow-template

### 1. Workflows: acciones locales eliminadas

En v1, el template incluía actions locales en `.github/actions/`:

```
.github/actions/
  security-scan/sast/
  security-scan/sca/
  security-scan/secrets/
  build-security/sbom/
  build-security/container-scan/
  build-security/sign/
```

Estas han sido **eliminadas** de `osdo-starter`. Sus equivalentes (superiores) están en `osdo-actions`:

| Action local (v1) | Ahora (v2) |
|---|---|
| `.github/actions/security-scan/sast` | `opensecdevops/osdo-actions/actions/osdo-sast@osdo-sast/v2` |
| `.github/actions/security-scan/sca` | `opensecdevops/osdo-actions/actions/osdo-sca@osdo-sca/v2` |
| `.github/actions/security-scan/secrets` | `opensecdevops/osdo-actions/actions/osdo-secrets-scan@osdo-secrets-scan/v2` |
| `.github/actions/build-security/sbom` | `opensecdevops/osdo-actions/actions/osdo-sbom@osdo-sbom/v2` |
| `.github/actions/build-security/container-scan` | `opensecdevops/osdo-actions/actions/osdo-container-scan@osdo-container-scan/v2` |
| `.github/actions/build-security/sign` | `opensecdevops/osdo-actions/actions/osdo-sign@osdo-sign/v2` |

### 2. CI del template: Go CLI eliminado

El `osdo-ci.yml` ya no depende de `osdo-infra-cli` (Go). Usa `@osdo/cli` (Node.js):

```yaml
# Antes (v1)
- uses: actions/setup-go@v5
  with:
    go-version: "1.22"
- run: go build -o osdo
  working-directory: osdo-infra-cli

# Ahora (v2)
- uses: actions/setup-node@v4
  with:
    node-version: "22"
- run: npm install -g @osdo/cli
```

### 3. Referencia al workflow central: @v1 → @v2

En `.github/workflows/osdo-workflow.yml`:

```yaml
# Antes (v1)
uses: opensecdevops/osdo-workflows/.github/workflows/osdo-framework.yml@v1

# Ahora (v2)
uses: opensecdevops/osdo-workflows/.github/workflows/osdo-framework.yml@v2
```

### 4. CLI: osdo-infra-cli deprecado

El CLI Go (`osdo-infra-cli`) ha sido **deprecado**. Migra a `@osdo/cli`:

```bash
# Antes
go install github.com/osdo/osdo-infra-cli@latest
osdo init

# Ahora
npm install -g @osdo/cli
osdo init
```

Ver tabla completa de equivalencias en [`osdo-infra-cli/DEPRECATED.md`](../osdo-infra-cli/DEPRECATED.md).

---

## Pasos de migración (si forkeas desde osdo-workflow-template)

Si ya tienes un proyecto creado desde `osdo-workflow-template`, sigue estos pasos:

```bash
# 1. Actualizar la referencia al workflow central
sed -i 's|osdo-framework.yml@v1|osdo-framework.yml@v2|g' .github/workflows/osdo-workflow.yml

# 2. Eliminar las actions locales (ya no se necesitan)
rm -rf .github/actions/

# 3. Verificar .osdo/config.yml — es compatible con v2 sin cambios

# 4. Instalar el CLI v2 (Node.js)
npm install -g @osdo/cli
osdo --version  # debe mostrar 2.x
```

---

## ¿Qué NO cambió?

- `.osdo/config.yml` — **100% compatible** con v2, sin cambios requeridos
- El pipeline principal `osdo-workflow.yml` — mismos inputs y comportamiento
- Los quality gates y umbrales de severidad — idénticos
- La estructura de directorios del proyecto — idéntica

---

*OSDO Starter v2 · Apache 2.0 · [opensecdevops.com](https://opensecdevops.com)*
