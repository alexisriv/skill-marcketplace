# Modelo de Ramas

## Ramas Protegidas

| Rama | Propósito | Quién hace merge |
|---|---|---|
| `main` | Solo código listo para producción. Cada commit aquí es desplegable. | Solo PRs de release o hotfix |
| `develop` | Rama de integración. Todas las features completadas llegan aquí primero. | PRs de feature vía squash |

## Ramas de Trabajo

### Feature Branches
- **Base**: `develop`
- **Nomenclatura**: `feature/TICKET-NNN-descripcion-corta`
- **Merge target**: `develop` (squash and merge)
- **Tiempo de vida**: Corto. Abrir PR cuando esté listo, borrar tras merge.
- **Ejemplo**: `feature/PROJ-42-user-oauth-login`

### Release Branches
- **Base**: `develop`
- **Nomenclatura**: `release/vMAJOR.MINOR.PATCH` (semver)
- **Merge target**: `main` (merge commit + tag), luego back-merge a `develop`
- **Tiempo de vida**: Solo durante la estabilización del release
- **Trabajo permitido**: Solo bugfixes — sin nuevas features
- **Ejemplo**: `release/v2.4.0`

### Hotfix Branches
- **Base**: `main`
- **Nomenclatura**: `hotfix/TICKET-NNN-descripcion`
- **Merge target**: `main` (merge commit + tag) Y `develop` (merge commit)
- **Tiempo de vida**: Lo más corto posible — producción está rota
- **Ejemplo**: `hotfix/OPS-88-null-pointer-checkout`

### Fix / Chore Branches
- **Base**: `develop`
- **Nomenclatura**: `fix/TICKET-NNN-descripcion` o `chore/descripcion`
- **Merge target**: `develop` (squash and merge)
- **Uso**: Bugfixes no urgentes, tareas de mantenimiento, actualizaciones de dependencias

## Reglas de Nomenclatura (Obligatorias)

1. Solo letras minúsculas, números y guiones — sin guiones bajos, sin espacios
2. Debe comenzar con un prefijo de tipo válido: `feature/`, `release/`, `hotfix/`, `fix/`, `chore/`
3. Incluir número de ticket cuando exista: `PROJ-NNN`
4. Máximo 50 caracteres en total (incluyendo el prefijo de tipo)
5. Palabras de la descripción separadas por guiones: `user-oauth-login` no `userOauthLogin`

## Regex de Validación

```
^(feature|release|hotfix|fix|chore)\/([A-Z]+-[0-9]+-)?[a-z0-9][a-z0-9-]{0,44}$
```

## Ejemplos Válidos vs Inválidos

| Nombre | Válido | Problema |
|---|---|---|
| `feature/PROJ-42-user-oauth-login` | Sí | — |
| `hotfix/OPS-88-null-pointer-checkout` | Sí | — |
| `release/v2.4.0` | Sí | — |
| `chore/upgrade-node-v22` | Sí | — |
| `Feature/user-login` | No | Prefijo en mayúscula |
| `feature/user_oauth_login` | No | Guiones bajos |
| `feature/userOAuthLogin` | No | camelCase |
| `fix-login-bug` | No | Sin prefijo de tipo |
| `feature/PROJ-42-implementacion-completa-del-sistema-de-autenticacion-oauth` | No | Supera 50 chars |
