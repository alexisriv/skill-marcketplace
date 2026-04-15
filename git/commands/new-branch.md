---
description: Crea una rama git con nomenclatura Gitflow validada
argument-hint: <type> <TICKET-NNN> <descripción>
allowed-tools: Bash(git checkout:*), Bash(git branch:*), Bash(git pull:*), Bash(git push:*)
---

# Nueva Rama (Gitflow)

Cuando el usuario ejecuta `/new-branch <type> <TICKET> <descripción>`, seguir este flujo:

## Paso 1: Interpretar la intención

Desde los argumentos determinar:
- **Tipo de rama**: feature, hotfix, release, fix, chore
- **Número de ticket**: ej. `PROJ-42` o `OPS-88` (opcional pero recomendado)
- **Descripción**: palabras cortas, minúsculas, separadas por espacios

Si alguna parte es ambigua, preguntar antes de continuar.

## Paso 2: Determinar rama base

| Tipo de rama | Bifurcar desde |
|---|---|
| `feature` | `develop` |
| `fix` | `develop` |
| `chore` | `develop` |
| `release` | `develop` |
| `hotfix` | `main` |

Para `hotfix`, confirmar explícitamente con el usuario antes de bifurcar desde `main`.

## Paso 3: Construir y validar el nombre de rama

Reglas:
- Formato: `type/TICKET-NNN-descripcion-corta`
- Solo minúsculas, números y guiones — sin guiones bajos ni espacios
- Máximo 50 caracteres en total

Ejemplos de construcción:
- Input: `feature PROJ-42 user oauth login` → `feature/PROJ-42-user-oauth-login`
- Input: `hotfix OPS-88 null pointer checkout` → `hotfix/OPS-88-null-pointer-checkout`
- Input: `release v2.4.0` → `release/v2.4.0`
- Input: `chore upgrade node` → `chore/upgrade-node`

Si el nombre supera 50 caracteres, truncar la descripción y mostrar qué se recortó.

Mostrar el nombre final al usuario para confirmación antes de crear.

## Paso 4: Crear la rama

```bash
git checkout <rama-base>
git pull origin <rama-base>
git checkout -b <nombre-rama>
git push -u origin <nombre-rama>
```

Preguntar al usuario si desea hacer push inmediatamente (la mayoría sí, para visibilidad del equipo).

## Paso 5: Confirmar y orientar

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
