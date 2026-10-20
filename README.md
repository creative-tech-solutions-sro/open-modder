# OpenModder

**An OpenCode skill for modding PC games the user owns — from idea to working in the real game, recorded,
with a field note for the next run.**

OpenModder gives the agent a repeatable method instead of improvising: search prior art, fingerprint the
install, pick the cheapest route that reaches the idea, back up first, read the actual code, ship one
vertical slice, verify against the running game, then package and write down what was learned.

<img width="1365" height="768" alt="openmodder_banner" src="https://github.com/user-attachments/assets/001b565c-ee04-4750-817e-1550f55a1415" />


```text
@openmodder  Mod Terraria: add a homing missile launcher and a tactical nuke that craters the world.
@openmodder  What engine is C:\Games\Foo, and has anyone modded it before?
@openmodder  Put GTA V into Minecraft, with a with all GTA NPCs and functions on the Minecraft map.
```

## The loop

1. **Intake** — game, store, idea in one sentence, what "done" means; settle online vs offline up front;
   start `MODLOG.md`.
2. **Recon** — knowledge base first, then fingerprint engine/version/code type/anti-cheat/loaders/saves,
   then the living community → `MODDING_PLAN.md`.
3. **Route** — data/assets → loader API → managed patching → native hooks → reimplementation → mashup.
   Record the route *and the reason*.
4. **Lab** — backup saves and config, lab profile, windowed at a known size, decomp outside the repo.
5. **Source of truth** — decompile, dump data, inspect live; record exact names and IDs.
6. **Vertical slice** — one item all the way through with placeholder art, proven in game before widening.
7. **Assets** — study the game's own assets, generate, convert to the exact engine format.
8. **Verify** — the running game is the oracle: repeatable test, screenshot, log. Three identical failures
   → stop and change approach.
9. **Showcase** — scripted take, recorded window with the game's own audio, 20–45 s cut.
10. **Package** — lint, platform-correct archive, honest README.
11. **Field note** — versions, route, engine facts, verification, numbered gotchas (even for dead ends).

## Safety

The skill refuses by construction, not by mood:

- only games the user owns; single-player, offline, or servers they control;
- never touches an anti-cheat-protected online client, never bypasses anti-cheat, DRM or ownership
  checks, never writes multiplayer cheats;
- never ships game files, extracted assets or decompiled source — code, own assets and patches only;
- backs up saves before the first modded launch, kills processes by exact PID;
- asks before driving input for long sessions, installing loaders, changing settings, or publishing.

Reasoning lives in [`skills/openmodder/references/safety.md`](skills/openmodder/references/safety.md).

## Install

Quick options (full detail in [`docs/install.md`](docs/install.md)):

```powershell
# global — available in every project
Copy-Item -Recurse skills\openmodder "$env:USERPROFILE\.config\opencode\skills\openmodder"
```

```powershell
# project — this repo, or any project that should carry it
Copy-Item -Recurse skills\openmodder .opencode\skills\openmodder
```

Then load it with `@openmodder` in a prompt, or let the description trigger it. Verify the ID is
`openmodder` (path-derived), not the display name.

## Repository layout

```text
openmodder/
├── README.md                     ← this file
├── CHANGELOG.md
├── LICENSE
├── docs/
│   ├── install.md                ← every install method, verification, updates, removal
│   ├── workflow.md               ← the loop in detail and the artifacts it produces
│   └── toolkit.md                ← the optional `um` CLI and what replaces it
└── skills/
    └── openmodder/
        ├── SKILL.md              ← the skill: loop, hard rules, toolkit table
        └── references/
            ├── recon.md              find, fingerprint, community research, routes, MODDING_PLAN.md
            ├── engines.md            playbook index by engine family
            ├── reverse-engineering.md decompilers, dumps, live memory, format round trips
            ├── verification.md       lab, windowed capture, driving, logs, crashes
            ├── assets.md             art, 3D and audio: generate, then convert
            ├── publish-and-notes.md  lint, packaging, README, the field note
            └── safety.md             the bright lines, with reasons
```

`SKILL.md` stays small on purpose: it loads into context, and the reference files are read only when the
matching step comes up.

## Toolkit (optional)

Everything works without extra software. If the user wants the faster path, install the companion CLI once
and point the skill at it — see [`docs/toolkit.md`](docs/toolkit.md):

```bash
uv tool install git+https://github.com/creative-tech-solutions-sro/open-modder
```

Example: Put GTA V into Minecraft, with a with all GTA NPCs and functions on the Minecraft map.

<img width="3365" height="1368" alt="GTA_Minecraft_example" src="https://github.com/user-attachments/assets/c7be8c9e-2b84-45ec-95b0-b3fbc36a4604" />

## License

MIT — see [`LICENSE`](LICENSE). Derived from [open-modder](https://github.com/creative-tech-solutions-sro/open-modder)
(MIT); its knowledge base and full engine playbooks remain in that project.
