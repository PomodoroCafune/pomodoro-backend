# 06 · Registro de decisiones

Formato: una fila por decisión. Si se cambia una decisión, se añade una fila nueva que la sustituye (no se borra la antigua).

| # | Fecha | Decisión | Motivo | Estado |
|---|-------|----------|--------|--------|
| D1 | 2026-10-19 | Backend en Django + DRF, frontend en React (repos separados). | Definido en el documento inicial del proyecto. | Aceptada |
| D2 | 2026-10-19 | Gestión con Taiga (Kanban) + GitFlow + Conventional Commits con `Ref #ID`. | Definido en el documento inicial del proyecto. | Sustituida por D10 (commits) |
| D3 | 2026-10-19 | Desarrollo guiado por specs con OpenSpec; agentes de Claude Code; comandos vía Git Bash + RTK. | Orden, trazabilidad y ahorro de tokens. | Aceptada |
| D4 | 2026-10-19 | **Django 5.2 LTS**. | Soporte largo (hasta abril 2028), máxima compatibilidad de librerías. | Aceptada |
| D5 | 2026-10-19 | **uv** como gestor de dependencias (`pyproject.toml` + `uv.lock`). | Rápido, gestiona venv y lockfile reproducible. | Aceptada |
| D6 | 2026-10-19 | **PostgreSQL en Docker Compose** (Docker Desktop) en local. | Mismo motor que producción, sin instalar Postgres en Windows. | Aceptada |
| D7 | 2026-10-19 | **JWT** con `djangorestframework-simplejwt` (access + refresh, blacklist en logout). | Encaja con una SPA en React. | Aceptada |
| D8 | 2026-10-19 | JSON en **snake_case**. | Convención nativa de Python/DRF; sin capa de conversión. Comunicar a frontend. | Aceptada |
| D9 | 2026-10-19 | **El cliente cuenta el tiempo**; el backend solo registra sesiones (inicio, fin, estado). | Simplicidad. El riesgo (falsear tiempos) es irrelevante en una app personal. El backend hará validaciones de coherencia: `ended_at > started_at`, sin fechas futuras, duración real razonable respecto a la planificada, una sola sesión `running` por usuario. | Aceptada |
| D10 | 2026-10-19 | Commits con Conventional Commits completo: `tipo(alcance): descripción en imperativo` + cuerpo y pie opcionales. Las referencias a Taiga van en el **pie** (`Refs #ID` / `Closes #ID`), no en el asunto. Ver [05](05-flujo-trabajo.md#convención-de-commits). | Historial más legible y compatible con herramientas (changelog, versionado). | Aceptada |
| D11 | 2026-10-19 | El documento "Pomodoro Timer" (Drive) **no se sube** al repositorio. | Es un documento externo de Drive. | Aceptada |
| D12 | 2026-10-19 | Cada archivo nuevo se revisa contra el `.gitignore` antes del commit; las reglas propias van en la sección "Específico del proyecto". Ver [05](05-flujo-trabajo.md#qué-no-se-sube-al-repositorio). | Evitar subir secretos, datos locales o archivos redundantes. | Aceptada |
| D13 | 2026-10-19 | Se **versionan** los archivos de configuración IA compartidos: `CLAUDE.md`, `.claude/settings.json`, `.claude/agents/`, `.claude/commands/`, `.claude/skills/` (incluidos los generados por OpenSpec) y `openspec/`. Se ignoran los personales (`*.local.*`, `CLAUDE.local.md`) y los índices (`.codegraph/`). Versión de OpenSpec del equipo: **1.14.3**. | Cualquier desarrollador que clone el repo tiene las mismas pautas, comandos y agentes. | Aceptada |
| D15 | 2026-10-19 | Nombres de código (modelos, campos, funciones, rutas de API) **en inglés**; mensajes al usuario, docstrings, docs y commits en español. | Convención habitual; encaja con DRF y con los endpoints del MVP. | Aceptada |
| D14 | 2026-10-19 | Agente **`security-auditor`** separado del `code-reviewer`, solo lectura, con `pip-audit` y `bandit`. Obligatorio en HdU sensibles (auth, permisos, serializers, settings) y antes de cada release. | La API maneja usuarios, contraseñas y tokens; un checklist fijo de Django/DRF detecta fallos (IDOR, asignación masiva, config) que una revisión general pasa por alto. | Aceptada |
