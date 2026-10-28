# fr33d0m

Fork custom de [Dopamine 3](https://github.com/opa334/Dopamine) con modificaciones específicas para el flujo de trabajo SISE/TONA de rcolinzarate.

## ⚠️ Esto NO es un jailbreak nuevo

fr33d0m **se construye encima de Dopamine 3** usando su exploit chain existente (Fugu17 / Fugu15). No es un jailbreak independiente desde cero — eso requeriría zero-days de kernel que ningún individuo puede desarrollar.

Lo que fr33d0m aporta son **customizaciones** sobre el jailbreak base.

## Scope

### Realista (planeado)

- [ ] Rebrand UI Dopamine → fr33d0m (colores, logo, strings)
- [ ] Auto-whitelist bundle IDs sideloaded (`com.rcolinzarate.*`) en trust cache
- [ ] iDownload daemon activado por default + listening on 1337 USB
- [ ] Boot logo custom con icono SISE
- [ ] Bootstrap pre-populado con tweaks custom:
  - SISEOS (wake word)
  - Choicy (exclude list)
  - OpenSSH (shell remota)
- [ ] Script post-install que fija `sileo.sources` para evitar el bug recurrente
- [ ] GitHub Actions CI con macOS runner + Theos + submódulos

### Fuera de alcance (técnicamente imposibles sin exploits adicionales)

- Bypass restricciones de `WKWebView` Web Speech API (código compilado en WebKit)
- Modificar `mediaserverd` para permitir mic always-on desde daemons custom
- Burlar TCC a nivel kernel desde apps sideloaded sin trust extendido

## Base

- **Upstream**: `opa334/Dopamine` branch `3.x` (commit base: a documentar en primer merge)
- **Target**: iOS 15.0 - 18.7.1 arm64 (incluye iPad7,5 iOS 17.7.10)
- **Build**: macOS 15 + Xcode 16 + Theos (GitHub Actions)

## Instalación (cuando esté lista)

1. Descargar `fr33d0m.ipa` del release
2. Sideloadly con Apple ID (como Dopamine normal)
3. Abrir fr33d0m app → tap "jailbreak"
4. Reinicia SpringBoard
5. Tus tweaks custom están pre-instalados

## Timeline realista

- **Fase 1** (semana 1): Fork + CI + build Dopamine sin cambios funcional
- **Fase 2** (semana 2): Rebrand UI + logo
- **Fase 3** (semanas 3-4): Auto-whitelist + iDownload default + bootstrap custom
- **Fase 4** (semana 5+): Testing + iteración

Total: 4-6 semanas de trabajo multi-sesión.

## Licencia

Hereda de Dopamine original (ver `LICENSE`). Modificaciones de rcolinzarate / Claude Opus 4.7.
