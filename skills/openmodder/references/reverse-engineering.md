# Reverse engineering for mods

Rule zero: **read the real thing, don't guess.** Decompiled code, the actual data, a live memory view or a
GPU capture is the spec. Write what you learn into `MODLOG.md` — names, IDs, offsets, formats — as you go.

Keep all decompiled output and extracted assets **outside** the mod repo (e.g. `~/<game>-decomp/`) and
never publish them. Single-player, offline or servers the user runs. Never attach a debugger or scanner to
a game with anti-cheat — `references/safety.md`.

## Pick the tool by what the code is

**Managed .NET** (XNA/FNA, Unity Mono, most indie C#)
- `dotnet tool install -g ilspycmd`, then `ilspycmd -p -o ~/<game>-decomp <Game>.exe` (or
  `Assembly-CSharp.dll`) — a full C# project you can grep.
- dnSpyEx: step with a debugger, set breakpoints, edit methods live.
- MCP servers for ILSpy/dnSpy let an agent query types and methods directly.

**Unity IL2CPP**
- Cpp2IL or Il2CppDumper on `GameAssembly.dll` + `global-metadata.dat`: types, fields, signatures,
  addresses, plus dummy DLLs for ILSpy.
- Method bodies are native: load `GameAssembly.dll` in Ghidra/IDA and apply the generated script to name
  functions. Live: UnityExplorer.

**Java** (Minecraft, Slay the Spire…) — Vineflower / CFR / Recaf; for Minecraft, Loom `genSources` with
Mojang mappings.

**Native C/C++** (custom engines, Unreal game code, recomps)
- Ghidra (free) or IDA, driven over MCP so you can decompile, rename, retype and follow cross-references.
- Workflow: strings → cross-references → the function; name and type everything you understand (renames
  accumulate into a readable program); **confirm dynamically before building on a guess** — agents can
  confidently misidentify things.
- Unreal: dump reflection data first (UE4SS dumper or Dumper-7); it names most gameplay classes and
  properties for free.

**Dynamic / live** — Cheat Engine value scans → "what writes this address" → struct → owner; x64dbg
breakpoints and traces (bind its MCP to `127.0.0.1`); Frida hooks that log arguments; ReClass.NET to
rebuild structs from live memory.

**Graphics** — RenderDoc captures a frame: every draw, render target, constant buffer, depth buffer.
That's how you find where geometry or effects get injected, and which texture holds a sprite atlas.

## Data files and asset formats
Use the community tool first: UABEA/AssetRipper (Unity), FModel/UAssetGUI (Unreal), xEdit/BSArch
(Bethesda), UndertaleModTool (GameMaker), genieutils (Genie/AoE2), VRF/Crowbar (Source), WitchyBND/Smithbox
(FromSoft), GDRE Tools (Godot).

Before reversing a format yourself, search the community archives — most game formats from the 2000s–2010s
were already worked out on XeNTaX (archived on archive.org, with a GitHub backup), ZenHAX (QuickBMS
scripts) and their successor forums. Use them for format knowledge and tools; do **not** download game
files, leaked code, SDKs or keys from them.

For an **undocumented format**:
1. Collect several stock files. Compare sizes, hex-dump the headers. Look for magic numbers, counts,
   offsets and tables of fixed-size records.
2. Hypothesise the header, then the record/frame layout, then the compression. Check by parsing every
   stock file without errors.
3. Write a reader that decodes to something viewable (PNGs, JSON) and **look at it**.
4. Write the writer and **prove it with a round trip**: decode → encode → decode, compared against the
   original (a sprite writer is only done when it round-trips a stock asset with near-zero error).
5. Only then write new files. Test in game with one asset before batch-converting.

## Make it an oracle
- **Logic you port** (simulator, trainer, reimplementation): record real traces from the game — positions,
  velocities per tick — and replay them against your port. Replays find what reading the code misses:
  hidden state, float32 constant differences, an action applied one frame late.
- **For long RE runs:** keep a journal; take small verified steps; cap attempts per problem (about three
  identical failures, then change approach); commit every confirmed fact.
- Check what the *executable* enforces, not only what the data says: a data file may happily hold content
  the hard-coded tables in the EXE will never display, which turns "add" into "replace a slot".
