# modpack-server-files

Public manifest repository consumed by the `launcher` desktop app.

Replace placeholder SHA-256 values before publishing real packs.

## Layout

```txt
manifest.json
servers/
  server1/manifest.json
  server2/manifest.json
  server3/manifest.json
files/
  server1/
    mods/
    config/
    shaderpacks/
    resourcepacks/
    overrides/
```

All file URLs must use HTTPS and every file in a server manifest must include its real SHA-256.
