# Assets: art, 3D and sound that the engine will actually load

Generate the thing, then convert it to *exactly* the format, size and layout the game already uses.
Studying the game's own assets comes first: pixel size, palette, outline weight, camera angle, facing
convention, frame layout, bit depth, filename scheme. Put that into a reusable style suffix.

## Sources
- **fal (fal.ai)** — one key for hundreds of models, via `um fal <recipe>`, the fal MCP
  (`https://mcp.fal.ai/mcp`, `Authorization: Bearer $FAL_KEY`), or `fal api`. `FAL_KEY` comes from the
  environment or a `.env` file; never write it into mod files.
- **Local ComfyUI** — `um comfy status`, `um comfy image "..." --sprite`; no key needed, images only.
- **Anything else** — any image/audio/3D model the user has access to; the conversion steps below are the
  same regardless of the generator.

## Generate
| Asset | Recipe | Notes |
|---|---|---|
| Sprite / icon with transparency | `um fal sprite "<subject, view, style>" --name x` | or a flat background you flood-fill later |
| Concept / key art | `um fal image "<prompt>" --aspect 16:9` | study before coding |
| Consistent variants, extra frames | `um fal edit "<change>" --ref base.png` | never prompt a whole sheet in one go — grids come out uneven |
| Background removal | `um fal rmbg in.png` | |
| Clean pixel art from any image | `um fal pixelate in.png --colors 24` | |
| Seamless texture / PBR maps | `um fal texture ...`, `um fal pbr ...` | basecolor, normal, roughness, metalness, height |
| Image → textured 3D (GLB) | `um fal model3d concept.png` | then remesh low-poly, optionally auto-rig |
| SFX / music / voice | `um fal sfx/music/voice ...` | short SFX (0.2–2 s), loopable music |
| Trailer clip | `um fal video still.png "camera orbits the boss"` | |
| Anything else | `um fal run <endpoint> key=value image_url=@local.png` | |

Every call should land on disk with a manifest (`fal_manifest.jsonl`: endpoint, inputs, seed, request id)
so any asset can be traced and regenerated.

## Prompting that fits the game
- Describe view and orientation explicitly ("perfectly horizontal side view, muzzle pointing right");
  engines have conventions and fixing orientation later costs quality.
- Transparent or flat-colour backgrounds only — no gradients, scenery or ground shadows.
- Ask for "bright saturated team-colour accents" on the parts the game tints, then mask them by hue.
- Derive a set from one hero image with the edit endpoint, not by prompting a grid.
- No text or logos unless wanted; models love to add them.

## Convert
- **2D:** cut out the background → one nearest-neighbour fit to the engine's frame size (never a smooth
  resize on pixel art) → palette reduction if the game uses one → pack into the sheet/atlas layout the
  game expects → team-colour masks → preview before shipping.
- **Many angles / animations of the same object: go 3D.** One concept → `um fal model3d` → render every
  heading from the game's own camera (`um render3d --preset <game>` with Blender) → 8/16-heading sprite
  sets that stay consistent. This is how a 3D model becomes a civ's unit set.
- **Audio:** convert to the engine's format — `ffmpeg -i x.mp3 -ar 44100 x.wav` (XNA/tModLoader and most
  engines), `-c:a libvorbis -q:a 5 x.ogg` (Minecraft, Godot, Unity); trim silence first
  (`-af silenceremove=...`), normalize loudness (`-af loudnorm=I=-16`), keep SFX short, crossfade loops.
- **Textures:** match resolution, tiling, colour space (sRGB vs linear) and the material slots the engine
  reads.

## Cost and etiquette
- Price the expensive stuff before batching: 3D, video and long music dominate the bill. For more than
  ~20 generations, tell the user the rough cost first.
- Iterate cheap (low quality/resolution, few frames), then re-run the winners at full quality with the
  same prompt and seed.
- Credit generated assets and name the models in the mod's README, and check each model's licence before
  commercial use.
- Verify in game before batch-converting: one asset, launched, screenshot, *look at it*.
