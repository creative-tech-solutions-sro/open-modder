# Installing the OpenModder skill

OpenCode discovers skills from several locations. The skill ID comes from the **path**
(`skills/openmodder/SKILL.md` → `openmodder`); the frontmatter `name` is only a display label.

Pick one source. If the same ID exists in several, the later-registered source wins (project beats global).

## 1. Global (every project for this user)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.config\opencode\skills" | Out-Null
Copy-Item -Recurse -Force skills\openmodder "$env:USERPROFILE\.config\opencode\skills\openmodder"
```

Linux/macOS:

```bash
mkdir -p ~/.config/opencode/skills
cp -R skills/openmodder ~/.config/opencode/skills/openmodder
```

Global compatibility sources also work: `~/.claude/skills/openmodder/SKILL.md` or
`~/.agents/skills/openmodder/SKILL.md`.

## 2. Project (this project only)

```powershell
New-Item -ItemType Directory -Force .opencode\skills | Out-Null
Copy-Item -Recurse -Force skills\openmodder .opencode\skills\openmodder
```

Project compatibility sources: `.claude/skills/` and `.agents/skills/`.

Commit the copy if the project should carry the skill for everyone who clones it.

## 3. Registered directory (any folder, via config)

Point OpenCode at this repo's `skills/` folder from any `opencode.json(c)`:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "skills": ["D:/Tools/openmodder/skills"]
}
```

Relative paths resolve from the active OpenCode working directory; `~/` paths from the home directory;
absolute paths are used as written. Every discovered config contributes entries — arrays combine rather
than replace.

## 4. HTTP catalog (many machines)

Host the repo and serve an `index.json` at the base URL:

```json
{
  "skills": [
    {
      "name": "openmodder",
      "version": "1",
      "files": ["openmodder/SKILL.md", "openmodder/references/recon.md", "openmodder/references/engines.md"]
    }
  ]
}
```

OpenCode fetches each file from `<base-url>/openmodder/<file>`. Keep paths same-origin and relative, and
increment `version` whenever files change so the cache refreshes.

## Verify

- The skill must be a directory containing a file named exactly `SKILL.md` (root-level `*.md` also works,
  but then the ID is the file name).
- ID is `openmodder` — check it, not the frontmatter name.
- Load it explicitly with `@openmodder` in a prompt, or with the `skill` tool input `{"id": "openmodder"}`.
- If it is missing: confirm the path, add/keep the `description` in the frontmatter, check
  `metadata.opencode/autoinvoke` is not `false`, and check the agent's `skill` permission
  (`{"action": "skill", "resource": "openmodder", "effect": "allow"}`).
- Supporting files are **not** auto-loaded; the skill lists them and reads the ones it needs. Keep
  `references/` next to `SKILL.md`.

## Update or remove

```powershell
# update: re-copy over the installed copy
Copy-Item -Recurse -Force skills\openmodder "$env:USERPROFILE\.config\opencode\skills\openmodder"

# remove
Remove-Item -Recurse "$env:USERPROFILE\.config\opencode\skills\openmodder"
```

Changes take effect the next time the skill is loaded (restart the session if it was already loaded).
