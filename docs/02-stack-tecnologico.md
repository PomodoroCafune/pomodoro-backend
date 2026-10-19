# 02 · Stack tecnológico (backend)

## Propuesta

| Área | Tecnología | Por qué |
|------|-----------|---------|
| Lenguaje | **Python 3.13** (o 3.14) | Ya instalado 3.14; 3.13 es la opción con más compatibilidad de librerías. |
| Framework | **Django 5.2 LTS** | Versión con soporte largo (hasta abril 2028). Admin gratis, ORM, migraciones. |
| API | **Django REST Framework** | Estándar de facto para APIs en Django. |
| Auth | **djangorestframework-simplejwt** | JWT (access + refresh) con lista negra para logout. |
| Documentación API | **drf-spectacular** | Genera OpenAPI 3 + Swagger UI. **Clave para trabajar con frontend**: Cinta ve el contrato siempre actualizado y puede generar tipos TypeScript. |
| CORS | **django-cors-headers** | Que React (localhost:5173) pueda llamar a la API. |
| Config | **django-environ** (o python-decouple) | Variables de entorno en `.env`. |
| Base de datos | **PostgreSQL 17** en **Docker Compose** | Mismo motor en local y producción. Sin instalar Postgres en Windows. |
| Dependencias | **uv** | Muy rápido, gestiona venv + lockfile (`uv.lock`). Alternativa: `pip` + `requirements.txt`. |
| Tests | **pytest + pytest-django + factory-boy** | Tests más legibles que `unittest`. |
| Calidad | **Ruff** (lint + formato) + **pre-commit** | Una sola herramienta, rapidísima. |
| Seguridad | **pip-audit** + **bandit** (dev) | Vulnerabilidades en dependencias y análisis estático; los usa el agente `security-auditor`. |
| CI | **GitHub Actions** | Lint + tests en cada PR a `develop`. |
| Filtros | **django-filter** | Filtrar sesiones por fecha/tarea sin código repetido. |

### Estructura de carpetas propuesta

```
pomodoro-backend/
├── config/                 # proyecto Django
│   ├── settings/ (base.py, dev.py, prod.py)
│   ├── urls.py
│   └── wsgi.py / asgi.py
├── apps/
│   ├── users/              # usuario personalizado, auth, ajustes
│   ├── tasks/              # tareas
│   ├── pomodoros/          # sesiones pomodoro
│   └── stats/              # estadísticas (solo lectura)
├── tests/ (o tests dentro de cada app)
├── openspec/               # specs y cambios (OpenSpec)
├── docs/
├── docker-compose.yml
├── pyproject.toml / uv.lock
├── .env.example
└── CLAUDE.md
```

Cada app sigue: `models.py` · `serializers.py` · `views.py` · `urls.py` · `admin.py` · `services.py` (lógica de negocio fuera de las vistas) · `tests/`.

### Convenciones de API
- Prefijo versionado: `/api/v1/...`
- JSON en `snake_case` (decisión D8).
- Fechas en **ISO 8601 con zona horaria (UTC)**; el frontend las muestra en la zona del usuario.
- Paginación estándar en listados.
- Errores con formato uniforme: `{ "detail": "...", "code": "...", "errors": {campo: [...]} }`.

## Despliegue

Opciones (decidir en el Paso 10, no ahora):
- **Render** o **Railway**: sencillos, Postgres gestionado, despliegue desde GitHub.
- **Fly.io**: más control, algo más de configuración.
- Frontend: Vercel/Netlify (lo decide Cinta).

Heroku ya no tiene plan gratuito; Vercel no es buena opción para Django con BD.

## Decisiones

Todas cerradas el 2026-10-19 (detalle en [06-decisiones.md](06-decisiones.md)):
Django **5.2 LTS** · **uv** · PostgreSQL en **Docker Compose** · **JWT** · JSON **snake_case** · temporizador en el **cliente** (el backend registra y valida coherencia).
