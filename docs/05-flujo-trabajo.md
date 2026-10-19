# 05 · Flujo de trabajo

Resumen operativo del protocolo de trabajo del equipo (documento original en Drive), con OpenSpec añadido.

## Ramas (GitFlow)

| Rama | Sale de → vuelve a | Ejemplo |
|------|--------------------|---------|
| `main` | — (producción) | — |
| `develop` | integración | — |
| `feature/TG-[ID]-[desc]` | develop → develop | `feature/TG-15-registro-usuario` |
| `bugfix/TG-[ID]-[desc]` | develop → develop | `bugfix/TG-28-fix-stats-zona-horaria` |
| `hotfix/[desc]` | main → main + develop | `hotfix/cors-produccion` |
| `docs/[desc]` | develop → develop | `docs/plan-inicial` |

Nunca se hace commit directo en `main` ni en `develop`.

## Convención de commits

Basada en [Conventional Commits](https://www.conventionalcommits.org/es/v1.0.0/).

```
<tipo>(<alcance opcional>): <descripción corta en imperativo>

[cuerpo opcional con detalles más amplios]

[pie de página opcional]
```

### Reglas
- **Asunto** (primera línea): en minúsculas, en **imperativo** ("agregar", no "agregado"/"agrega"), sin punto final, máx. ~72 caracteres.
- **Línea en blanco** entre asunto, cuerpo y pie.
- **Cuerpo**: explica el *qué* y el *por qué* (no el *cómo*). Líneas de ~72 caracteres.
- **Pie**: referencias a Taiga/GitHub y cambios incompatibles.
  - `Refs #ID` → vincula sin cerrar.
  - `Closes #ID` / `Fixes #ID` → cierra la HdU/bug al fusionar.
  - `BREAKING CHANGE: <descripción>` → cambio incompatible (p. ej. en el contrato de API). También se puede marcar con `!`: `feat(api)!: ...`.
- Si todavía no hay ID de Taiga, el pie se omite.

### Tipos

| Tipo | Uso |
|------|-----|
| `feat` | Nueva funcionalidad. |
| `fix` | Corrección de un error. |
| `docs` | Solo documentación. |
| `style` | Formato (espacios, comas…) sin cambiar lógica. |
| `refactor` | Cambio de código que ni corrige ni añade funcionalidad. |
| `test` | Añadir o corregir tests. |
| `chore` | Mantenimiento: dependencias, configuración, tooling. |
| `ci` | Cambios en la integración continua (GitHub Actions). |
| `perf` | Mejora de rendimiento. |

### Alcances habituales (backend)
`auth`, `users`, `settings`, `tasks`, `sessions`, `stats`, `api`, `db`, `config`, `deps`, `spec` (OpenSpec), `agents` (Claude Code).

### Ejemplos

Completo:
```
feat(auth): agregar autenticación con Google OAuth

Se implementa el flujo de autenticación mediante Google para permitir
el registro directo de usuarios desde la pantalla de login.

Closes #123
```

Solo asunto:
```
fix(stats): corregir cálculo de minutos según zona horaria
```

Con cambio incompatible:
```
feat(api)!: renombrar campo duration a planned_minutes en sesiones

BREAKING CHANGE: el frontend debe usar planned_minutes en lugar de duration.
Refs #42
```

## Qué no se sube al repositorio

**Regla:** cada vez que se crea un archivo o carpeta nuevos (a mano, con un comando o por una herramienta/agente), se revisa si debe ir al `.gitignore` **antes del commit**. Si debe ignorarse, se añade en la sección *"Específico del proyecto"* del `.gitignore`, en el bloque que le corresponda.

Se ignora:
| Tipo | Ejemplos |
|------|----------|
| **Secretos** | `.env`, `.env.*` (excepto `.env.example`), claves `*.pem`/`*.key`, tokens, credenciales. |
| **Generado / reproducible** | `__pycache__/`, `.venv/`, cachés (`.pytest_cache/`, `.ruff_cache/`), `htmlcov/`, `staticfiles/`. |
| **Datos locales** | Volúmenes de BD (`pgdata/`), volcados (`*.dump`), `media/` (subidas de usuarios), logs. |
| **Configuración personal** | `.claude/settings.local.json`, `CLAUDE.local.md`, `.codegraph/`, `.vscode/` (salvo `extensions.json`), `.idea/`. |
| **Documentos externos** | El documento de Drive "Pomodoro Timer" (ignorado solo en local, en `.git/info/exclude`). |

Sí se sube (aunque parezca generado): `uv.lock`, `.env.example` (sin valores reales), migraciones de Django y **toda la configuración IA compartida** (D13): `CLAUDE.md`, `openspec/`, `.claude/settings.json`, `.claude/agents/`, `.claude/skills/`, `.claude/commands/`. Los archivos de OpenSpec se regeneran con `openspec update` usando la versión del equipo (1.14.3) para no meter ruido en los diffs.

Comprobar si un archivo se ignora: `git check-ignore -v <ruta>`.
Antes de cada commit: revisar `git status` y que no aparezcan secretos ni archivos pesados.

## Ciclo de una HdU (con OpenSpec)

1. **Taiga:** mover la HdU #15 a "En proceso".
2. **Rama:** `git checkout develop && git pull && git checkout -b feature/TG-15-registro-usuario`
3. **Spec:** crear el cambio `openspec/changes/tg-15-registro-usuario/` (agente `openspec-architect`) y revisarlo. Commit: `docs(spec): proponer registro de usuario` con pie `Refs #15`.
4. **Implementar:** siguiendo `tasks.md` del cambio, con commits pequeños y pie `Refs #15`.
5. **Verificar:** lint + tests verdes (agente `test-runner`), revisión (agente `code-reviewer`).
6. **PR → develop** con `Closes #15` en la descripción. Revisa Cinta.
7. **Merge** y `openspec archive tg-15-registro-usuario` (las specs pasan a `openspec/specs/`).

## Definición de "Hecho" (DoD) de una HdU de backend
- [ ] Spec de OpenSpec aprobada y archivada.
- [ ] Tests que cubren los criterios de aceptación.
- [ ] Lint y CI en verde.
- [ ] Endpoint visible y documentado en Swagger.
- [ ] Revisado y aprobado por la otra persona.
- [ ] Si toca auth, permisos, serializers o settings: informe de `security-auditor` **sin hallazgos críticos ni altos**.

## Antes de cada release (`develop` → `main`)
- [ ] Auditoría completa con `security-auditor` sin hallazgos críticos ni altos.
- [ ] `python manage.py check --deploy` sin avisos con la configuración de producción.
- [ ] `pip-audit` sin vulnerabilidades conocidas en dependencias.
