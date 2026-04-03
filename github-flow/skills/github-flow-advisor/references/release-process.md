# Proceso de Release

## Release Estándar

### Paso 1: Cortar la rama de release

```bash
git checkout develop
git pull origin develop
git checkout -b release/vX.Y.Z
git push -u origin release/vX.Y.Z
```

### Paso 2: Estabilizar

- Solo bugfixes están permitidos en la rama de release
- Sin nuevas features — esas van a develop para el próximo release
- Cada fix debe ser un commit separado con mensaje claro

### Paso 3: Merge a main

- Abrir un PR: `release/vX.Y.Z` → `main`
- Título del PR: `[chore] Release vX.Y.Z`
- Usar **merge commit** (no squash)
- Tras el merge, taggear `main`:

```bash
git checkout main
git pull origin main
git tag -a vX.Y.Z -m "Release vX.Y.Z"
git push origin vX.Y.Z
```

### Paso 4: Back-merge a develop

- Abrir un PR: `release/vX.Y.Z` → `develop`
- Usar **merge commit**
- Este paso es obligatorio — omitirlo causa que develop diverja silenciosamente de main

### Paso 5: Borrar la rama de release

```bash
git push origin --delete release/vX.Y.Z
```

---

## Proceso de Hotfix

Se usa cuando `main` (producción) tiene un bug crítico que no puede esperar al próximo ciclo de release.

### Paso 1: Bifurcar desde main

```bash
git checkout main
git pull origin main
git checkout -b hotfix/TICKET-NNN-descripcion-corta
git push -u origin hotfix/TICKET-NNN-descripcion-corta
```

### Paso 2: Arreglar y testear

- Mantener el fix mínimo y enfocado
- Agregar un test que cubra la regresión si es posible

### Paso 3: Merge a main

- Abrir un PR: `hotfix/TICKET-NNN-*` → `main`
- Título del PR: `[fix] Descripción corta del fix (#TICKET-NNN)`
- Usar **merge commit**
- Tras el merge, taggear con incremento de patch:

```bash
git checkout main
git pull origin main
git tag -a vX.Y.Z -m "Hotfix vX.Y.Z: descripción breve"
git push origin vX.Y.Z
```

### Paso 4: Merge a develop (crítico, frecuentemente olvidado)

- Abrir un PR: `hotfix/TICKET-NNN-*` → `develop`
- Usar **merge commit**
- Si hay una rama de release activa en ese momento, hacer merge del hotfix a la rama de release en lugar de (o además de) develop

### Paso 5: Borrar la rama de hotfix

```bash
git push origin --delete hotfix/TICKET-NNN-descripcion-corta
```

---

## Versionado (Semver)

| Tipo de cambio | Incremento | Ejemplo |
|---|---|---|
| Cambio breaking de API | MAJOR | `1.2.3` → `2.0.0` |
| Nueva feature, compatible hacia atrás | MINOR | `1.2.3` → `1.3.0` |
| Bugfix | PATCH | `1.2.3` → `1.2.4` |
| Hotfix | PATCH | `1.2.3` → `1.2.4` |

---

## Errores Comunes a Evitar

| Error | Consecuencia | Solución |
|---|---|---|
| Bifurcar un hotfix desde `develop` en lugar de `main` | El fix no llega correctamente a producción | Siempre `git checkout main` antes de crear hotfix |
| Olvidar el back-merge de `release/*` o `hotfix/*` a `develop` | Divergencia silenciosa entre main y develop | El back-merge es obligatorio, no opcional |
| Agregar nuevas features a una rama de release | Scope no revisado llega a producción | Solo bugfixes en ramas de release |
| Usar squash merge para `release/*` → `main` | Se destruye el historial del release | Siempre merge commit para releases y hotfixes |
| No taggear tras merge a main | Sin referencia clara de qué versión está en producción | Siempre taggear inmediatamente tras merge a main |
