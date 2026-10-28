# Patches de fr33d0m sobre Dopamine upstream

Cada archivo `.patch` aquí es un `git diff` que se aplica sobre `upstream/` durante el CI build.

## Orden de aplicación

Los patches se aplican alfabéticamente (`00_rebrand.patch` antes de `10_trust_cache.patch`).

## Pendientes (Fase 1-4)

- `00_rebrand.patch` — Dopamine → fr33d0m en Application/*.strings + Info.plist (nombre, colores)
- `10_auto_trustcache.patch` — Hook en launchdhook para auto-add cdhash de apps `com.rcolinzarate.*`
- `20_idownload_default.patch` — Flip `Disabled: true` → `false` en com.opa334.Dopamine.idownloadd.plist
- `30_bootstrap_tweaks.patch` — Agregar SISEOS/Choicy/OpenSSH debs al bootstrap tar
- `40_bootlogo_sise.patch` — Reemplazar bootlogo.jp2 default por icono SISE
