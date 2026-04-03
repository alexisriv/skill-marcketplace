# github-flow

Plugin de Claude Code que aplica las convenciones de Gitflow/GitHub Flow automáticamente.

## Qué hace

- **Se activa solo** cuando hablas de ramas, PRs, merges, releases o hotfixes
- Aplica silenciosamente las reglas de nomenclatura, formato de PR y estrategia de merge
- Provee tres comandos para las operaciones más comunes

## Skill

### Invocada por el modelo: `github-flow-advisor`

Se activa automáticamente cuando surgen temas de flujo git. Aplica validación de nomenclatura de ramas, guía de estrategia de merge, verificación de formato de PR y recordatorios del proceso de release/hotfix — sin que tengas que invocar nada.

## Comandos

| Comando | Descripción |
|---|---|
| `/github-flow` | Imprime la tarjeta de referencia completa de todas las reglas |
| `/create-pr` | Creación guiada de PR: valida rama, redacta título y cuerpo, ejecuta `gh pr create` |
| `/new-branch` | Crea una rama con nomenclatura validada: tipo, ticket, descripción |

## Modelo de Ramas (Referencia Rápida)

| Rama | Desde | Merge a | Estrategia |
|---|---|---|---|
| `feature/TICKET-NNN-desc` | `develop` | `develop` | Squash |
| `release/vX.Y.Z` | `develop` | `main` + `develop` | Merge commit |
| `hotfix/TICKET-NNN-desc` | `main` | `main` + `develop` | Merge commit |
| `fix/TICKET-NNN-desc` | `develop` | `develop` | Squash |
| `chore/descripcion` | `develop` | `develop` | Squash |

## Requisitos

- `git` instalado y repositorio inicializado
- `gh` (GitHub CLI) instalado y autenticado — requerido para `/create-pr`

# Instalación

Ejecutar estos dos comandos en Claude Code desde cualquier proyecto:

```
/plugin marketplace add https://github.com/alexisriv/skill-marcketplace.git
/plugin install github-flow@skill-marcketplace
```
