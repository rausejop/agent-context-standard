# Agent Context Standard

> Forma estándar de compartir contexto de proyecto con agentes de codificación IA — portable, harness-agnóstica, eficiente en tokens.

> 🇪🇸 Documento en castellano. English version: [Agent_Context_Standard_v1.0.en.md](Agent_Context_Standard_v1.0.en.md).
> ID de spec formal: `ACS-001-agent-context-standard` · v1.0 · 2026-05-05.

---

## ¿Qué es ACS?

El Agent Context Standard (ACS) es un formato abierto y ligero para guardar el contexto que un agente de codificación IA necesita para reanudar el trabajo en un proyecto. Complementa el formato [Agent Skills](https://agentskills.io): donde Agent Skills capturan *capacidades* portables, ACS captura *contexto de proyecto* portable — hechos de larga duración, investigación, estado de sesión, decisiones y especificaciones.

En su núcleo, un proyecto conforme a ACS contiene un único directorio de contexto:

```
mi-proyecto/
├── llm-session/                # ← EL único directorio con todo el contexto del agente
│   ├── MANIFEST.yaml           # Obligatorio: versión del schema + metadatos del proyecto
│   ├── BOOT.md                 # Obligatorio: punto de entrada universal
│   ├── memory/                 # Hechos de larga duración, reglas, convenciones
│   ├── knowledge/              # Dossiers de investigación, datos reales citables
│   ├── state/                  # Snapshots de sesión, "dónde lo dejamos"
│   ├── skills/                 # Agent Skills reutilizables (per agentskills.io)
│   ├── specs/                  # Artefactos de Spec-Driven Development
│   └── decisions/              # Architecture Decision Records (Nygard)
├── CLAUDE.md / GEMINI.md / .cursorrules / AGENTS.md   # Shims de harness de una línea
└── ...                         # Tus archivos del proyecto
```

`cp -r` ese directorio a otra máquina y la siguiente sesión del agente reanuda con todo el contexto.

## ¿Por qué ACS?

Cada harness de agente actual guarda "memoria" en su propio formato y ubicación:

* Claude Code → `CLAUDE.md` o `~/.claude/projects/<sanitized-path>/memory/`
* Cursor → `.cursorrules` o `.cursor/rules/*.mdc`
* Gemini CLI → `GEMINI.md`
* Antigravity → `AGENTS.md`
* Continue.dev → `.continue/system.md`

Resultado: **el conocimiento es prisionero del harness y de la máquina.** Cambias de herramienta o copias el proyecto a otro sitio y se pierde la continuidad.

ACS lo arregla:

* **Portable**: cada artefacto vive dentro del proyecto. Sin estado global.
* **Harness-agnóstico**: cualquier herramienta compatible lee el mismo directorio mediante un shim raíz de una línea.
* **Eficiente en tokens**: los agentes hacen carga perezosa vía INDEX → frontmatter → cuerpo. Sin sumarios parafraseados, sin pérdida de fidelidad.
* **Versionado**: `MANIFEST.yaml.acs_version` declara el schema; los harnesses ignoran extensiones desconocidas con seguridad.
* **Spec-Driven Development integrado**: cada cambio traza a una spec numerada e inmutable-tras-aceptación.

## ¿Cómo funciona ACS?

ACS extiende el formato Agent Skills (usado para `skills/`) con cinco tipos adicionales de artefacto y un protocolo de descubrimiento. Los agentes cargan contexto por etapas:

1. **Descubrimiento**: al arrancar, el harness lee su shim nativo en la raíz del proyecto (`CLAUDE.md`, etc.) que enlaza a `llm-session/BOOT.md`.
2. **Boot**: BOOT.md declara el orden de carga; el agente lee `MANIFEST.yaml` y el `INDEX.md` de cada subdirectorio.
3. **Activación**: cuando una tarea coincide con la descripción de una entrada del INDEX, el agente lee el cuerpo completo del archivo correspondiente.
4. **Persistencia**: al terminar la sesión, el agente escribe un snapshot bajo `state/` para que la próxima sesión retome desde ahí.

Los archivos INDEX citan la `description` del frontmatter de cada archivo que indexan — nunca una paráfrasis del cuerpo. Este es el contrato de carga perezosa que acota el uso de tokens sin sacrificar fidelidad.

## ¿Dónde puedo usar ACS?

Cualquier harness de agente que pueda leer archivos funciona. El estándar incluye plantillas de shim y convenciones de descubrimiento para:

* Claude Code (`CLAUDE.md`)
* Gemini CLI (`GEMINI.md`)
* Cursor (`.cursorrules`)
* Antigravity (`AGENTS.md`)
* Continue.dev (`.continue/system.md`)

Para cualquier otro harness, crea un shim raíz con el nombre de archivo que tu herramienta auto-descubra y enlázalo a `llm-session/BOOT.md`.

## Desarrollo abierto

El formato Agent Context Standard fue desarrollado originalmente por [CONFIANZA23](https://confianza23.com), publicado como estándar abierto, y está siendo adoptado por un número creciente de productos de agentes. El estándar está abierto a contribuciones del ecosistema en general.

ACS se construye directamente sobre el formato abierto [Agent Skills](https://agentskills.io) publicado por Anthropic — el directorio `skills/` dentro de cualquier proyecto ACS es, byte por byte, un directorio Agent Skills. ACS añade cinco directorios hermanos (`memory/`, `knowledge/`, `state/`, `specs/`, `decisions/`) y un protocolo de descubrimiento (`MANIFEST.yaml`, `BOOT.md`, `HARNESS.yaml`) para el contexto del proyecto que rodea a los skills.

Inspiraciones:

* **[Agent Skills](https://agentskills.io)** — formato de `skills/` que ACS reutiliza verbatim.
* **Michael Nygard, "Documenting Architecture Decisions" (2011)** — formato ADR de `decisions/`.
* **Karl Wiegers, *Software Requirements* (3ª ed.)** — disciplina Spec-Driven Development.
* **Convención Unix dotfile** (`.git`, `.vscode`) — nombre canónico `.agent/`.
* **[AGENTS.md](https://agents.md)** — convención multi-agente emergente adoptada como uno de los shims de ACS.

## Empezar

El camino más rápido es el skill bundleado del framework:

```bash
# Desde un proyecto ACS existente, copia el bundle del framework
cp -r skills.ACS_Framework_v1.0 /ruta/a/nuevo-proyecto/

# En el nuevo proyecto, ejecuta /init en tu harness, después di:
"load ACS"   # o  "carga ACS"
```

El skill bundleado `ACS.FRAMEWORK.agent-context-standard` detecta si el proyecto ya tiene ACS, crea el layout canónico si no, y reporta cumplimiento.

Para instalación manual, ver [Guía de adopción](#guía-de-adopción) abajo.

---

# Especificación

> La especificación completa del formato Agent Context Standard v1.0.

## Estructura de directorios

ACS define dos nombres canónicos aceptables; los harnesses deben buscar en la raíz del proyecto en este orden:

1. `.agent/` — nombre canónico preferido (convención Unix dot-dir).
2. `llm-session/` — alias visible.

`MANIFEST.yaml.directory.aliases` permite nombres adicionales específicos del proyecto.

### Subdirectorios

| Subdirectorio | Propósito | Obligatorio |
|---|---|---|
| `memory/` | Hechos de larga duración, preferencias del usuario, convenciones | Si tiene contenido |
| `knowledge/` | Dossiers de investigación, datos verificados, citas | Opcional |
| `state/` | Snapshots de sesión efímeros | Opcional |
| `skills/` | [Agent Skills](https://agentskills.io) reutilizables | Opcional |
| `specs/` | Artefactos de Spec-Driven Development | Opcional |
| `decisions/` | Architecture Decision Records | Opcional |

Todos los subdirectorios son opcionales. Subdirectorios desconocidos por el harness se ignoran silenciosamente (forward compatibility).

**Profundidad máxima**: 3 niveles bajo el directorio canónico.
**Excepción**: los internals de `skills/<name>/` siguen el formato [Agent Skills](https://agentskills.io/specification) y pueden usar cualquier layout interno (`references/`, `scripts/`, `assets/`).

## Formato de `MANIFEST.yaml`

El archivo `MANIFEST.yaml` en la raíz del directorio canónico declara la versión del schema, metadatos del proyecto y el orden de carga. Debe ser YAML válido.

### Campos obligatorios

| Campo | Restricciones |
|---|---|
| `acs_version` | Versión del schema. Actualmente `"1.0"`. |
| `kind` | Debe ser `agent-context`. |
| `project.name` | Nombre del proyecto. |
| `project.build` | Versión del proyecto (semver o build number). |
| `directory.canonical` | Uno de `llm-session` o `.agent`. |
| `boot.entry` | Documento de entrada, típicamente `BOOT.md`. |
| `boot.load_order` | Lista ordenada de subdirectorios a leer en el boot. |

### Campos opcionales

| Campo | Descripción |
|---|---|
| `project.description` | Máx 200 caracteres. Descripción del proyecto. |
| `project.language` | Idioma de contenido por defecto (`en`, `es`, ...). |
| `directory.aliases` | Nombres adicionales de directorio aceptados. |
| `agents.primary` | El harness para el que se autorizó este directorio. |
| `agents.compatible` | Lista de harnesses soportados. |
| `boot.required_indexes` | Archivos INDEX que DEBEN existir o el boot falla. |
| `boot.glob_reads` | Patrones glob leídos en el boot solo para info de cabecera. |
| `schemas` | Versiones del schema por artefacto. |
| `invariants` | Reglas vinculantes específicas del proyecto (lista libre). |
| `extensions` | Definidas por el proyecto; los harnesses DEBEN ignorar lo desconocido. |

### Ejemplo

```yaml
acs_version: "1.0"
kind: agent-context

project:
  name: mi-proyecto
  build: "1.0.0"
  description: "Descripción corta del proyecto, ≤200 chars"
  language: es

directory:
  canonical: llm-session
  aliases: [.agent]

agents:
  primary: claude-code
  compatible: [claude-code, gemini-cli, cursor, antigravity, continue-dev]

boot:
  entry: BOOT.md
  load_order: [memory, skills, specs, decisions, knowledge, state]
  required_indexes: [memory/INDEX.md, skills/INDEX.md]
```

## Formato de `BOOT.md`

`BOOT.md` es el punto de entrada universal. Aunque los harnesses auto-cargan su shim nativo, ese shim debe enlazar a `BOOT.md`.

### Secciones requeridas (en orden)

1. **Identidad** — nombre del proyecto, build, versión ACS
2. **Política de almacenamiento** — regla de fuente única, dos excepciones únicamente
3. **Orden de carga** — duplica `MANIFEST.yaml.boot.load_order` para legibilidad humana
4. **Invariantes** — reglas no-negociables del proyecto
5. **Dónde empezar** — pointer al `state/session_*.md` más reciente

### Ejemplo

```markdown
---
acs_version: "1.0"
kind: boot
project: mi-proyecto
build: "1.0.0"
updated: 2026-05-05
---

# BOOT — mi-proyecto · build 1.0.0

## Identidad
- Nombre, build, dominio del proyecto.

## Política de almacenamiento
Todo artefacto de sesión vive dentro del directorio canónico. Dos excepciones: shim de harness + config de harness.

## Orden de carga
1. MANIFEST.yaml
2. memory/INDEX.md + memorias listadas
3. ...

## Invariantes
- Reglas vinculantes del proyecto.

## Dónde empezar
Pointer al snapshot de estado más reciente.
```

## Formato de `HARNESS.yaml`

Archivo opcional que declara mapeos de shim por harness. Un proyecto multi-harness conforme (nivel L2+) incluye `HARNESS.yaml` más ≥2 entry shims en la raíz del proyecto.

### Campos

| Campo | Obligatorio | Descripción |
|---|---|---|
| `acs_version` | Sí | Versión del schema, `"1.0"` |
| `kind` | Sí | Debe ser `harness-shims` |
| `harnesses.<id>.entry_shim` | Sí | Path del archivo de entrada raíz del harness |
| `harnesses.<id>.skills_path` | No | Dónde este harness espera encontrar skills |
| `harnesses.<id>.skills_paths` | No | Lista de paths de descubrimiento de skills (para proyectos con varios bundles) |
| `harnesses.<id>.settings` | No | Path al archivo de configuración del harness |
| `harnesses.<id>.notes` | No | Notas libres |

### Ejemplo

```yaml
acs_version: "1.0"
kind: harness-shims

harnesses:
  claude-code:
    entry_shim: CLAUDE.md
    skills_path: skills
    settings: .claude/settings.local.json
  gemini-cli:
    entry_shim: GEMINI.md
    skills_path: skills
  cursor:
    entry_shim: .cursorrules
```

## Formato de `INDEX.md`

Cada subdirectorio poblado debe contener un `INDEX.md`. El INDEX es lo único que un agente nuevo lee para saber qué hay en ese subdirectorio.

```markdown
---
kind: index
subdir: <nombre>
acs_version: "1.0"
---

# <subdir-name> index — <project name>

- [<archivo-sin-ext>](<archivo>) — <descripción copiada verbatim del frontmatter, ≤150 chars>
- ...
```

### Restricciones

* Una entrada por archivo
* Descripción copiada **verbatim** del frontmatter `description` del archivo indexado — nunca parafraseada
* Cap duro: 200 líneas por `INDEX.md`

Este es el contrato de carga perezosa: el agente lee INDEX → frontmatter → cuerpo, deteniéndose al nivel que responde la pregunta.

## Cheat sheet de frontmatter

Cada archivo Markdown bajo el directorio canónico debe llevar un bloque YAML de frontmatter. Campos requeridos por tipo:

### `memory/<name>.md`

```yaml
---
name: <kebab-case>           # obligatorio, coincide con el nombre del archivo
description: <≤200 chars>    # obligatorio, usado para gating de relevancia
type: user|feedback|project|reference   # obligatorio
created: YYYY-MM-DD          # obligatorio
updated: YYYY-MM-DD          # obligatorio
---
```

### `knowledge/<name>.md`

```yaml
---
name: <kebab-case>
description: <≤200 chars>
asof: YYYY-MM-DD             # fecha en que el dato era vigente
sources: [url1, url2]        # citas verificables
---
```

### `skills/<name>/SKILL.md`

Formato [Agent Skills](https://agentskills.io/specification). Mínimo `name` y `description`. Extensiones ACS:

```yaml
---
name: <kebab-case>
description: <cuándo invocarlo; trigger phrasing>
version: 1                   # opcional; bump en cada cambio
status: active|superseded|deprecated   # opcional
superseded_by: <name>        # si está superseded
validation: <una línea>      # opcional; qué prueba que funciona
---
```

### `specs/<ID-slug>/SPEC.md`

```yaml
---
id: <ID>                     # obligatorio, inmutable
title: <legible humano>
status: draft|proposed|accepted|implemented|superseded|rejected
owner: <email o handle>
target_version: <semver>
created: YYYY-MM-DD
updated: YYYY-MM-DD
depends_on: [otros-spec-ids]
implements: [otros-spec-ids]
---
```

### `decisions/ADR-<NNN>-<slug>.md`

```yaml
---
id: ADR-NNN
title: <legible humano>
status: proposed|accepted|deprecated|superseded
date: YYYY-MM-DD
deciders: [name1, name2]
context_specs: [spec-ids]
---
```

## Carga progresiva (progressive disclosure)

Los agentes cargan contexto ACS progresivamente, trayendo más detalle solo cuando una tarea lo requiere:

1. **Manifest** (~50 tokens): `MANIFEST.yaml` se lee en el boot.
2. **Indexes** (≤ 200 líneas cada uno): un `INDEX.md` por subdirectorio en el boot, resumiendo solo metadatos.
3. **Frontmatter** (~10 líneas por archivo): cuando una entrada del INDEX parece relevante, el agente lee el frontmatter del archivo.
4. **Cuerpo** (contenido completo): solo cuando el frontmatter confirma relevancia.

Mantén archivos individuales enfocados. Caps recomendados:

* `INDEX.md` ≤ 200 líneas
* `SKILL.md` cuerpo ≤ 250 líneas (coincide con la recomendación de Agent Skills de < 500 líneas / < 5000 tokens)
* `SPEC.md` cuerpo ≤ 500 líneas
* Documentos más grandes deben dividirse en `NOTES.md` o sub-specs

## Referencias entre archivos

Para referenciar otros archivos, usa paths relativos desde la ubicación del archivo:

```markdown
Ver [la memoria de fuentes de datos](../memory/data-sources.md) para la regla.
Ejecuta el verificador en [`specs/ACS-001/TESTS.md`](../../specs/ACS-001-agent-context-standard/TESTS.md).
```

Mantén las referencias a un nivel de profundidad cuando sea posible. Evita cadenas de referencia profundamente anidadas.

## Convenciones de nomenclatura

| Patrón | Ejemplo | Uso |
|---|---|---|
| `MAYÚSCULAS.md` / `MAYÚSCULAS.yaml` | `BOOT.md`, `MANIFEST.yaml`, `SPEC.md` | Archivos estructurales reservados |
| `kebab-case.md` | `data-sources.md`, `lme-cu-cash.md` | Archivos de contenido |
| `<prefijo>-<NNN>-<slug>` | `ACS-001-agent-context-standard`, `ADR-007-vendor-lockin` | IDs de specs y ADRs |
| `session_<ISO-DATE>.md` | `session_2026-05-05.md` | Snapshots de estado fechados (ISO 8601) |
| `research_<ISO-DATE>.md` | `research_2026-05-04.md` | Dossiers de conocimiento fechados |

Prefijos de ID: `ACS-` (specs del propio estándar), `ADR-` (ADRs Nygard), `<PROJ>-<VER>-` (específicos del proyecto).

Los IDs son inmutables — nunca se reutilizan, ni siquiera tras eliminar el spec.

## Niveles de cumplimiento

ACS define cinco niveles incrementales de adopción. Un proyecto puede empezar en L0 y subir progresivamente.

| Nivel | Requisitos |
|---|---|
| **L0** | Directorio canónico + `MANIFEST.yaml` |
| **L1** | + `BOOT.md` + todos los `INDEX.md` requeridos |
| **L2** | + `HARNESS.yaml` + ≥2 entry shims en la raíz del proyecto |
| **L3** | + `specs/` con al menos un spec aceptado para el trabajo en curso |
| **L4** | + `decisions/` con ADRs para decisiones arquitectónicas no triviales |

## Guía de adopción

### Caso A — proyecto nuevo

```bash
mkdir -p llm-session/{knowledge,memory,state,skills,specs,decisions}
# Mete MANIFEST.yaml, BOOT.md, HARNESS.yaml desde templates/
# Añade CLAUDE.md (u otro shim de harness) en la raíz del proyecto
```

### Caso B — instalador bundleado (recomendado)

El camino más simple usa el bundle del framework:

```bash
cp -r skills.ACS_Framework_v1.0 /ruta/a/nuevo-proyecto/
cd /ruta/a/nuevo-proyecto
/init                    # en tu harness
# después di "load ACS" o "carga ACS"
```

El skill bundleado `ACS.FRAMEWORK.agent-context-standard` ejecuta el procedimiento de instalación de extremo a extremo.

### Caso C — migración desde otro layout

Un proyecto con dirs previos `claude-memory/`, `agent-state/` o similares migra así:

1. Crea el directorio canónico con `MANIFEST.yaml` declarando `aliases: [<nombre-viejo>]`.
2. Mueve el contenido existente al subdirectorio adecuado (memorias → `memory/`, dossiers → `knowledge/`, snapshots → `state/`, skills → `skills/`).
3. Añade `INDEX.md` a cada subdirectorio (uno por archivo, descripción del frontmatter verbatim).
4. Añade `BOOT.md` con la sección de orden de carga.
5. Actualiza el shim del harness (`CLAUDE.md`, etc.) para apuntar al nuevo `BOOT.md`.
6. Ejecuta el verificador de conformidad (sección siguiente). Debe pasar sin errores.

## Verificador de conformidad

Verificador bash de referencia:

```bash
# Archivos requeridos
test -f llm-session/MANIFEST.yaml || echo "MISSING MANIFEST.yaml"
test -f llm-session/BOOT.md       || echo "MISSING BOOT.md"

# Frontmatter en cada .md (internals de skill exentos)
find llm-session -name '*.md' \
  -not -path 'llm-session/skills/*/reference/*' \
  -not -path 'llm-session/skills/*/templates/*' \
  -not -path 'llm-session/skills/*/scripts/*' \
  | while read f; do
      head -1 "$f" | grep -q '^---$' || echo "MISSING FRONTMATTER: $f"
    done

# INDEX presente por cada subdir poblado
for sub in llm-session/*/; do
  [ "$(ls -A $sub)" ] && [ ! -f "$sub/INDEX.md" ] && echo "MISSING INDEX: $sub"
done

# Cap de tamaño de INDEX (≤ 200 líneas)
for f in $(find llm-session -name 'INDEX.md'); do
  n=$(wc -l < "$f")
  [ "$n" -gt 200 ] && echo "INDEX TOO LONG: $f ($n lines)"
done

# Profundidad máxima 3 (internals de skill exentos)
find llm-session -mindepth 4 -type f -not -path 'llm-session/skills/*' \
  | head -1 | grep . && echo "FILES TOO DEEP"
```

Sin output = cumplimiento al menos L1.

## Tooling de validación

Un instalador de referencia se incluye con este estándar: el skill `ACS.FRAMEWORK.agent-context-standard` (bundleado dentro de `skills.ACS_Framework_v<X>.<Y>/`). Detecta instalaciones existentes, crea el layout canónico desde plantillas, copia este README al proyecto para referencia humana y ejecuta el verificador anterior. Ver su `SKILL.md` para el procedimiento completo.

## Licencia

ACS-1.0 se publica bajo [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/) — úsalo, fórkalo, adáptalo libremente, atribuyendo el origen.

## Origen

Autoría de [CONFIANZA23](https://confianza23.com) durante el desarrollo de la plataforma STRATOS para Navantia (mayo de 2026). El primer proyecto conforme L4 es la propia plataforma STRATOS. ACS se mantiene ahora como estándar abierto y las contribuciones de cualquier parte son bienvenidas.

---

*Agent Context Standard v1.0 · 2026-05-05*
