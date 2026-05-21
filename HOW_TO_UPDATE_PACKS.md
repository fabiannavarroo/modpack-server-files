# Como actualizar modpacks y servidores

Este repositorio contiene los manifests y archivos que descarga la app `launcher`.

La app no descarga carpetas completas por intuicion: solo descarga lo que aparezca en los manifests JSON. Por eso, cada vez que cambies mods, config, shaders, resource packs u overrides, tienes que subir los archivos y actualizar el manifest correspondiente.

## Estructura

```txt
manifest.json
servers/
  server1/
    manifest.json
files/
  server1/
    mods/
    config/
    shaderpacks/
    resourcepacks/
    datapacks/
    defaultconfigs/
    options.txt
    emi.json
```

`manifest.json` es el manifest principal. Define que servidores existen y cual es el recomendado.

`servers/<serverId>/manifest.json` define los archivos exactos de ese servidor.

`files/<serverId>/...` contiene los archivos reales que la app descarga.

## Actualizar el server actual

Servidor actual:

```txt
server1 = Create - Perfect World
```

Para actualizarlo:

1. Actualiza tu instancia en CurseForge.
2. Copia los archivos reales al repo:
   - `mods/` -> `files/server1/mods/`
   - `config/` -> `files/server1/config/`
   - `shaderpacks/` -> `files/server1/shaderpacks/`
   - `resourcepacks/` -> `files/server1/resourcepacks/`
   - `datapacks/` -> `files/server1/datapacks/`
   - `defaultconfigs/` -> `files/server1/defaultconfigs/`
   - archivos raiz utiles como `options.txt` o `emi.json` -> `files/server1/`
3. No subas cosas personales o de runtime:
   - `saves/`
   - `backups/`
   - `logs/`
   - `screenshots/`
   - `.cache/`
   - `.mixin.out/`
   - `crash-reports/`
   - mapas/caches de Xaero
   - archivos `.bak`, `.disabled`, `.preedit.bak`
4. Regenera `servers/server1/manifest.json`.
5. Sube cambios a GitHub.
6. Cambia `packVersion` para que la app muestre que hay version nueva.

## Campos importantes del manifest de servidor

Cada archivo necesita:

```json
{
  "path": "mods/example.jar",
  "url": "https://raw.githubusercontent.com/fabiannavarroo/modpack-server-files/refs/heads/main/files/server1/mods/example.jar",
  "sha256": "...",
  "size": 123456,
  "category": "mods",
  "required": true,
  "deleteIfRemoved": true
}
```

Campos:

- `path`: ruta donde quedara dentro de `.minecraft`.
- `url`: URL directa HTTPS al archivo en GitHub raw.
- `sha256`: hash real del archivo.
- `size`: tamano real en bytes.
- `category`: una de estas:
  - `mods`
  - `config`
  - `shaders`
  - `resourcepacks`
  - `overrides`
- `required`: `true` si es obligatorio.
- `deleteIfRemoved`: `true` si la app puede eliminarlo cuando desaparezca del manifest.

## Categorias recomendadas

```txt
mods/*.jar                  -> category: mods
config/**                  -> category: config
shaderpacks/**             -> category: shaders
resourcepacks/**           -> category: resourcepacks
datapacks/**               -> category: overrides
defaultconfigs/**          -> category: overrides
options.txt                -> category: overrides
emi.json                   -> category: overrides
```

## Cambiar version del pack

Cuando actualices mods o configs, sube la version:

```json
"packVersion": "1.0.1"
```

Hazlo en dos sitios:

1. `manifest.json`, dentro del servidor.
2. `servers/server1/manifest.json`.

La app usa esa version para mostrar al usuario que hay cambios.

## Cambiar version de NeoForge

Si cambias NeoForge:

```json
"loaderVersion": "21.1.230",
"expectedVersionId": "neoforge-21.1.230",
"installerUrl": "https://maven.neoforged.net/releases/net/neoforged/neoforge/21.1.230/neoforge-21.1.230-installer.jar"
```

La app verifica esta carpeta:

```txt
.minecraft/versions/neoforge-21.1.230/
```

Si no existe, mostrara `NeoForge requerido`.

## Anadir otro servidor

Para crear otro servidor:

1. Crea:

```txt
servers/server2/manifest.json
files/server2/
```

2. Sube sus archivos reales a `files/server2/`.
3. Genera `servers/server2/manifest.json`.
4. Anade el servidor al `manifest.json` principal:

```json
{
  "id": "server2",
  "name": "Nombre del servidor",
  "description": "Descripcion corta.",
  "channel": "stable",
  "minecraftVersion": "1.21.1",
  "loader": "neoforge",
  "loaderVersion": "21.1.230",
  "expectedVersionId": "neoforge-21.1.230",
  "packVersion": "1.0.0",
  "manifestUrl": "https://raw.githubusercontent.com/fabiannavarroo/modpack-server-files/refs/heads/main/servers/server2/manifest.json",
  "modCount": 150,
  "approximateSize": 433609031,
  "installerUrl": "https://maven.neoforged.net/releases/net/neoforged/neoforge/21.1.230/neoforge-21.1.230-installer.jar"
}
```

5. Si quieres que sea el recomendado, cambia:

```json
"activeServerId": "server2"
```

## Comandos utiles

Calcular SHA-256 de un archivo:

```bash
shasum -a 256 files/server1/mods/example.jar
```

Ver tamano en bytes:

```bash
stat -f%z files/server1/mods/example.jar
```

Subir cambios:

```bash
git add .
git commit -m "Update server1 pack"
git push
```

## Limites de GitHub

GitHub acepta archivos hasta 100 MB.

Recomendacion:

- Menos de 50 MB: bien.
- 50-100 MB: GitHub avisa, pero lo acepta.
- Mas de 100 MB: GitHub bloquea el push.

Si un mod supera 100 MB, usa Releases, R2, S3 u otro hosting y pon esa URL en el manifest.

## Que hace la app al actualizar

La app:

1. Lee `manifest.json`.
2. Lee `servers/<serverId>/manifest.json`.
3. Descarga archivos que falten.
4. Reemplaza archivos cuyo SHA-256 no coincida.
5. Valida SHA-256 despues de descargar.
6. Guarda una instancia local en:

```txt
.minecraft/t-launcher-instances/<serverId>/
```

Al pulsar `Jugar`, la app:

1. Actualiza la instancia local.
2. Limpia solo archivos gestionados anteriormente.
3. Copia el servidor seleccionado a `.minecraft`.
4. Verifica NeoForge.
5. Abre Minecraft Launcher oficial si todo esta correcto.

## Checklist antes de publicar

- `packVersion` actualizado.
- `manifest.json` principal actualizado.
- `servers/<serverId>/manifest.json` actualizado.
- Todos los archivos tienen SHA-256 correcto.
- Ningun archivo supera 100 MB.
- No hay `saves`, `backups`, `logs`, `screenshots`, caches ni archivos personales.
- La URL raw del manifest abre en navegador.
