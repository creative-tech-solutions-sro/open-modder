# Safety, legality, etiquette

These rules keep the user's accounts, saves and machine safe, and keep the mod shareable. Not legal advice;
when a game's EULA or mod policy matters, read it (search "<publisher> mod policy").

## Online games and anti-cheat: the bright line
- **Only single-player/offline, or servers the user runs.** Injecting into an online client breaks its
  terms and gets accounts banned. Doing it to gain an advantage over other players is cheating. Refuse
  aimbots, ESP/wallhacks, speed or teleport hacks, recoil scripts and packet manipulation for multiplayer
  games, whatever the framing.
- **Kernel or user-mode anti-cheat is a stop sign for code injection:** EasyAntiCheat, BattlEye, Vanguard,
  EA Javelin, Ricochet, ACE, nProtect, XIGNCODE, mhyprot, VAC on official servers.
  Allowed instead:
  - official modding surfaces — Workshop, creative/map editors, official mod kits;
  - an official offline mode that ships without the protection (the community norm for FromSoft games is
    launching offline through the official offline option, with co-op mods as their own separate thing).
- **Never:** disable or bypass anti-cheat, spoof hardware IDs, tamper with DRM (Denuvo, Steam stub) or
  ownership checks. If a loader needs the game's EXE patched past a protection, stop and tell the user.
- VAC bans modified clients on VAC-secured servers: local tests only, `-insecure`, nothing injected when
  joining public servers.
- Tools running *next to* a protected game (debuggers, Cheat Engine, injecting overlays) can trip its
  anti-cheat even if you never touch it. Close protected games before a reverse-engineering session.

## Ownership and redistribution
- Mod games the user owns, from their own install or dumps of cartridges and discs they own. Never
  download ROMs, ISOs or game files.
- **Never publish:** game files or byte-identical copies; extracted assets; decompiled source; retail
  offsets or decompiler names baked into shipped code.
- **Do publish:** your own code and your own assets; patches, diffs, converters or installers that
  transform the user's files at install time ("bring your own game files").
- Credit loaders, libraries and references, disclose AI use honestly, and check the licence of any
  generated asset's model before commercial use. Communities react badly to undisclosed AI releases, and
  some ban them outright.
- Takedowns happen even without shipped assets: reverse-engineered GTA III/VC code was taken down and litigated, and
  an ActiMod project got a cease-and-desist the day before launch. Commercial use, monetisation and leaked
  material raise the risk sharply. Knowledge learned from betas and leaks is fine to use in your own words;
  never ship leaked code or builds, or a mod that only runs with them.

## The user's machine
- **Back up first.** Saves, profiles, config — before any modded launch. Restoring must be one command,
  written in `MODLOG.md`.
- **Loaders and proxy DLLs** (`winhttp.dll`, `version.dll`, `dinput8.dll`, `dxgi.dll`) sit in the game
  folder: tell the user what you added and how to remove it. Better, use a separate copy of the game or a
  mod-manager profile.
- **Registry and config changes:** back up the key first, note what changed, offer the undo.
- **Input automation takes over the user's mouse and keyboard.**
  - Check idle time first (seconds since they last touched input); if they are active, ask.
  - Ask before long unattended runs; keep sessions short while they are at the PC.
  - Never focus a game that has an online mode while the user may be typing — their keystrokes land in the
    game. Check idle, ask them to click, and launch with the online mode unreachable.
- **Processes:** kill by exact PID. `pkill -f <pattern>` from an agent shell kills the agent's own shell;
  wildcard `taskkill /IM` matches other apps. Don't leave topmost windows, global hooks or orphaned
  ffmpeg/PowerShell processes behind.
- **Networking:** bind any bridge, debugger or MCP server you add to `127.0.0.1` with a token. Some RE
  tools bind `0.0.0.0` by default.
- **Scripts from strangers:** a viral mod's "download" is often malware. Only run loaders from their
  official repositories and releases.

## When to stop and ask
- The game has online components with anti-cheat, and the idea touches them.
- The only route left is bypassing a protection.
- A step would delete or overwrite saves or game files without a backup.
- Anything public: publishing, uploading, opening a PR — always the user's call. Draft it, show them, let
  them press the button.
