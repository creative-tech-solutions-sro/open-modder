# Engine playbooks: pick the family, then the route

`um scan "<game>"` names the family and the playbook. Without it, use `references/recon.md` step 1 to
identify the payload, then read the matching section here. Every family below is a decision, not a
tutorial: confirm against the real install and the community's current docs.

## Index

| Family | Detect by | Code | First route to try |
|---|---|---|---|
| Unity | `UnityPlayer.dll`, `GameAssembly.dll`, `globalgamemanagers`, `Data/` | managed (Mono) or IL2CPP | BepInEx/MelonLoader/Harmony; data via UABEA/AssetRipper |
| Unreal | `_Paks`, `CrashReportClient`, `UE4SS`, `Engine/Binaries` | native C++ + Blueprints | UE4SS (Lua), pak overrides via FModel/UAssetGUI |
| .NET / XNA | `MonoBleedingEdge`, `Assembly-CSharp.dll`, XNA/FNA libs | managed .NET | loader API if one exists (tModLoader, SMAPI), else Harmony/MonoMod |
| Godot | `project.godot`, `.godot/`, PCK files | GDScript/C# | GDRE Tools (extract), GDExtension or a loader |
| Source 1/2 | `vphysics.dll`, `gameinfo.txt`, `bin/engine2.dll` | native + data | Workshop/game tools, VPK/Crowbar, source-format mods |
| Bethesda (Skyrim/Fallout) | `Data/*.esp|esm|esl`, Creation Engine | data + script extender | xEdit/Creation Kit + SKSE/F4SE plugin |
| Minecraft | `versions/`, `libraries/`, `logs/latest.log` | Java | Fabric/NeoForge (or Forge) mod, Loom `genSources` |
| AoE2 / Genie | `resources/`, `.dat` data tables, `.sld` sprites | data | genieutils + AoE2ScenarioParser, official mod uploader |
| Big frameworks (RE Engine, FromSoft, GTA/Cyberpunk/BG3) | per-game markers, `dinput8.dll`/`version.dll` proxy culture | native C++ | the community loader (REFramework, UE4SS, ScriptHookV, mod engine) + data tools |
| Indie/other engines (GameMaker, RPG Maker, Ren'Py, Paradox, Doom, HTML5, LÖVE, Java) | engine-specific files (`yy`, `RGSSAD`, `librenpy`, `common/pdx`, WAD, `*.love`) | mixed | the community editor/tool for that engine, or data edits |
| Retro / decompilations | console formats, ELF/NRO/XCI dumps the user owns | native | decomp/recomp toolchains, texture/mesh extractors |
| Gothic 1/2 (Daedalus/Union) | `GothicI/II`, `*.DAT` scripts, Union, `GD3D11` | data + script | Daedalus script edits, Union, GD3D11 |

Full per-family playbooks (exact loaders, file layouts, gotchas) live in the source project's
`skills/mod-any-game/references/engines/`. Pull one up when you know the family; verify versions against
the community before installing anything.

## Route order (cheapest that reaches the idea)
1. **Data / assets only** — the idea fits existing data files: content tables, plugins, scripts, pak
   overrides. Lowest risk: no injected code, easy to uninstall.
2. **Loader API** — if the community already ships a loader, use it. It solves injection, hot reload,
   logging and distribution for you, and its API tells you what is even possible.
3. **Managed patching** — Harmony/MonoMod prefixes, postfixes and transpilers for .NET/Mono/IL2CPP;
   Mixins for Java. Choose it when there is no API but the code is managed.
4. **Native hooks** — proxy DLL (`dinput8`, `version`, `winmm`, `winhttp`, `dxgi`) + a hooking library
   (MinHook/SafetyHook) and signature scans, for a C/C++ engine with no loader.
5. **Reimplement / decomp / recomp** — total control, or retro consoles; heavy, and the "bring your own
   game files" rule applies to whatever you read.
6. **Mashup / passthrough** — two games at once: content port, shared socket bridge, guest engine hosted
   in the host, or a headless guest sim with the host as the view.

Write the chosen route and the reason into `MODLOG.md` before building.

## Signals that change the plan
- **Anti-cheat present** → no injection route at all. Data-only, official surfaces, offline mode.
- **IL2CPP** → the code is native; you need dumpers first (see `reverse-engineering.md`).
- **Signed/packed EXE, launcher-only installs, always-online** → budget extra recon before promising a route.
- **A loader already installed** → start from its version and example mod, not from memory.
- **Game version newer than every tool** → pin the game version, or wait; don't guess at offsets.
- **Save format encrypted/opaque** → treat saves as read-only until you have proven a round trip.
