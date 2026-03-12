# Codex Notes (Flet)

This file supplements the root `AGENTS.md` with Codex-specific notes for this repo.

## Skills

Skills are auto-loaded from `.agents/skills/`. In this repo:

- `flet-app` and `flet-extension` are the core reference skills.
- `flet-app-workflow`, `flet-extension-workflow`, `flet-review-workflow` are guided workflows (adapted from the Claude `commands/`).

Each skill folder includes:

- `SKILL.md`
- `agents/openai.yaml`

## Multi-Agent (Optional)

This repo ships generic multi-agent layers under `.codex/agents/` (read-only explorer/reviewer/docs-researcher).
They are optional and only apply if you enable `[features] multi_agent = true` and configure `[agents.*]` in `.codex/config.toml`.

This repo also ships Flet-specific builder roles you can use as child agents:

- `.codex/agents/flet-app-builder.toml`
- `.codex/agents/flet-extension-builder.toml`

## Credits

The `.codex/` structure and idea of a reference configuration are inspired by Everything Claude Code (ECC) by `affaan-m`:

https://github.com/affaan-m/everything-claude-code/tree/main/.codex
