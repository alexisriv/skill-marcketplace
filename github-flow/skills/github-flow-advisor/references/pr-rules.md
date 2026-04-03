# Reglas de Pull Request

## Formato de Título

```
[TYPE] Descripción corta (#TICKET)
```

- `TYPE` debe ser exactamente uno de: `feat`, `fix`, `chore`, `refactor`, `docs`, `test`
- Descripción: modo imperativo, máx ~60 chars, sin punto al final
- Referencia de ticket: `(#TICKET-NNN)` o `(#123)` según el tracker del equipo

### Ejemplos Válidos

- `[feat] Add OAuth login with Google (#PROJ-42)`
- `[fix] Prevent null pointer in checkout flow (#OPS-88)`
- `[chore] Upgrade Node.js to v22 (#INFRA-7)`
- `[refactor] Extract payment service from order module`
- `[docs] Add API authentication guide (#PROJ-55)`
- `[test] Add unit tests for cart calculation logic`

### Ejemplos Inválidos

| Título | Problema |
|---|---|
| `Fix bug` | Sin TYPE, sin ticket |
| `[FEAT] fixed the login thing.` | TYPE en mayúscula, pasado verbal, punto final |
| `[feat] Added support for multiple OAuth providers including Google GitHub and Microsoft` | Demasiado largo |
| `WIP: working on auth` | Sin formato, sin TYPE |
| `feature/PROJ-42` | Es un nombre de rama, no un título de PR |

## Secciones Obligatorias del Cuerpo

### Mínimo requerido

```markdown
## What
Descripción breve del cambio y su alcance.

## Why
El problema que se resuelve o el valor que se agrega.

## How
Decisiones clave de implementación o el enfoque utilizado.

## Testing
Cómo se probó. Incluir comandos para ejecutar tests localmente.
```

### Requerido para cambios de UI

Agregar sección `## Screenshots` con capturas antes/después.

### Requerido cuando aplique

- `## Breaking Changes` — si este PR cambia una interfaz pública o contrato de API
- `## Migration` — si los consumidores necesitan actualizar algo
- `## Ticket` — link al tracker: `Closes #TICKET-NNN`

## Reglas de Merge

| Regla | Detalle |
|---|---|
| Aprobaciones | Mínimo 1 aprobación de un miembro del equipo (no el autor) |
| Auto-merge | Nunca permitido, ni siquiera para owners |
| CI | Todos los checks requeridos deben pasar antes del merge |
| Actualización | La rama debe estar actualizada respecto al target antes del merge |
| Borrar rama | Siempre borrar la rama origen tras el merge |

## Estrategia de Merge por Tipo de Rama

| Origen | Target | Estrategia | Justificación |
|---|---|---|---|
| `feature/*` | `develop` | Squash and merge | Historial limpio y lineal en develop; los commits de feature son ruido |
| `release/*` | `main` | Merge commit | Preservar historial del release; permite `git log --first-parent` |
| `hotfix/*` | `main` | Merge commit | Preservar historial del hotfix en main |
| `hotfix/*` | `develop` | Merge commit | Preservar contexto de qué se arregló |
| `release/*` | `develop` (back-merge) | Merge commit | Preservar cambios del release; evitar perder fixes de la rama |

## Checklist Antes de Solicitar Revisión

- [ ] El título sigue el formato `[TYPE] descripción (#TICKET)`
- [ ] La rama está actualizada respecto al target (`git pull origin <target>`)
- [ ] Todos los checks de CI pasan localmente
- [ ] El cuerpo del PR tiene las secciones What/Why/How/Testing completadas
- [ ] Screenshots agregados para cambios de UI
- [ ] Auto-revisión completada (leer el propio diff)
- [ ] Sin código de debug, console.logs ni TODOs olvidados
- [ ] Ticket linkeado en el cuerpo o en el título
- [ ] Revisor asignado (no a uno mismo)
