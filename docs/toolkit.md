# Toolkit: the optional `um` CLI and its replacements

OpenModder does not require any external tool. The commands referenced in the skill come from the
companion CLI of the project this skill is derived from; install it once if the user wants the faster
path, and the skill's `um ...` lines become available.

```bash
uv tool install git+https://creative-tech-solutions-sro/open-modder   # or: pipx install ...
um --help                                                            # every group has --help with examples
```

Requirements for the full toolkit: Git, Python 3.10+, `uv` (recommended), ffmpeg for capture, Blender for
3D → sprite renders. A fal API key (`FAL_KEY`) enables image/audio/3D/video generation.

## Command groups

| Command | Does |
|---|---|
| `um scan` | find Steam/Epic/Xbox installs; engine, version, managed vs native, anti-cheat, loaders, save folders, ranked routes |
| `um fal` | `sprite`, `image`, `edit`, `rmbg`, `pixelate`, `upscale`, `texture`, `pbr`, `model3d`, `rig`, `sfx`, `music`, `voice`, `video`, `run`, `search`, `schema`, `price` — REST, with a manifest of every generation |
| `um comfy` | `status`, `image` (`--sprite` cuts it out), `run` — local ComfyUI, no API key |
| `um sprite` | `cutout`, `fit`, `pixelate`, `palette`, `sheet`, `slice`, `frames`, `team-mask`, `seamless`, `preview` |
| `um render3d` | GLB → sprite frames from the game's camera (`aoe2`, `iso8`, `trueiso`, `topdown`, `side`, `turntable`) via Blender |
| `um win` | `shot`, `record` (window + game-only audio), `drive` (input that only reaches the game), `ps`, `kill`, `launch`, `reg` |
| `um video` | `contact` sheets, `compile` (EDL → titled, beat-cut video), `mux`, `beats`, `first-frame` |
| `um backup` | snapshot, diff and restore save/config folders |
| `um publish check` | blocks shipping game files, decompiled code and leaked keys |
| `um kb` | knowledge base: `search`, `show`, `new`, `check`, `index`, `sync`, `pr` |

Two no-build Windows helpers ship with the toolkit (PowerShell with embedded C#): window input driving and
game-only loopback audio capture.

## Without the toolkit

| Task | Replacement |
|---|---|
| Find and fingerprint games | store library folders + engine file markers (`references/recon.md`) |
| Prior art | upstream knowledge-base index on GitHub (`knowledge/INDEX.md`) |
| Generation | fal MCP (`https://mcp.fal.ai/mcp` + `Authorization: Bearer $FAL_KEY`), `fal api`, local ComfyUI, or any model the user has |
| Sprite conversion | ImageMagick / Pillow: flood-fill cutout, nearest-neighbour fit, palette, sheet packing |
| 3D → sprites | Blender headless (`blender -b model.glb -P render.py`) with the game's camera preset |
| Launch / screenshot / drive | PowerShell window + input APIs, `xdotool`/`grim` (Linux), `osascript`/`screencapture` (macOS) |
| Record | ffmpeg window capture with the game's audio (WASAPI loopback on Windows) |
| Back up | `Copy-Item -Recurse` / `cp -a` into a dated folder |
| Lint | the checklist in `references/publish-and-notes.md` |
| Knowledge base | read upstream `knowledge/INDEX.md` / `knowledge/index.json` |

## Secrets

`FAL_KEY` and any other token come from the environment or a local `.env`. Never write a key into a prompt,
a mod file, a report, a log, a commit or a screenshot; the lint step fails on leaked keys.
