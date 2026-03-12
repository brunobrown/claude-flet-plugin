# Changelog

## [0.3.0] - 2026-03-12

### Added

- Codex skill pack support via `AGENTS.md`, `.agents/skills/*`, and `.codex/*` (keeps Claude plugin layout unchanged)

## [0.2.0] - 2026-03-05

### Changed

- Restructured repository to flat layout matching Claude Code plugin conventions (agents/, commands/, skills/ at root)
- Updated `marketplace.json` with `source: "./"` (flat plugin pattern)
- Updated `package.json` with `files`, `bugs`, `homepage` fields
- Bumped target Flet version from 0.81.x to **0.82.x** across all skills and agents
- Removed nested `plugins/flet/` directory

### Target Flet Version

- Flet **0.82.x** — Verified: no breaking changes to declarative mode, hooks, or extension system

---

## [0.1.0] - 2026-03-02

### Added

- **Skills** (2):
  - `flet-app` — Complete reference for building multi-platform Flet declarative apps (state management, hooks, navigation, theming, async patterns, component architecture)
  - `flet-extension` — Complete reference for creating Flet extension packages (Service Controls and UI Controls, Python/Dart integration, type mapping, events, compound widgets, publishing)

- **Agents** (2):
  - `flet-app-builder` — Senior Flet engineer agent that builds apps using declarative mode with proper patterns
  - `flet-extension-builder` — Senior extension engineer agent that creates Service and UI Control extensions wrapping Flutter packages

- **Commands** (3):
  - `/flet-app` — Guided 5-phase workflow for building a Flet app from scratch
  - `/flet-extension` — Guided 6-phase workflow for creating a Flet extension package
  - `/flet-review` — Code review with checklists for both Flet apps and extensions

- **Project Configuration**:
  - `plugin.json` with agents, commands, and skills paths
  - `marketplace.json` for Claude Code marketplace registration
  - `package.json` with npm-style metadata

### Target Flet Version

- Flet **0.81.x** — Declarative mode (`ft.run`, `@ft.component`, `@ft.observable`)
