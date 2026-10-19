# 04 · MVP: épicas, historias de usuario y tareas

> Borrador para validar con Cinta contra los Figma. Los IDs `TG-x` se sustituyen por los reales de Taiga.

## Alcance del MVP

**Dentro:** registro/login con email, ajustes de duración, tareas, temporizador que registra sesiones, estadísticas básicas (hoy/semana, por tarea).
**Fuera (post-MVP):** Google login, Spotify/música, temas y sonidos, notificaciones, estadísticas mes/año, encadenar programaciones.

## Épicas

| Épica | Descripción |
|-------|-------------|
| E1 · Cuentas | Registro, login, perfil. |
| E2 · Configuración | Duraciones del pomodoro y descansos. |
| E3 · Tareas | Gestionar tareas a las que se asocian pomodoros. |
| E4 · Temporizador | Registrar sesiones de trabajo y descanso. |
| E5 · Estadísticas | Ver tiempo dedicado. |

## Historias de usuario (MVP)

| ID | Épica | Historia | Criterios de aceptación (resumen) |
|----|-------|----------|-----------------------------------|
| HU-1 | E1 | Como usuario nuevo, quiero crear una cuenta con email y contraseña para guardar mi progreso. | Email único; contraseña validada; devuelve 201; crea ajustes por defecto. |
| HU-2 | E1 | Como usuario, quiero iniciar sesión para acceder a mis datos. | Devuelve access + refresh; 401 con credenciales erróneas. |
| HU-3 | E1 | Como usuario, quiero cerrar sesión. | El refresh token queda invalidado. |
| HU-4 | E2 | Como usuario, quiero configurar la duración del trabajo, descanso corto, descanso largo y nº de pomodoros por ciclo. | Valores por defecto 25/5/15/4; rangos validados. |
| HU-5 | E3 | Como usuario, quiero crear, editar, completar y borrar tareas. | Solo veo mis tareas. |
| HU-6 | E4 | Como usuario, quiero que se guarde cada pomodoro que inicio y termino (o cancelo). | Guarda tipo, inicio, fin, duración planificada/real, estado, tarea opcional. |
| HU-7 | E4 | Como usuario, quiero asociar un pomodoro a una tarea. | La tarea debe ser mía. |
| HU-8 | E5 | Como usuario, quiero ver cuántos pomodoros y minutos llevo hoy y esta semana. | Solo cuenta sesiones de trabajo completadas; respeta la zona horaria. |
| HU-9 | E5 | Como usuario, quiero ver cuánto tiempo he dedicado a cada tarea. | Agregado por tarea. |

## Modelo de datos (borrador)

```
User (personalizado, login por email)
 ├─1:1─ UserSettings: work_minutes, short_break_minutes, long_break_minutes,
 │                    pomodoros_per_cycle, timezone
 ├─1:N─ Task: title, description?, is_completed, created_at, updated_at
 └─1:N─ PomodoroSession: task (FK opcional), kind (work|short_break|long_break),
                         status (running|completed|cancelled),
                         planned_minutes, started_at, ended_at?, actual_seconds?
```

## Endpoints (borrador)

| Método | Ruta | HU |
|--------|------|----|
| POST | `/api/v1/auth/register/` | HU-1 |
| POST | `/api/v1/auth/token/` | HU-2 |
| POST | `/api/v1/auth/token/refresh/` | HU-2 |
| POST | `/api/v1/auth/logout/` | HU-3 |
| GET | `/api/v1/users/me/` | — |
| GET / PATCH | `/api/v1/users/me/settings/` | HU-4 |
| GET / POST | `/api/v1/tasks/` | HU-5 |
| GET / PATCH / DELETE | `/api/v1/tasks/{id}/` | HU-5 |
| POST | `/api/v1/sessions/` (inicia) | HU-6, HU-7 |
| POST | `/api/v1/sessions/{id}/complete/` · `/cancel/` | HU-6 |
| GET | `/api/v1/sessions/?from=&to=&task=` | HU-6 |
| GET | `/api/v1/stats/summary/?period=today\|week` | HU-8 |
| GET | `/api/v1/stats/by-task/?from=&to=` | HU-9 |
| GET | `/api/docs/` (Swagger) · `/api/schema/` | — |

## Preguntas para revisar con los Figma
- ¿La pantalla de inicio necesita algo del backend antes de loguearse (modo invitado)?
- ¿"Encadenar pomodoros" y "nº de pomodoros" son del MVP o post-MVP?
- ¿Las tareas tienen estimación de pomodoros, prioridad, fecha?
- ¿Qué muestra exactamente la pantalla de estadísticas en la primera versión?
