# Package, publish, and leave a field note

## 1. Lint first
```bash
um publish check ./MyMod --game "<game install folder>"
```
Without `um`, walk the same checklist:

- **Must not ship:** files byte-identical to game files; extracted assets; decompiled source; leaked keys
  (any API token, `.env` files); retail offsets or decompiler names baked into shipped code.
- **Warns:** decompiler fingerprints in source (`FUN_`, `DAT_`, `sub_` names, "Decompiled with" headers),
  large engine archives, absolute user paths, a missing README, generated assets without credit.
- **Should ship:** your code, your own assets, patches/converters/installers that transform the user's
  own files at install time.

Fix every must-not; resolve each warning deliberately (a data mod that ships a modified `.dat` may simply
be that platform's norm).

## 2. Package the way the platform expects

| Platform / loader | Package |
|---|---|
| tModLoader | build → `.tmod`, publish from the in-game Mod Sources menu |
| BepInEx / Thunderstore | zip with `manifest.json`, `icon.png` (256×256), `README.md`, `plugins/<Mod>.dll` |
| Nexus Mods | zip laid out as it installs (`Data/...`, `BepInEx/plugins/...`, `ue4ss/Mods/...`, `~mods/*.pak`) |
| Steam Workshop | the game's own uploader or SDK tool |
| Minecraft | Modrinth / CurseForge jar with its mod manifest |
| ROM hacks / GameMaker | patches only (BPS/IPS/xdelta), never the modified game file |

## 3. README (in the archive and on the page)
- What it adds — bullets, a GIF, the showcase clip.
- Requirements: game version, loader and its version, dependencies.
- Install, uninstall, troubleshooting (where the log is).
- Compatibility: multiplayer? known conflicts?
- Credits: the loader and libraries, what you learned from, **which models generated the art/audio**, and
  an honest note that AI tooling built it.
- A licence for your code; the assets follow their models' licences.

## 4. Version, changelog, post
- Semver in the manifest/build file, name the supported game version, keep a changelog. When the game
  updates, re-run the in-game test scene before bumping.
- The post leads with the video: 20–45 s, gameplay within the first 2–3 s, one-line titles, a fade-out.
- **Publishing is always the user's call.** Draft it, show them, let them press the button.

## 5. The field note (do this even for a dead end)
Your journal (`MODLOG.md`) is the raw material: versions, paths, IDs and symbols, formats, every failure
with its cause once you know it, and what you verified and how. Turn it into a short note covering:

1. **Setup** — exact versions: game build, loader, SDK, OS. "Latest" helps nobody.
2. **Route** — which one you took and why the cheaper ones didn't reach the idea.
3. **How the engine really works** — the symbols and the logic in your own words. Never paste decompiled
   code.
4. **Verification** — the oracle you used, and what you did *not* verify.
5. **Gotchas** — numbered, symptom → cause → fix. The most valuable part of any note.
6. **Status** — honest. `in-progress` and `abandoned` notes are welcome; a dead end is knowledge.

Rules for anything you publish as a note: no game files or extracted assets, no decompiled dumps, no
leaked code/SDKs/keys (what you learned from them, in your own words, is fine), nothing that helps cheat
other players or bypass protections. Ask the human before any public PR — it is public and uses their
account. If a note for the same game and idea already exists, extend it instead of writing a second one.
