# 01 · Roadmap y seguimiento

Leyenda: `[ ]` pendiente · `[~]` en curso · `[x]` hecho · `[-]` descartado/aplazado

Cada paso tiene un **criterio de "hecho"**. No se pasa al siguiente sin cumplirlo.

---

## Paso 0 · Documentación y plan  `[x]`
- [x] Crear carpeta `docs/` con roadmap, stack, agentes, MVP y flujo.
- [x] Revisar la documentación y cerrar las **decisiones** D4–D9 ([06](06-decisiones.md)).
- [x] Hacer commit de `docs/` en una rama `docs/plan-inicial` → PR a `develop`.

**Hecho cuando:** las decisiones pendientes están respondidas y registradas en [06](06-decisiones.md).

---

## Paso 1 · Entorno de herramientas IA (Claude Code)  `[x]`
- [x] Confirmar que Claude Code usa **Git Bash** (necesario para RTK). Ver [03 § Git Bash](03-agentes-y-herramientas.md#1-git-bash-obligatorio).
- [x] Verificar RTK: `rtk --version` (0.38) y `rtk gain`; hook global `rtk hook claude` activo.
- [x] Hacer accesible `codegraph` desde Git Bash (envoltorio `~/bin/codegraph`, porque solo existe `codegraph.cmd`).
- [x] Inicializar CodeGraph en el repo (`codegraph init -i`) y añadir `.codegraph/` al `.gitignore`.
- [x] Inicializar OpenSpec (`openspec init --tools claude --language Spanish`) → `openspec/` + comandos `/opsx:*` y skills en `.claude/`.
- [x] Añadir contexto del proyecto y reglas en `openspec/config.yaml`.
- [x] Completar `.gitignore` con la sección del proyecto (secretos, BD local, IA, IDE, SO) y documentar la regla de revisión (D12).
- [x] Decidir qué configuración IA se versiona (D13: toda la compartida).
- [x] Crear `CLAUDE.md` del proyecto (reglas, comandos, convenciones).
- [x] Añadir agente `security-auditor` al diseño (D14).
- [x] Crear los agentes en `.claude/agents/` (ver [03](03-agentes-y-herramientas.md#3-agentes-del-proyecto)), uno a uno con revisión:
  - [x] `openspec-architect`
  - [x] `codegraph-explorer`
  - [x] `django-developer`
  - [x] `test-runner`
  - [x] `code-reviewer`
  - [x] `security-auditor`
- [x] Crear `.claude/settings.json` compartido (permisos; PowerShell, `git commit/push` y lectura de `.env` denegados).
- [x] Documentar la puesta en marcha para desarrolladores nuevos ([03 § 5](03-agentes-y-herramientas.md#5-puesta-en-marcha-para-un-desarrollador-nuevo)).
- [ ] Commit del Paso 1 en `chore/setup-herramientas-ia` → PR a `develop`.
- [-] Probar cada agente con una tarea pequeña → **se mueve al Paso 2/3**: `openspec-architect` con la primera spec; el resto cuando exista código Django.

**Hecho cuando:** la configuración está fusionada en `develop` y `rtk gain` muestra ahorro.

---

## Paso 2 · Definición del producto (con frontend)  `[ ]`
- [ ] Revisar los Figma con Cinta y listar **qué datos necesita cada pantalla**.
- [ ] Cerrar las **épicas** y las **HdU del MVP** ([04](04-mvp-backlog.md)).
- [ ] Acordar el **contrato de API** (endpoints, formatos JSON, errores) → primera spec en OpenSpec.
- [ ] Crear las HdU y tareas técnicas en **Taiga** (columnas Kanban).
- [ ] Configurar el webhook Taiga ↔ GitHub.

**Hecho cuando:** el backlog del MVP está en Taiga y el contrato de API inicial está aprobado por ambos.

---

## Paso 3 · Esqueleto del backend  `[ ]`
- [ ] Gestor de dependencias + entorno virtual (ver [02](02-stack-tecnologico.md)).
- [ ] `django-admin startproject` con settings separados (`base`, `dev`, `prod`) y variables de entorno.
- [ ] PostgreSQL local (Docker Compose).
- [ ] DRF, CORS, OpenAPI (Swagger) instalados y configurados.
- [ ] Linter/formatter (Ruff) + pre-commit.
- [ ] Herramientas de seguridad en dev: `pip-audit`, `bandit`.
- [ ] Comprobar que `uv run rtk pytest` y `uv run rtk ruff check .` funcionan (los usa `test-runner`) y rellenar la tabla de comandos de `CLAUDE.md`.
- [ ] Tests con pytest funcionando (aunque sea uno de humo).
- [ ] CI en GitHub Actions: lint + tests en cada PR.
- [ ] `README.md` con cómo arrancar el proyecto.

**Hecho cuando:** `docker compose up` + `runserver` funcionan, `/api/docs/` muestra Swagger y la CI está en verde.

---

## Paso 4 · MVP — Autenticación  `[ ]`
- [ ] Modelo de usuario personalizado (email como login) **antes de la primera migración**.
- [ ] Registro, login (JWT), refresco de token, logout, `GET /me`.
- [ ] Tests.

## Paso 5 · MVP — Ajustes del usuario  `[ ]`
- [ ] Duraciones (trabajo, descanso corto/largo), nº de pomodoros por ciclo.
- [ ] `GET/PATCH /settings`. Se crean por defecto al registrarse.

## Paso 6 · MVP — Tareas  `[ ]`
- [ ] CRUD de tareas del usuario.

## Paso 7 · MVP — Sesiones Pomodoro  `[ ]`
- [ ] Registrar sesiones (trabajo/descanso), completar/cancelar, asociar a tarea.
- [ ] Historial filtrable por fecha.

## Paso 8 · MVP — Estadísticas básicas  `[ ]`
- [ ] Pomodoros y minutos de hoy / semana; tiempo por tarea.

## Paso 9 · Integración con frontend  `[ ]`
- [ ] Probar el flujo completo con la app React (registro → pomodoro → estadísticas).
- [ ] Corregir desajustes de contrato.

## Paso 10 · Despliegue del MVP  `[ ]`
- [ ] Elegir proveedor (ver [02](02-stack-tecnologico.md#despliegue)).
- [ ] BD gestionada, variables de entorno de producción, `DEBUG=False`, HTTPS, CORS.
- [ ] Release `develop → main` con tag `v0.1.0`.

---

## Post-MVP (no tocar hasta cerrar el MVP)
- Login con Google · Estadísticas avanzadas (mes/año, gráficas) · Encadenar programaciones de pomodoros · Sonidos/temas (preferencias) · Integración Spotify · Notificaciones.

---

## Diario de avance

| Fecha | Paso | Qué se hizo | Siguiente |
|-------|------|-------------|-----------|
| 2026-10-19 | 0 | Creada documentación inicial en `docs/` | Revisar decisiones pendientes |
| 2026-10-19 | 0 | Cerradas decisiones D4–D9 (Django 5.2 LTS, uv, Docker, JWT, snake_case, timer en cliente) | Commit de `docs/` y Paso 1 |
| 2026-10-19 | 0→1 | PR de docs fusionado en `develop`. Verificados Git Bash y RTK. Detectado que `codegraph` no es accesible desde Git Bash | Envoltorio de codegraph + `codegraph init -i` |
| 2026-10-19 | 1 | CodeGraph accesible e inicializado. OpenSpec inicializado (Claude, español) y `config.yaml` con contexto y reglas | `CLAUDE.md` del proyecto |
| 2026-10-19 | 1 | `.gitignore` completado (D12). Decidido versionar la configuración IA compartida (D13). Borrador de `CLAUDE.md` | Revisar `CLAUDE.md` y crear agentes |
| 2026-10-19 | 1 | `CLAUDE.md` aprobado. Añadido `security-auditor` (D14). Empezada la creación de agentes | Revisar `openspec-architect` |
| 2026-10-19 | 1 | `openspec-architect` aprobado. Reparto de modelos acordado. Creado `codegraph-explorer` | Revisar `codegraph-explorer` |
| 2026-10-19 | 1 | `codegraph-explorer` aprobado. Creado `django-developer` | Revisar `django-developer` |
| 2026-10-19 | 1 | `django-developer` aprobado (código en inglés, D15). Creado `test-runner` con filtros RTK de pytest/ruff | Revisar `test-runner` |
| 2026-10-19 | 1 | `test-runner` aprobado. Creado `code-reviewer` | Revisar `code-reviewer` |
| 2026-10-19 | 1 | `code-reviewer` aprobado. Creado `security-auditor` | Revisar `security-auditor`, luego `settings.json` y commit |
| 2026-10-19 | 1 | `security-auditor` aprobado. Creado `.claude/settings.json` y guía de puesta en marcha. **Paso 1 cerrado** (pendiente solo el PR) | **Mañana:** Paso 2 — revisar Figma con frontend y cerrar HdU del MVP |
