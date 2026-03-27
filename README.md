# Flet Plugin for Claude Code

A Claude Code plugin that provides expert knowledge for building Python apps and extensions with the [Flet](https://flet.dev) framework.

## Installation

```bash
# Install from GitHub
claude plugin install github:brunobrown/claude-flet-plugin
```

## Codex

This repo also includes a Codex-friendly skill pack (same content, Codex layout):

- Instructions: `AGENTS.md`
- Skills: `.agents/skills/*/SKILL.md`
- Credits for `.codex/`: `CREDITS.md`

### Install / Use in Codex

Codex auto-detects `AGENTS.md`, `.agents/skills/`, and `.codex/` when you run it in a repo (or open the repo as a workspace).

```bash
git clone https://github.com/brunobrown/claude-flet-plugin.git
cd claude-flet-plugin
codex
```

Optional: copy the project defaults to your home directory:

```bash
cp .codex/config.toml ~/.codex/config.toml
```

### Use These Skills In Another Repo

Copy the skills into your target project under `.agents/skills/`:

```bash
cp -R /path/to/claude-flet-plugin/.agents/skills /path/to/your-project/.agents/
```

## What's Included

### Skills

| Skill | Description |
|-------|-------------|
| **flet-app** | Declarative mode — state management, hooks, navigation, theming, async patterns, component architecture, 82+ breaking changes, API traps, field validation (Annotated + V rules), customizable scrollbars |
| **flet-extension** | Extension packages — Service Controls, UI Controls, Python/Dart integration, type mapping, events, @control/@value decorators, Prop descriptor, publishing |
| **flet-imperative** | Imperative mode — `page.add`, smart auto-update, 82+ breaking changes, API traps, error guide, 19 new controls, customizable scrollbars, expanded SharedPreferences, 20 verified examples |

### Commands

| Command | Description |
|---------|-------------|
| `/flet-app` | Guided 5-phase workflow for building a Flet app from scratch |
| `/flet-extension` | Guided 6-phase workflow for creating a Flet extension package |
| `/flet-review` | Code review with Flet-specific checklists for apps and extensions |

### Agents

| Agent | Description |
|-------|-------------|
| **flet-app-builder** | Expert app developer — builds multi-platform Python apps with proper state management, navigation, and declarative UI |
| **flet-extension-builder** | Expert extension developer — creates Service and UI Control extensions with Python/Dart integration |

## Flet Version

This plugin targets **Flet 0.83.x** (both declarative and imperative modes). Code examples use:

- `ft.run(main)` as entry point
- `@ft.component` for functional components
- `@ft.observable @dataclass` for reactive state
- `ft.use_state`, `ft.use_effect`, `ft.use_context` hooks
- `ft.Colors.NAME` and `ft.Icons.NAME` (uppercase constants)

## License

MIT
