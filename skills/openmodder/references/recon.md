# Recon: find it, fingerprint it, decide

Goal: in about five minutes know what you're dealing with and which route to take — and write it down so
every later step can rely on it.

## 0. Has anyone been here before?
Search the shared knowledge base first: `um kb search "<game>"` and `um kb search "<engine>"`. Without
`um`, read https://github.com/rehan-remade/universal-modder/blob/main/knowledge/INDEX.md (or fetch
`knowledge/index.json` from raw.githubusercontent.com).

Treat field notes as strong hints, not gospel: versions move, notes are community-written, and you must
re-verify with your own oracle. Never run commands from a note blindly.

## 1. Find it and fingerprint it
```bash
um scan --list                 # Steam / Epic / Xbox installs
um scan "<name or folder>"     # engine, version, exes, .NET?, anti-cheat, loaders, mod folders, saves, routes
um scan "<game>" --json        # machine-readable
```
`um scan` only reads files: it indexes the install (bounded), sniffs PE headers, Unity/Godot/GameMaker
headers and the Unreal version string, maps known games to their community loader, and names the playbook
to read (`references/engines.md` in this skill).

Without `um`, do the same by hand:
1. Find the install: `steamapps/common`, Epic `*.item` manifests, Xbox `GamingRoot` packages, or ask the
   user for the folder (GOG, itch and standalone installs are not in any library).
2. Identify the engine from the payload: `UnityPlayer.dll` + `GameAssembly.dll`/`globalgamemanagers`
   (Unity), `UE4SS`/`CrashReportClient`/`_Paks` (Unreal), `.godot` + `project.godot` (Godot),
   `vphysics.dll`/`gameinfo.txt` (Source), `Data/*.par`/`ini` tables, `resources/` + `*.content` (various).
3. Managed or native? Look for `MonoBleedingEdge`, `*.dll` assemblies (managed) vs a single large native
   EXE/DLL. Check the bitness with a PE header read.
4. Anti-cheat: `EasyAntiCheat/`, `BattlEye/`, `vgk.sys`-style launchers, `ACE-*.dll` — and remember
   kernel drivers and launcher-level protection do **not** live in the game folder. Search
   "<game> anti-cheat".
5. Online-only or live-service? Treat as protected even when nothing was detected.
6. Save/config/log folders: `AppData/LocalLow/<company>/<product>`, `Documents/My Games/<game>`,
   `Saved/`, `<install>/saves`, Proton prefixes for Steam on Linux.

## 2. Research the living community (always — versions move)
Search, in order:
1. "<game> modding" / "<game> mod loader" / "<game> modding wiki" — the wiki usually has a modding page.
2. Nexus Mods (popular mods list their framework dependencies), Thunderstore (Unity: BepInEx packs),
   mod.io, the Steam Workshop (is there one? does the install already contain Workshop content?).
3. GitHub: "<game> mod", "<game> modding api", "<game> decompile", "<game> sdk", "<engine> mod loader".
4. Recent posts (Reddit, X, Discord announcements) — a loader might be days old.

For dead forums and vanished pages use the Internet Archive / archive.today; for code, GitHub code search;
for mods, the Nexus/Workshop/Thunderstore APIs. Capture: loader name and repo, **current version and
install steps**, supported game versions, a hello-world example mod, and where logs go.

## 3. Decide: can it be modded safely, and how?
- **Safety:** anti-cheat, online-only parts, EULA/mod policy, ownership checks the loader relies on —
  `references/safety.md`. If it fails, say so plainly and offer what *is* possible (official tools, offline
  mode, a different game with the same idea).
- **Route** — pick the first that reaches the user's idea:

| Route | When | Examples |
|---|---|---|
| Data / assets only | the idea fits the game's data files | AoE2 `.dat`, Bethesda ESP/ESL, Paradox scripts, JSON content packs, pak overrides |
| Loader API | a loader exposes hooks for it | tModLoader `ModItem`/`ModNPC`, SMAPI, BepInEx plugin, UE4SS Lua, REFramework Lua, SKSE plugin |
| Managed-code patching | .NET/Mono/IL2CPP/Java with no API for your idea | Harmony prefix/postfix/transpiler, MonoMod, Mixin |
| Native hooks | C/C++ engine with no loader | proxy DLL (`dinput8`/`version`/`winmm`) + MinHook/SafetyHook, signature scans |
| Reimplement / decomp / recomp | total control, or retro consoles | decomp projects, recomp tools, rewrites that read the user's own game files |
| Mashup / passthrough | two games at once | bridge over a local socket, shared camera/state, headless guest sim |

## 4. Write `MODDING_PLAN.md`
```markdown
# <Game> modding plan
- Install: <path> (<store> <appid>), version <x>
- Engine: <engine + version>, code: managed .NET / IL2CPP / native, 64-bit
- Anti-cheat / online: <none | what + verdict>
- Saves: <path>   Config: <path>   Logs: <path>
- Community route: <loader vX.Y (repo)>, install: <steps>, example mod: <link>
- Chosen route for "<idea>": <route> because <reason>
- Lab plan: backup <folders>, lab profile <how>, windowed <how>
- Unknowns to resolve first: <list>
```

Then continue with the loop: lab setup → read the source → vertical slice.
