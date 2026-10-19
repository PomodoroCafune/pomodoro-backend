# 01 · Roadmap y seguimiento

Leyenda: `[ ]` pendiente · `[~]` en curso · `[x]` hecho · `[-]` descartado/aplazado

Cada paso tiene un **criterio de "hecho"**. No se pasa al siguiente sin cumplirlo.

---

## Paso 0 · Documentación y plan  `[~]`
- [x] Crear carpeta `docs/` con roadmap, stack, agentes, MVP y flujo.
- [x] Revisar la documentación y cerrar las **decisiones** D4–D9 ([06](06-decisiones.md)).
- [ ] Hacer commit de `docs/` en una rama `docs/plan-inicial` → PR a `develop`.

**Hecho cuando:** las decisiones pendientes están respondidas y registradas en [06](06-decisiones.md).

---

## Paso 1 · Entorno de herramientas IA (Claude Code)  `[ ]`
- [ ] Confirmar que Claude Code usa **Git Bash** (necesario para RTK). Ver [03 § Git Bash](03-agentes-y-herramientas.md#1-git-bash-obligatorio).
- [ ] Verificar RTK: `rtk --version` y `rtk gain`.
- [ ] Inicializar CodeGraph en el repo (`codegraph init -i`) y añadir `.codegraph/` al `.gitignore`.
- [ ] Inicializar OpenSpec (`openspec init`) → carpeta `openspec/`.
- [ ] Crear `CLAUDE.md` del proyecto (reglas, comandos, convenciones).
- [ ] Crear los agentes en `.claude/agents/` (ver [03](03-agentes-y-herramientas.md#3-agentes-del-proyecto)).
- [ ] Probar cada agente con una tarea pequeña.

**Hecho cuando:** los agentes responden correctamente y `rtk gain` muestra ahorro en comandos ejecutados.

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
