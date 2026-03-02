# Flet Plugin for Claude Code

A Claude Code plugin that provides expert knowledge for building Python apps and extensions with the [Flet](https://flet.dev) framework.

## Installation

```bash
# In Claude Code
/plugin install /path/to/claude-flet-plugin
```

## What's Included

### Skills

| Skill | Description |
|-------|-------------|
| **flet-app** | Complete reference for building declarative Flet apps — state management, hooks, navigation, theming, async patterns, and component architecture |
| **flet-extension** | Complete reference for creating Flet extension packages — Service Controls, UI Controls, Python/Dart integration, type mapping, events, and publishing |

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

This plugin targets **Flet 0.81.x+** (declarative mode). All code examples use:

- `ft.run(main)` as entry point
- `@ft.component` for functional components
- `@ft.observable @dataclass` for reactive state
- `ft.use_state`, `ft.use_effect`, `ft.use_context` hooks
- `ft.Colors.NAME` and `ft.Icons.NAME` (uppercase constants)

## License

MIT
