# Workflow: the loop and what it produces

`SKILL.md` holds the loop; this document expands each stage and lists the artifact it must leave behind.
Nothing is "done" until the artifact exists — the artifacts are what survives context compaction.

| # | Stage | Artifact |
|---|---|---|
| 0 | Intake | one-sentence idea, agreed definition of done, online/offline verdict, `MODLOG.md` started |
| 1 | Recon | `MODDING_PLAN.md` |
| 2 | Route | route + reason in `MODLOG.md` |
| 3 | Lab | backups on disk, restore path in `MODLOG.md` |
| 4 | Source of truth | exact names, IDs, offsets, formats in `MODLOG.md` |
| 5 | Vertical slice | one feature working in game, committed |
| 6 | Assets | engine-exact files + generation manifest |
| 7 | Verify | screenshot + log evidence per change |
| 8 | Showcase | 20–45 s clip |
| 9 | Package | linted archive + README |
| 10 | Field note | a short write-up: versions, route, engine facts, verification, gotchas |

## 0. Intake
Keep it to a few questions: game + store/platform, the idea in one sentence, what "done" means (usually
working in game plus a short clip). Decide online vs offline **now** — if the game is online or
competitive with anti-cheat, the client is off limits and the offer becomes offline modes, servers the
user runs, or official tools.

Start `MODLOG.md`: paths, IDs, file formats, class names, what failed and why, next step. It is the
journal the whole run depends on.

## 1. Recon
Prior art first (`um kb search "<game>"` or the upstream knowledge-base index), then fingerprint the
install, then the living community — wiki, Nexus/Workshop/Thunderstore/mod.io, GitHub, recent posts.
Versions move, so nothing gets installed from memory. Output: `MODDING_PLAN.md` (template in
`references/recon.md`).

## 2. Route
Data/assets → loader API → managed patching → native hooks → reimplementation → mashup: the cheapest that
reaches the idea. Write route **and reason** down before building; if the plan later changes, amend it
rather than leaving a stale justification.

## 3. Lab
Backups of saves and config before the first modded launch; a lab profile/save directory when the loader
allows it; windowed mode at a known client size; decompiled and extracted material kept outside the repo.
The restore path is one command, written down.

## 4. Source of truth
Read what the game actually does: decompile (managed), dump (IL2CPP, data formats), inspect live memory or
capture a frame. Record exact symbols and IDs. Check what the executable enforces, not just what the data
contains. See `references/reverse-engineering.md`.

## 5. Vertical slice
One item, unit or weapon end to end with placeholder art: define it, launch, prove it appears and works
from the log plus a screenshot you actually looked at. Only then widen. Commit each working step.

## 6. Assets
Study the game's own assets, generate what is missing, convert to the exact engine format (size, palette,
sheet layout, facing, audio format). Batch only after a single asset has been verified in game.
See `references/assets.md`.

## 7. Verify
The running game decides. Repeatable test (mod-side command, scripted scene, test world), act, screenshot,
read the log. Three identical failures → stop, write down what you know, change approach or ask.
See `references/verification.md`.

## 8. Showcase
Scripted take so it repeats, recorded window with the game's own audio, contact sheet to pick moments,
then a 20–45 s cut with one-line titles.

## 9. Package
Lint (no game files, no decompiled code, no keys), package the way the platform expects, README with
install steps and honest credits. Publishing is the user's call.

## 10. Field note
Clean up `MODLOG.md` into: exact versions, the route and why, how the engine really works (in your own
words), how it was verified — and what was not — plus numbered gotchas (symptom → cause → fix). Dead ends
count: an honest `abandoned` note saves the next run hours.

## Working agreements
- One claim per verification: never report "it works" without a screenshot or log line.
- Journal before context gets tight; the note at the end is mostly a cleaned-up copy of the journal.
- Ask before anything public (PR, upload, post) and before anything irreversible (delete, overwrite,
  registry, long input sessions).
