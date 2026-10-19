# 03 · Agentes y herramientas IA

Trabajamos con **Claude Code** y varios **subagentes** especializados (ficheros en `.claude/agents/*.md`). Cada agente tiene un rol, unas herramientas permitidas y unas reglas.

## 1. Git Bash (obligatorio)

RTK solo funciona si los comandos se ejecutan en **Git Bash**, no en PowerShell ni cmd.

- En Windows, la herramienta `Bash` de Claude Code usa Git Bash. Si no la encuentra, se configura con la variable `CLAUDE_CODE_GIT_BASH_PATH` (p. ej. `C:\Program Files\Git\bin\bash.exe`).
- Los agentes del proyecto **solo tendrán la herramienta `Bash`**, nunca `PowerShell`, para que no puedan saltarse RTK.
- Sintaxis siempre POSIX: `/` en rutas, `$VAR`, `/dev/null`.

## 2. Herramientas base

| Herramienta | Para qué | Cómo se usa |
|-------------|----------|-------------|
| **RTK** (Rust Token Killer) | Comprime la salida de comandos (git, tests, ls…) → 60–90 % menos tokens. | Un *hook* de Claude Code reescribe `git status` → `rtk git status` automáticamente. Ver ahorro: `rtk gain`. Comando sin filtrar: `rtk proxy <cmd>`. |
| **CodeGraph** | Grafo del código (símbolos, llamadas, impacto). Responde "¿quién llama a X?", "¿qué rompe si cambio Y?" sin leer ficheros enteros. | MCP `codegraph_*`. Requiere `codegraph init -i` en el repo (crea `.codegraph/`, no se sube a git). |
| **OpenSpec** | Desarrollo guiado por especificaciones: antes de programar se escribe *qué* debe hacer el cambio. | CLI `openspec` (v1.14). Carpeta `openspec/specs/` (estado actual) y `openspec/changes/` (propuestas). Flujo: **proponer → aprobar → implementar → archivar**. |

## 3. Agentes del proyecto

| Agente | Rol | Herramientas | Cuándo usarlo |
|--------|-----|--------------|---------------|
| `openspec-architect` | Convierte una HdU de Taiga en un cambio OpenSpec (`proposal.md`, `tasks.md`, deltas de spec). Valida con `openspec validate`. **No escribe código.** | Bash (rtk), Read, Write/Edit solo en `openspec/` | Al empezar cada HdU. |
| `codegraph-explorer` | Explora el código con CodeGraph: contexto, llamadas, impacto de cambios. **Solo lectura.** | MCP codegraph, Read, Grep, Glob (sin Bash) | Antes de modificar algo existente; para análisis de impacto. |
| `django-developer` | Implementa las tareas de un cambio OpenSpec aprobado: modelos, serializers, vistas, migraciones, tests. | Bash (rtk), Read, Edit, Write, codegraph | Fase de implementación. |
| `test-runner` | Ejecuta tests, lint y migraciones **a través de RTK** y devuelve un resumen corto de fallos. | Bash (rtk), Read | Tras implementar; para no llenar el contexto con logs. |
| `code-reviewer` | Revisa el diff contra la spec y las convenciones (corrección, legibilidad, tests) antes del PR. **Solo lectura.** | Bash (rtk git), Read, Grep, codegraph | Antes de abrir el PR. |
| `security-auditor` | Audita seguridad con checklist Django/DRF (OWASP API Top 10): acceso a datos ajenos (IDOR), asignación masiva, auth/JWT, throttling, settings de producción, CORS, secretos, dependencias. Usa `manage.py check --deploy`, `pip-audit` y `bandit`. Informe por gravedad. **Solo lectura.** | Bash (rtk), Read, Grep, codegraph | Antes del PR de HdU que tocan auth, permisos, serializers o settings; antes de cada release/despliegue; al actualizar dependencias. |
| `api-contract-keeper` *(opcional)* | Mantiene el esquema OpenAPI y un changelog de API para frontend. | Bash (rtk), Read, Write | Cuando cambia un endpoint. |

**Modelos por agente** (equilibrio calidad/coste): `opus` → `openspec-architect`, `code-reviewer`, `security-auditor` (razonamiento crítico) · `sonnet` → `django-developer`, `codegraph-explorer` · `haiku` → `test-runner` (solo ejecuta y resume).

`codegraph-explorer` no tiene Bash ni herramientas de escritura: es estrictamente de solo lectura y trabaja con las herramientas MCP de CodeGraph.

> **RTK no es un agente en sí:** es una capa que se aplica a *todos* los agentes que usan Bash. El agente `test-runner` es el que más partido le saca (salida de tests comprimida).

### Ciclo con agentes para cada HdU

```
Taiga HdU #15
   │
   ▼
openspec-architect ──► openspec/changes/tg-15-registro-usuario/  (propuesta)
   │                         │
   │         revisión humana (tú) ✔
   ▼
codegraph-explorer ──► contexto/impacto del código existente
   ▼
django-developer   ──► código + tests (rama feature/TG-15-...)
   ▼
test-runner        ──► lint + tests verdes
   ▼
code-reviewer      ──► informe de revisión
   ▼
security-auditor   ──► informe de seguridad (solo si la HdU es sensible)
   ▼
PR a develop (Closes #15) ──► revisión de Cinta ──► merge ──► openspec archive
```

## 4. Ficheros que se crearán (Paso 1)

```
CLAUDE.md                      # reglas del proyecto para Claude
.claude/
├── settings.json              # permisos, hooks (RTK)
└── agents/
    ├── openspec-architect.md
    ├── codegraph-explorer.md
    ├── django-developer.md
    ├── test-runner.md
    ├── code-reviewer.md
    └── security-auditor.md
openspec/                      # generado por `openspec init`
```

## 5. Puesta en marcha para un desarrollador nuevo

Lo versionado (agentes, skills, comandos `/opsx:*`, `CLAUDE.md`, `.claude/settings.json`) llega al clonar. Cada persona debe instalar y configurar **en su máquina**:

1. **Git Bash** (Git for Windows). Claude Code debe usarlo como shell.
2. **RTK** + su hook global en `~/.claude/settings.json` (`rtk init` o hook `rtk hook claude` en `PreToolUse`). Comprobar con `rtk gain`.
3. **CodeGraph**. En Windows, si solo existe `codegraph.cmd`, crear el envoltorio para Git Bash:
   ```bash
   mkdir -p ~/bin
   printf '#!/usr/bin/env bash\nCG="$LOCALAPPDATA/codegraph/current"\nexec "$CG/node.exe" --liftoff-only "$CG/lib/dist/bin/codegraph.js" "$@"\n' > ~/bin/codegraph
   chmod +x ~/bin/codegraph
   ```
   Después, en la raíz del repo: `codegraph init -i` (el índice `.codegraph/` es local). Si CodeGraph se actualiza y cambia su estructura de carpetas, revisar la ruta del envoltorio.
4. **OpenSpec 1.14.3** (versión del equipo): `npm i -g @fission-ai/openspec@1.14.3` (o con pnpm).
5. Reiniciar Claude Code en el repo para que cargue agentes y permisos.

## 6. Permisos del proyecto (`.claude/settings.json`)

| Regla | Motivo |
|-------|--------|
| Permitido sin preguntar: `openspec`, `codegraph`, `rtk gain`, `git status/diff/log/fetch/check-ignore`, herramientas MCP de CodeGraph | Comandos de solo lectura o de las herramientas del flujo. |
| **Denegado: PowerShell** | RTK solo funciona en Git Bash. |
| **Denegado: `git commit` y `git push`** | Los commits y push los ejecuta la persona (acuerdo del equipo). |
| **Denegado: leer `.env`** | Evitar que los secretos lleguen al contexto del modelo. |

Las preferencias personales van en `.claude/settings.local.json` (ignorado por git).

## 7. Notas
- **Figma:** el conector de Figma de Claude necesita autorizarse en los ajustes de conectores de claude.ai. Si se autoriza, los agentes pueden leer los diseños para extraer qué datos pide cada pantalla.
- **Taiga:** no hay conector; la trazabilidad se hace con los IDs en ramas, commits y nombres de cambios OpenSpec.
