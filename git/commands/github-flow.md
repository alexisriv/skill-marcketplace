---
description: Muestra la tarjeta de referencia completa de las convenciones Gitflow
---

# GitHub Flow — Referencia Rápida

Imprimir la siguiente tarjeta sin narración adicional:

---

## Modelo de Ramas

| Rama | Propósito | Desde | Merge a | Estrategia |
|---|---|---|---|---|
| `main` | Solo producción | — | — | Protegida |
| `develop` | Integración | — | — | Protegida |
| `feature/TICKET-NNN-desc` | Nueva funcionalidad | `develop` | `develop` | Squash |
| `release/vX.Y.Z` | Preparación release | `develop` | `main` + `develop` | Merge commit |
| `hotfix/TICKET-NNN-desc` | Fix de producción | `main` | `main` + `develop` | Merge commit |
| `fix/TICKET-NNN-desc` | Bugfix no urgente | `develop` | `develop` | Squash |
| `chore/descripcion` | Mantenimiento | `develop` | `develop` | Squash |

## Nomenclatura de Ramas

```
type/TICKET-NNN-descripcion-corta
```
- Minúsculas, solo guiones (sin guiones bajos)
- Máx 50 caracteres
- Tipos: `feature`, `release`, `hotfix`, `fix`, `chore`
- Incluir ticket cuando exista

## Formato de Título de PR

```
[TYPE] Descripción corta (#TICKET)
```
- TYPE: `feat` | `fix` | `chore` | `refactor` | `docs` | `test`
- Modo imperativo, sin punto al final

## Requisitos de PR

- 1+ aprobación (no auto-merge)
- CI debe pasar
- Rama actualizada respecto al target
- Borrar rama tras merge

## Estrategias de Merge

| Origen | Destino | Estrategia |
|---|---|---|
| `feature/*` | `develop` | Squash and merge |
| `release/*` | `main` | Merge commit |
| `hotfix/*` | `main` | Merge commit |
| `hotfix/*` | `develop` | Merge commit |
| `release/*` | `develop` (back-merge) | Merge commit |

## Flujo de Release

1. `git checkout -b release/vX.Y.Z` desde `develop`
2. Solo bugfixes en la rama de release
3. PR a `main` → merge commit → tag `vX.Y.Z`
4. Back-merge PR a `develop` → merge commit
5. Borrar rama de release

## Flujo de Hotfix

1. `git checkout -b hotfix/TICKET-NNN-desc` desde `main`
2. Fix + test
3. PR a `main` → merge commit → tag de patch
4. PR a `develop` → merge commit (no omitir este paso)
5. Borrar rama de hotfix
