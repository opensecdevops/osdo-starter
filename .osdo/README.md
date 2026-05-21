# 🔧 OSDO Configuration Directory

Este directorio contiene los archivos de configuración para las herramientas de seguridad del OSDO Framework.

## 📁 Archivos de Configuración

### `config.yml`
Configuración principal del framework OSDO. Define:
- Configuración de testing y cobertura
- Herramientas de seguridad (SCA, SAST, Secrets)
- Comandos de construcción por lenguaje
- Quality gates y umbrales

### `spotbugs-include.xml`
Configuración de **SpotBugs** para análisis estático de Java.
- Patrones de bugs de seguridad basados en OWASP
- Detección de vulnerabilidades comunes (SQL Injection, XSS, etc.)
- Reglas para código malicioso y malas prácticas

**Uso:**
```xml
<plugin>
  <groupId>com.github.spotbugs</groupId>
  <artifactId>spotbugs-maven-plugin</artifactId>
  <configuration>
    <includeFilterFile>.osdo/spotbugs-include.xml</includeFilterFile>
  </configuration>
</plugin>
```

### `phpstan.neon`
Configuración de **PHPStan** para análisis estático de PHP.
- Nivel de análisis: 8 (muy estricto)
- Verificación de tipos y seguridad
- Detección de código no utilizado

**Uso:**
```bash
phpstan analyse -c .osdo/phpstan.neon
```

### `gitleaks.toml`
Configuración de **Gitleaks** para detección de secretos.
- Reglas para API keys, tokens, passwords
- Patrones personalizados para servicios comunes (AWS, Azure, GitHub, etc.)
- Allowlist para falsos positivos

**Uso:**
```bash
gitleaks detect --config .osdo/gitleaks.toml
```

### `dependency-check-suppressions.xml`
Archivo de supresiones para **OWASP Dependency-Check**.
- Supresión de falsos positivos
- Documentación de vulnerabilidades conocidas con mitigación
- Supresiones temporales con fecha de expiración

**Uso:**
```xml
<plugin>
  <groupId>org.owasp</groupId>
  <artifactId>dependency-check-maven-plugin</artifactId>
  <configuration>
    <suppressionFile>.osdo/dependency-check-suppressions.xml</suppressionFile>
  </configuration>
</plugin>
```

### `semgrep.yml`
Reglas personalizadas de **Semgrep** para SAST multi-lenguaje.
- Cobertura de OWASP Top 10 2021
- Detección de CWE Top 25
- Reglas para Node.js, Python, Java, Go, PHP

**Uso:**
```bash
semgrep --config .osdo/semgrep.yml
```

## 🎯 Personalización

### Configuración por Proyecto

Puedes crear un archivo `config.local.yml` para sobrescribir configuraciones específicas de tu proyecto:

```yaml
# .osdo/config.local.yml
security:
  sca:
    severity_threshold: 'critical'  # Más estricto que el default
  
  sast:
    rules:
      - 'security'
      - 'owasp-top-10'
      - 'custom-rules'
```

### Variables de Entorno

Las configuraciones pueden referenciar variables de entorno:

```yaml
security:
  tools:
    semgrep:
      timeout: ${SEMGREP_TIMEOUT:-300}
```

## 📚 Mejores Prácticas

### 1. Mantén las Configuraciones Actualizadas
Revisa y actualiza las configuraciones regularmente para incluir nuevos patrones de seguridad.

### 2. Documenta las Supresiones
Cuando suprimas vulnerabilidades en `dependency-check-suppressions.xml`, siempre incluye:
- Justificación clara
- Link a issue de seguimiento
- Fecha de revisión
- Estrategia de mitigación

### 3. Versiona las Configuraciones
Commitea los archivos de configuración al repositorio, pero excluye:
- `config.local.yml` (específico del desarrollador)
- Archivos con secretos o credenciales
- Reportes generados

### 4. Revisa las Reglas de Seguridad
Realiza revisiones trimestrales de:
- Reglas de Semgrep: Añadir nuevos patrones
- Gitleaks allowlist: Remover entradas obsoletas
- SpotBugs filters: Actualizar para nuevas amenazas
- PHPStan rules: Incrementar nivel de strictness

## 🔐 Seguridad de las Configuraciones

⚠️ **IMPORTANTE**: No commitees en estas configuraciones:
- API keys o tokens
- Passwords o credenciales
- Información sensible del proyecto
- Direcciones IP o hostnames internos

Usa el archivo `.gitignore` incluido para prevenir commits accidentales.

## 🛠️ Comandos Útiles

### Validar Configuración de Gitleaks
```bash
gitleaks detect --config .osdo/gitleaks.toml --verbose --no-git
```

### Ejecutar PHPStan
```bash
phpstan analyse --configuration .osdo/phpstan.neon --level 8 src/
```

### Ejecutar Semgrep con Reglas Custom
```bash
semgrep --config .osdo/semgrep.yml --config auto src/
```

### Ejecutar SpotBugs con Maven
```bash
mvn spotbugs:check -Dspotbugs.includeFilterFile=.osdo/spotbugs-include.xml
```

### Verificar Dependency-Check
```bash
mvn dependency-check:check -DsuppressionFile=.osdo/dependency-check-suppressions.xml
```

## 📖 Referencias

- [OWASP Top 10 2021](https://owasp.org/Top10/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [SpotBugs Documentation](https://spotbugs.readthedocs.io/)
- [PHPStan Documentation](https://phpstan.org/user-guide/getting-started)
- [Gitleaks Documentation](https://github.com/gitleaks/gitleaks)
- [Semgrep Rules](https://semgrep.dev/docs/writing-rules/overview/)
- [OWASP Dependency-Check](https://jeremylong.github.io/DependencyCheck/)

## 🤝 Contribuir

Para contribuir mejoras a las configuraciones:

1. Crea un issue describiendo la mejora
2. Prueba los cambios localmente
3. Documenta el impacto y beneficios
4. Envía un Pull Request con ejemplos

---

**Mantenido por**: OSDO Team  
**Última actualización**: 28 de noviembre de 2025
