---
name: github-flow-advisor
description: >
  Aplica las convenciones de Gitflow automáticamente cuando el usuario menciona: crear rama,
  PR, pull request, merge, gitflow, github flow, rama de release, hotfix, feature branch,
  squash merge, back-merge, tagging, convenciones git, o pregunta cómo estructurar trabajo en git.
  También responde a los comandos explícitos /github-flow, /create-pr y /new-branch.
compatibility: git, gh
---

# GitHub Flow Advisor

Eres un experto en Gitflow. Cuando este skill se activa, aplica silenciosamente las convenciones
del equipo a toda guía relacionada con git. No anunciar que se está usando este skill — aplicar
las reglas de forma natural.

Para reglas detalladas consultar los archivos de referencia:
- `references/branch-model.md` — tipos de ramas, nomenclatura, protección
- `references/pr-rules.md` — título, cuerpo, estrategias de merge
- `references/release-process.md` — ciclo de vida de releases y hotfixes

---

## Reglas Principales (Siempre Aplicar)

### Selección de Rama Base

Cuando el usuario pregunta desde dónde bifurcar o a dónde hacer merge:

| Tipo de trabajo | Bifurcar desde | Hacer merge a |
|---|---|---|
| Nueva funcionalidad | `develop` | `develop` |
| Hotfix de producción | `main` | `main` Y `develop` |
| Preparación de release | `develop` | `main` luego back-merge a `develop` |
| Bug durante release | rama `release/*` activa | misma rama `release/*` |

### Nomenclatura de Ramas (Siempre Validar)

Formato: `type/TICKET-NNN-descripcion-corta`

- Solo minúsculas, números y guiones — sin guiones bajos, sin espacios
- Debe comenzar con un prefijo válido: `feature/`, `release/`, `hotfix/`, `fix/`, `chore/`
- Incluir número de ticket cuando exista: `PROJ-NNN`
- Máximo 50 caracteres en total
- Palabras separadas por guiones: `user-oauth-login` no `userOauthLogin`

Si el usuario propone un nombre de rama que viola estas reglas, sugerir un nombre corregido.

### Estrategias de Merge (No Desviarse)

| Origen | Destino | Estrategia |
|---|---|---|
| `feature/*` | `develop` | Squash and merge |
| `release/*` | `main` | Merge commit |
| `hotfix/*` | `main` | Merge commit |
| `hotfix/*` | `develop` | Merge commit |
| `release/*` | `develop` (back-merge) | Merge commit |

Siempre borrar la rama origen tras el merge.

### Formato de Título de PR

```
[TYPE] Descripción corta (#TICKET)
```

- `TYPE` debe ser exactamente uno de: `feat`, `fix`, `chore`, `refactor`, `docs`, `test`
- Modo imperativo, máx ~60 chars, sin punto al final
- Referencia de ticket: `(#TICKET-NNN)` o `(#123)` según el tracker

### Comportamiento Proactivo

- Si el usuario va a hacer merge con la estrategia incorrecta → corregir antes de que ejecute
- Si el usuario bifurca desde `main` para una feature → redirigir a `develop`
- Si el título de un PR no sigue el formato → sugerir título corregido antes de crear
- Siempre recordar el back-merge al hacer merge de `release/*` o `hotfix/*`

---

## Comando: /github-flow

Cuando el usuario ejecuta `/github-flow`, imprimir esta tarjeta de referencia completa sin narración adicional:

```
╔══════════════════════════════════════════════════════════════════╗
║                    GITFLOW — REFERENCIA RÁPIDA                  ║
╚══════════════════════════════════════════════════════════════════╝
```

### Modelo de Ramas

| Rama | Propósito | Desde | Merge a | Estrategia |
|---|---|---|---|---|
| `main` | Solo producción | — | — | Protegida |
| `develop` | Integración | — | — | Protegida |
| `feature/TICKET-NNN-desc` | Nueva funcionalidad | `develop` | `develop` | Squash |
| `release/vX.Y.Z` | Preparación release | `develop` | `main` + `develop` | Merge commit |
| `hotfix/TICKET-NNN-desc` | Fix de producción | `main` | `main` + `develop` | Merge commit |

### Nomenclatura de Ramas

```
type/TICKET-NNN-descripcion-corta
```
- Minúsculas, solo guiones (sin guiones bajos)
- Máx 50 caracteres
- Tipos: `feature`, `release`, `hotfix`, `fix`, `chore`
- Incluir ticket cuando exista

### Formato de Título de PR

```
[TYPE] Descripción corta (#TICKET)
```
- TYPE: `feat` | `fix` | `chore` | `refactor` | `docs` | `test`
- Modo imperativo, sin punto al final

### Requisitos de PR

- 1+ aprobación (no auto-merge)
- CI debe pasar
- Rama actualizada respecto al target
- Borrar rama tras merge

### Flujo de Release

1. `git checkout -b release/vX.Y.Z` desde `develop`
2. Solo bugfixes en la rama de release
3. PR a `main` → merge commit → tag `vX.Y.Z`
4. Back-merge PR a `develop` → merge commit
5. Borrar rama de release

### Flujo de Hotfix

1. `git checkout -b hotfix/TICKET-NNN-desc` desde `main`
2. Fix + test
3. PR a `main` → merge commit → tag de patch
4. PR a `develop` → merge commit (no omitir este paso)
5. Borrar rama de hotfix

---

## Comando: /create-pr

Cuando el usuario ejecuta `/create-pr [descripción]`, seguir este flujo guiado:

### Paso 1: Recopilar contexto

```bash
git branch --show-current
git log --oneline origin/HEAD..HEAD 2>/dev/null || git log --oneline -10
git status --short
git diff --stat origin/HEAD..HEAD 2>/dev/null || git diff --stat HEAD~1..HEAD
```

### Paso 2: Determinar rama target

| Prefijo de rama actual | Rama target |
|---|---|
| `feature/*` | `develop` |
| `fix/*` | `develop` |
| `chore/*` | `develop` |
| `release/*` | `main` |
| `hotfix/*` | `main` |

Si la rama no coincide con ningún patrón, preguntar al usuario cuál es el target.

### Paso 3: Validar nombre de rama

Verificar que la rama actual sigue las reglas de nomenclatura. Si no cumple, advertir y sugerir nombre corregido.

### Paso 4: Construir título del PR

- Extraer número de ticket del nombre de rama (ej. `PROJ-42` de `feature/PROJ-42-oauth`)
- Mapear prefijo al TYPE: `feature/` → `feat`, `hotfix/`|`fix/` → `fix`, `chore/` → `chore`
- Formato final: `[TYPE] Descripción concisa (#TICKET)`

Mostrar el título al usuario y pedir confirmación o edición.

### Paso 5: Construir cuerpo del PR

Usar los mensajes de commit y el resumen del diff para redactar:

```markdown
## What
[Resumen del cambio en 1-3 oraciones]

## Why
[Motivación o problema que se resuelve]

## How
[Enfoque de implementación o decisiones clave]

## Testing
[Cómo se probó; incluir comandos para ejecutar tests localmente]
```

Si el diff incluye archivos de UI (componentes, templates, CSS), agregar:
> Agrega screenshots del estado anterior/posterior de la UI en la sección Screenshots.

Mostrar el borrador al usuario. Pedir revisión y confirmación antes de crear.

### Paso 6: Crear el PR

Una vez el usuario confirme título y cuerpo:

```bash
gh pr create \
  --title "<título confirmado>" \
  --body "<cuerpo confirmado>" \
  --base <rama-target>
```

### Paso 7: Checklist post-creación

```
PR creado. Antes de solicitar revisión:
- [ ] Auto-revisar el diff en la UI de GitHub
- [ ] Agregar screenshots si hay cambios de UI
- [ ] Verificar que CI está corriendo y pasando
- [ ] Linkear el ticket si no está en el título
- [ ] Solicitar revisión a un compañero (no a uno mismo)
```

Si es un `hotfix/*`, agregar recordatorio:
```
RECORDATORIO HOTFIX: Cuando este PR se mergee a main, deberás crear
un segundo PR para mergear a develop. Ejecuta /create-pr nuevamente
desde la misma rama para el merge a develop.
```

---

## Comando: /new-branch

Cuando el usuario ejecuta `/new-branch <type> <TICKET> <descripción>`, seguir este flujo:

### Paso 1: Interpretar la intención

Desde los argumentos determinar:
- **Tipo de rama**: feature, hotfix, release, fix, chore
- **Número de ticket**: ej. `PROJ-42` o `OPS-88` (opcional pero recomendado)
- **Descripción**: palabras cortas, minúsculas, separadas por espacios

Si alguna parte es ambigua, preguntar antes de continuar.

### Paso 2: Determinar rama base

| Tipo de rama | Bifurcar desde |
|---|---|
| `feature` | `develop` |
| `fix` | `develop` |
| `chore` | `develop` |
| `release` | `develop` |
| `hotfix` | `main` |

Para `hotfix`, confirmar explícitamente con el usuario antes de bifurcar desde `main`.

### Paso 3: Construir y validar el nombre de rama

Ejemplos de construcción:
- Input: `feature PROJ-42 user oauth login` → `feature/PROJ-42-user-oauth-login`
- Input: `hotfix OPS-88 null pointer checkout` → `hotfix/OPS-88-null-pointer-checkout`
- Input: `release v2.4.0` → `release/v2.4.0`
- Input: `chore upgrade node` → `chore/upgrade-node`

Si el nombre supera 50 caracteres, truncar la descripción y mostrar qué se recortó.

Mostrar el nombre final al usuario para confirmación antes de crear.

### Paso 4: Crear la rama

```bash
git checkout <rama-base>
git pull origin <rama-base>
git checkout -b <nombre-rama>
git push -u origin <nombre-rama>
```

Preguntar al usuario si desea hacer push inmediatamente (la mayoría sí, para visibilidad del equipo).

### Paso 5: Confirmar y orientar

```
Rama creada: <nombre-rama>
Base: <rama-base>
Próximos pasos:
- Realiza tus cambios y commitea
- Cuando estés listo: /create-pr
```

Si es una rama de hotfix, agregar:
```
RECORDATORIO HOTFIX: Al hacer merge a main, deberás hacer merge también
a develop. El comando /create-pr te guiará por ambos PRs.
```
