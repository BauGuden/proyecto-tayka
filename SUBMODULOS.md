# Repositorios y submódulos de Tayka

El repositorio principal `https://github.com/BauGuden/proyecto-tayka` guarda la configuración y el commit de cada submódulo. Cada repositorio conserva su historial, sus ramas y sus dependencias.

## Estructura actual

```text
proyecto-tayka/
├── backend-tayka/     # https://github.com/BauGuden/backend-tayka (NestJS)
├── frontend-tayka/    # https://github.com/BauGuden/frontend-tayka (Next.js)
├── .gitmodules       # URLs, rutas y ramas de los submódulos
├── README.md
└── SUBMODULOS.md
```

Se usan `backend-tayka/` y `frontend-tayka/`, según los comandos de incorporación. No hay un submódulo contracts todavía.

## Clonar todo el proyecto

```bash
git clone --recurse-submodules https://github.com/BauGuden/proyecto-tayka.git
cd proyecto-tayka
```

Si ya clonaste el repositorio principal:

```bash
git submodule update --init --recursive
```

Después de traer cambios del repositorio principal:

```bash
git pull
git submodule sync --recursive
git submodule update --init --recursive
```

Estos comandos recuperan los commits registrados por el repositorio principal. Es normal que los submódulos queden en `detached HEAD`; cambia a una rama antes de desarrollar.

## Cómo se agregaron backend y frontend

Desde la raíz del repositorio principal:

```bash
git submodule add -b main https://github.com/BauGuden/backend-tayka.git backend-tayka
git submodule add -b main https://github.com/BauGuden/frontend-tayka.git frontend-tayka
```

Si la carpeta ya contiene un clon del repositorio correcto, Git puede reutilizarlo. Revisa primero su URL, rama y cambios pendientes. Para guardar sus metadatos Git dentro del repositorio principal:

```bash
git submodule absorbgitdirs
```

## Agregar otro repositorio

1. Crea o identifica el repositorio remoto. Debe tener al menos un commit y la rama que vas a usar.
2. Elige una carpeta relativa a la raíz que no esté ocupada por archivos del repositorio principal.
3. Ejecuta el siguiente comando reemplazando URL, rama y carpeta:

```bash
git submodule add -b main https://github.com/ORGANIZACION/REPOSITORIO.git carpeta
```

4. Comprueba la configuración y el commit incorporado:

```bash
git diff --cached -- .gitmodules
git submodule status
git status
```

5. Publica el registro desde el repositorio principal:

```bash
git add .gitmodules carpeta
git commit -m "Agrega nuevo repositorio como submodulo"
git push
```

Para contracts, **solo cuando esté listo y se decida incorporarlo**, el comando sería:

```bash
git submodule add -b main https://github.com/BauGuden/contracts-tayka.git contracts
```

Este comando no se ha ejecutado. Un repositorio Rust no necesita incorporarse a un workspace JavaScript.

## Trabajar dentro de un submódulo

Ejemplo con backend, partiendo de un árbol de trabajo limpio:

```bash
cd backend-tayka
git switch main
git pull --ff-only
# Realiza y revisa tus cambios.
git add ruta/del/archivo
git commit -m "Describe el cambio del backend"
git push origin main
cd ..
git add backend-tayka
git commit -m "Actualiza referencia del backend"
git push
```

Publica primero el commit del submódulo para que otras personas puedan descargarlo. Si trabajas con ramas y PR, publica el cambio por ese flujo y luego registra en el padre el commit que corresponda. Repite el proceso con `frontend-tayka` para el frontend.

## Actualizar un submódulo a su rama configurada

Con los cambios locales guardados, para incorporar la versión actual de `main` del backend:

```bash
git submodule update --init --remote backend-tayka
git diff --submodule
# Verifica que el proyecto funcione con la nueva versión.
git add backend-tayka
git commit -m "Actualiza backend a main"
git push
```

`branch = main` indica qué rama consulta `--remote`; el padre siempre fija un commit concreto. Un `git pull` del padre no incorpora automáticamente el último commit de cada submódulo.

## Dependencias y control central

Actualmente ambos proyectos tienen `pnpm-lock.yaml`, y el frontend declara pnpm en `packageManager`. Agregar submódulos no centraliza dependencias ni genera `package.json`, `yarn.lock` o `.yarnrc.yml` en la raíz. Se conserva la configuración actual de cada proyecto.

Si se adopta Yarn con dependencias centralizadas más adelante, habrá que configurar un `package.json` raíz privado con los workspaces `backend-tayka` y `frontend-tayka`, elegir y fijar una versión de Yarn, configurar `.yarnrc.yml`, adaptar los repositorios y sus flujos de instalación, y ejecutar la instalación para generar un `yarn.lock` real. Esa migración debe coordinarse con el uso independiente de cada repositorio.

## Guardar la incorporación inicial

Los comandos de alta preparan `.gitmodules` y las referencias de los submódulos en el índice. Para publicar también esta documentación, revisa y ejecuta desde la raíz:

```bash
git add .gitmodules backend-tayka frontend-tayka README.md SUBMODULOS.md
git diff --cached --submodule
git commit -m "Integra backend y frontend como submodulos"
git push
```
