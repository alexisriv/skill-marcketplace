---
description: Crea un Pull Request guiado siguiendo las convenciones de Gitflow
argument-hint: [descripción]
allowed-tools: Bash(git branch:*), Bash(git log:*), Bash(git status:*), Bash(git diff:*), Bash(gh pr create:*), Bash(gh pr view:*)
---

# Crear PR (Gitflow)

Cuando el usuario ejecuta `/create-pr [descripción]`, seguir este flujo guiado:

## Paso 1: Recopilar contexto

```bash
git branch --show-current
git log --oneline origin/HEAD..HEAD 2>/dev/null || git log --oneline -10
git status --short
git diff --stat origin/HEAD..HEAD 2>/dev/null || git diff --stat HEAD~1..HEAD
```

## Paso 2: Determinar rama target

| Prefijo de rama actual | Rama target |
|---|---|
| `feature/*` | `develop` |
| `fix/*` | `develop` |
| `chore/*` | `develop` |
| `release/*` | `main` |
| `hotfix/*` | `main` |

Si la rama no coincide con ningún patrón, preguntar al usuario cuál es el target.

## Paso 3: Validar nombre de rama

Verificar que la rama actual sigue las reglas de nomenclatura:
- Formato: `type/TICKET-NNN-descripcion-corta`
- Solo minúsculas, números y guiones
- Prefijo válido: `feature/`, `release/`, `hotfix/`, `fix/`, `chore/`
- Máximo 50 caracteres

Si no cumple, advertir y sugerir nombre corregido.

## Paso 4: Construir título del PR

- Extraer número de ticket del nombre de rama (ej. `PROJ-42` de `feature/PROJ-42-oauth`)
- Mapear prefijo al TYPE: `feature/` → `feat`, `hotfix/`|`fix/` → `fix`, `chore/` → `chore`
- Formato final: `[TYPE] Descripción concisa (#TICKET)`

Mostrar el título al usuario y pedir confirmación o edición.

## Paso 5: Construir cuerpo del PR

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

## Paso 6: Crear el PR

Una vez el usuario confirme título y cuerpo:

```bash
gh pr create \
  --title "<título confirmado>" \
  --body "<cuerpo confirmado>" \
  --base <rama-target>
```

## Paso 7: Checklist post-creación

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
