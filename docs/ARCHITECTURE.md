# fr33d0m — Arquitectura

## Capas

```
┌─────────────────────────────────────────────────┐
│  fr33d0m.ipa (lo que instala el user)            │
│                                                  │
│  ┌──── Dopamine upstream (sin tocar) ────────┐  │
│  │  - Fugu17 exploit chain                   │  │
│  │  - BaseBin (systemhook, ellekit, idownloadd│  │
│  │  - rootless bootstrap tarball            │  │
│  └──────────────────────────────────────────┘  │
│                                                  │
│  ┌──── Patches fr33d0m (patches/*.patch) ────┐  │
│  │  - Rebrand UI (strings + colores)         │  │
│  │  - Auto-whitelist bundle IDs              │  │
│  │  - iDownload por default                  │  │
│  │  - Bootstrap custom con tweaks pre-included│  │
│  └──────────────────────────────────────────┘  │
└─────────────────────────────────────────────────┘
```

## Flujo de build (GitHub Actions)

1. `checkout` recursivo (incluye submódulo `upstream/`)
2. Instalar Theos + SDKs iOS
3. Para cada archivo `patches/*.patch` (orden alfabético): `git apply` sobre `upstream/`
4. `cd upstream && make package` (invoca el build real de Dopamine)
5. Upload `.ipa` como artifact

## Fases

### Fase 1 — "Build de Dopamine funcional vía CI"

**Objetivo**: lograr que GitHub Actions produzca una fr33d0m.ipa **idéntica a Dopamine 3.0.12** (sin cambios aún). Esto valida que tenemos el entorno de build operativo.

Pasos:
- Clonar upstream vía submodule ✅ (hecho)
- Setup Theos en CI ✅ (yaml creado)
- Primer build sin modificaciones (pendiente — Dopamine tiene build custom no trivial)

### Fase 2 — "Rebrand visual"

Modificar strings localizados en `upstream/Application/*.lproj/*.strings`:
- "Dopamine" → "fr33d0m"
- Colores UI (asset catalog): paleta nueva
- Icono app: SVG custom

Patch 1: `00_rebrand.patch`

### Fase 3 — "Auto-whitelist y iDownload"

Hook en `upstream/BaseBin/launchdhook/` para que al cargar un proceso cuyo bundle ID matchea `com.rcolinzarate.*`, su cdhash se agregue automáticamente al trust cache de jbd.

Patch: `10_auto_trustcache.patch`

Modificar `upstream/BaseBin/_external/basebin/LaunchDaemons/com.opa334.Dopamine.idownloadd.plist`:
- `<Disabled>true</Disabled>` → `<Disabled>false</Disabled>`

Patch: `20_idownload_default.patch`

### Fase 4 — "Bootstrap con tweaks pre-included"

El bootstrap de Dopamine (`upstream/Application/bootstrap_*.tar.zst`) es el tarball que se extrae en `/var/jb/` al jailbreakear. Modificarlo para incluir:
- `/var/jb/usr/lib/TweakInject/SISEOS.dylib` + `.plist`
- `/var/jb/usr/bin/openssh*` 
- `/var/jb/Applications/Choicy.app/` (si lo logramos compilar rootless)

Patch script (no `.patch` sino `.sh`): `30_bootstrap_tweaks.sh`

## Lo que NO vamos a tocar (de upstream)

- `upstream/BaseBin/Fugu15*/` — exploit chain, muy riesgoso
- `upstream/BaseBin/libjailbreak.dylib` — core API, otros tweaks dependen de ella
- `upstream/BaseBin/systemhook.dylib` — inyector base, modificarlo rompe todo

## Testing

Build local (requiere Mac):
```bash
cd upstream
make package
# produce Application/build/*.ipa
```

Build CI (GitHub Actions):
```
git push origin main
# Actions runs .github/workflows/build.yml automático
```

Deploy al iPad:
- sideloadly (como Dopamine normal)
- O ideviceinstaller vía USB

## Riesgos conocidos

- **Build de Dopamine es complejo**: tiene múltiples submódulos (ChOma, XPF, Fugu15, kfd) que requieren setups específicos. CI puede necesitar varias iteraciones para funcionar.
- **Trust cache patching**: si el hook en launchdhook está mal, puede impedir boot de SpringBoard (ladrillo del jailbreak hasta reinstalar).
- **Modificar bootstrap**: la firma del tarball es checada por Dopamine al jailbreakear. Hay que firmarlo nosotros tras modificar o deshabilitar el check.
