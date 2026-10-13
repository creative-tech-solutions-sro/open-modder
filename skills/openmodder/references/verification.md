# Lab, capture, and verification: the running game is the oracle

Your reading of the code is a hypothesis; the running game decides. Build the harness once per game and
every later change is checkable in a minute.

## Lab setup (before the first modded launch)
- Back up the save folder and the config/profile folder:
  `um backup create "<saves folder>" --name <game>-saves`, or `Copy-Item -Recurse` / `cp -a` into
  `backups/<game>-<date>/`. The restore path goes in `MODLOG.md`.
- Use a separate lab profile, lab save directory or a copy of the world when the loader allows it
  (tModLoader `-tmlsavedirectory`, a mod-manager profile, a duplicated world). Scripted takes destroy test
  worlds — keep a pristine copy and restore it before every take.
- Windowed mode at a known client size (see below) so screenshots and click coordinates stay stable.
- Keep decompiled code and extracted assets outside the repo and gitignore derived data. Never commit
  game files.

## Make the window predictable
- **Windowed at a fixed client size**, found wherever the game hides it: an ini
  (`[/Script/Engine.GameUserSettings]` in UE, Unity's `Screenmanager` values under
  `HKCU\Software\<company>\<product>`), a launch flag (`-windowed -w 1920 -h 1080`, Unity
  `-screen-fullscreen 0 -screen-width 1920 -screen-height 1080`, Source `-sw -w 1920 -h 1080`), or the
  game's own registry keys. `um win drive --proc X "size 1920 1080"` also resizes most windowed games.
- **Skip intros** with launch flags (`-skipintro`, `-nosplash`, Unity `-popupwindow`) or by renaming intro
  videos in a *copy* of the game.
- **Jump straight in:** loader flags (`-skipselect Player:World`), a save made for testing, scenario
  auto-load, a dev console command.
- **Background throttling:** many games throttle when unfocused — Unity `Application.runInBackground`,
  Terraria `InactiveSleepTime`, UE idle-when-not-foreground. Find the setting or your test stalls.

## Launch, look, drive
```bash
um win setup                                 # once: capture + ffmpeg with GPU encoding
um win ps                                    # windowed processes: pid, name, title
um win launch --steam <appid>                # or: um win launch "C:\Games\Foo\Foo.exe" -- -windowed
um win shot --exe Game.exe shot.png --scale 0.33
um win drive --proc Game "focus" "click 640 360" "key 0x1B" "type hello" "hold 0x44 1500"
um win drive --proc Game idle                # seconds since the user last touched input
um win kill <pid>                            # exact PID only
um win record --exe Game take.mp4            # window + the game's own audio
```
Without `um`: PowerShell window/input APIs on Windows, `xdotool` + `grim`/ffmpeg `x11grab` on Linux,
`screencapture -l <windowid>` + `osascript`/`cliclick` on macOS (needs Accessibility + Screen Recording
permission).

Rules of the road:
- **Screenshots cost tokens.** Look at `--scale 0.33` copies, multiply coordinates back when clicking,
  and keep a table of measured menu click points in `MODLOG.md`.
- **GPU-rendered games come out black with naive capture.** Use Windows.Graphics Capture (what
  `um win shot` uses); it can't capture minimized windows. HDR Auto-HDR washes out captures — disable it
  for the game while recording.
- **Input goes only to the game, and only when it is foreground.** Never focus a game with an online mode
  while the user may be typing: their keystrokes land in it. Check `idle` first, ask them to click, and
  keep sessions short while they're at the PC — `references/safety.md`.
- Coordinates are the game window's **client area**.

## Better than clicking: a scripted scene or a bridge
- **Mod-side chat/console commands** give items, spawn enemies, teleport: tests become one line.
- **Test scenes:** a mod-side timeline (spawn at frame N, fire at N+30), a scenario with triggers, or a
  dedicated test world restored from backup before every take.
- **Agent bridge:** a JSON-lines socket inside the mod on `127.0.0.1` with commands like `observe`,
  `click <id>`, `controls`, `step`. Handle every request on the game's main thread after an update so
  replies are consistent; read menus from the UI tree so you see what a player sees; leave destructive
  buttons out. The in-game part stays thin — brains live outside the process.

## Verify
- Make the test repeatable, act, then **screenshot and read the log** — decide from both.
- Common log locations:

| Game / loader | Log |
|---|---|
| tModLoader | `client.log` |
| BepInEx | `BepInEx/LogOutput.log` |
| UE4SS | `UE4SS.log` |
| Unity | `Player.log` in `AppData/LocalLow/<company>/<product>/` |
| SKSE | `Documents/My Games/<game>/SKSE/` |
| Minecraft | `logs/latest.log` |

- **Circuit breaker:** the same failure three times → stop, write down what you know, then change the
  approach or ask the user.
- Read a screenshot at reduced scale *and actually look at it* before claiming success.

## Crashes and cleanup
- Crash reporters (BugSplat, `CrashReportClient.exe`, `UnityCrashHandler64.exe`) linger and make the store
  say "already running": spot them in the process list, kill them **by exact PID**.
- Never `pkill -f` or wildcard `taskkill /IM` — the pattern can match your own shell.
- After a crash: loader/game log first, then the OS event log (Windows Application log) for native crashes.
- Never block the game's main thread (for example by waiting on ffmpeg from inside a mod).
- Leave no topmost windows, global hooks or orphaned ffmpeg/PowerShell processes behind.
