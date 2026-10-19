# CLAUDE.md — pomodoro-backend

API REST (Django + DRF) de una app Pomodoro Timer. El frontend (React) está en otro repo y lo desarrolla otra persona del equipo.
Toda la documentación está en [docs/](docs/README.md): roadmap, stack, MVP, flujo de trabajo y decisiones.

## Forma de trabajar (obligatorio)

1. **Paso a paso.** Seguir [docs/01-roadmap.md](docs/01-roadmap.md). No empezar un paso sin cerrar el anterior. Ante dudas o decisiones, **preguntar** antes de avanzar.
2. **Mantener el seguimiento.** Al terminar algo: marcar el checklist del roadmap y añadir una fila al "Diario de avance". Las decisiones nuevas van a [docs/06-decisiones.md](docs/06-decisiones.md).
3. **Git lo ejecuta la persona.** Para ramas, commits, push y PR: dar los comandos exactos (Git Bash, una línea cada uno), no ejecutarlos.
4. **Revisar `.gitignore` con cada archivo nuevo.** Tras crear archivos (a mano, con comandos, herramientas o agentes), comprobar si deben ignorarse (secretos, datos locales, generados, configuración personal) y añadirlos en la sección "Específico del proyecto". Informar de lo añadido. Detalle: [docs/05 § Qué no se sube](docs/05-flujo-trabajo.md#qué-no-se-sube-al-repositorio).
5. **Nunca** escribir secretos reales en archivos versionados; usar `.env` (ignorado) y documentar la variable en `.env.example`.

## Shell: Git Bash + RTK

- Ejecutar comandos **solo con la herramienta Bash (Git Bash)**. **No usar PowerShell**: RTK no funciona ahí.
- Sintaxis POSIX: rutas con `/`, `$VAR`, `/dev/null`.
- RTK reescribe los comandos automáticamente mediante un hook (`git status` → `rtk git status`). Para ver la salida sin filtrar: `rtk proxy <cmd>`. Ahorro: `rtk gain`.
- `codegraph` se invoca mediante el envoltorio `~/bin/codegraph`.

## Exploración de código: CodeGraph

- Preguntas estructurales (dónde se define X, quién llama a Y, qué rompe si cambio Z) → herramientas `codegraph_*` antes que grep/leer ficheros.
- Antes de modificar código existente, consultar el impacto (`codegraph_impact`).
- El índice está en `.codegraph/` (local, ignorado por git).

## Especificaciones: OpenSpec

- **No se implementa ninguna funcionalidad sin un cambio de OpenSpec aprobado** en `openspec/changes/`.
- Flujo: `/opsx:propose` → revisión humana → `/opsx:apply` → tests y revisión → PR → `/opsx:archive` tras el merge.
- Contexto y reglas de los artefactos: [openspec/config.yaml](openspec/config.yaml). Versión del equipo: OpenSpec 1.14.3.

## Git

- Ramas: `feature/TG-<id>-<desc>`, `bugfix/TG-<id>-<desc>`, `hotfix/<desc>`, `docs/<desc>`, `chore/<desc>`. Salen de `develop` (los hotfix, de `main`). Nunca commit directo en `main` ni `develop`.
- Commits: Conventional Commits en español e imperativo; las referencias a Taiga van en el pie (`Refs #id` / `Closes #id`). Detalle: [docs/05 § Convención de commits](docs/05-flujo-trabajo.md#convención-de-commits).

```
feat(auth): agregar endpoint de registro

Se crea el endpoint POST /api/v1/auth/register/ con validación de email único.

Refs #15
```

## Convenciones de código

- Python 3.13+, Django 5.2 LTS, DRF, simplejwt, drf-spectacular, PostgreSQL (Docker), uv, pytest, Ruff.
- Apps en `apps/` (`users`, `tasks`, `pomodoros`, `stats`). Lógica de negocio en `services.py`, no en vistas ni serializers.
- API: prefijo `/api/v1/`, JSON en `snake_case`, fechas ISO 8601 en UTC, formato de error uniforme `{"detail", "code", "errors"}`.
- Cada usuario solo accede a sus propios recursos (si no es suyo, 404).
- Todo endpoint nuevo lleva tests y aparece documentado en `/api/docs/`.

## Comandos del proyecto

> Se completarán en el Paso 3 (esqueleto del backend).

| Acción | Comando |
|--------|---------|
| Instalar dependencias | _pendiente_ |
| Levantar BD | _pendiente_ |
| Servidor de desarrollo | _pendiente_ |
| Tests | _pendiente_ |
| Lint / formato | _pendiente_ |
| Migraciones | _pendiente_ |

## Agentes

> Se definirán en el Paso 1.7. Ver [docs/03-agentes-y-herramientas.md](docs/03-agentes-y-herramientas.md).
