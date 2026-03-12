# Project Instructions (Codex + Claude Plugin)

This repository is a **Claude Code plugin** and also a **Codex skill pack** for the Flet framework.

## Content map

- Claude plugin metadata: `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`
- Claude plugin agents: `agents/*.md`
- Claude plugin commands: `commands/*.md`
- Claude plugin skills: `skills/*/SKILL.md`

## Codex skill pack

Codex-oriented skills are provided under:

- `.agents/skills/flet-app/` (reference: declarative apps, Flet 0.82.x+)
- `.agents/skills/flet-extension/` (reference: extension packages)
- `.agents/skills/flet-app-workflow/` (guided app build workflow)
- `.agents/skills/flet-extension-workflow/` (guided extension build workflow)
- `.agents/skills/flet-review-workflow/` (review checklist workflow)

Each skill includes:

- `SKILL.md` (the content)
- `agents/openai.yaml` (Codex metadata)

## Flet version target

All guidance in this repo targets **Flet 0.82.x** (declarative mode).

## Credits

The `.codex/` folder is inspired by the ECC structure/config conventions by `affaan-m`:

```text
https://github.com/affaan-m/everything-claude-code/tree/main/.codex
```

See `CREDITS.md` for details.
