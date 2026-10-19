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
