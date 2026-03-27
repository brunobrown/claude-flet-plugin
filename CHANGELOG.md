# Changelog

## [0.5.0] - 2026-03-27

### Added

- **Flet 0.83.x support** — Updated all skills, agents, commands, and references for Flet 0.83.0
- **Performance documentation** — Up to 6.7x faster control diffing via `Prop` descriptor and `@value` decorator
- **Smart update logic** — Framework tracks explicit `.update()` calls to skip redundant auto-updates
- **Declarative field validation** — `Annotated` types with `V` rules (`V.between`, `V.ge`, `V.instance_of`, etc.)
- **Customizable scrollbars** — `Scrollbar` class with `thumb_visibility`, `track_visibility`, `thickness`, `radius`, `interactive`, `orientation`
- **Scrollable ExpansionPanelList** — Now inherits `ScrollableControl`
- **Expanded SharedPreferences** — Supports `int`, `float`, `bool`, `list[str]` in addition to `str`
- **Padding removal notice** — Module-level `ft.padding.all()` / `.symmetric()` / `.only()` removed in 0.83.0
- **Flet 0.83.x review checklist** in `/flet-review` command
- **Extension builder awareness** — `@control` / `@value` decorators, `Prop` descriptor, `skip_field()` helper

### Changed

- Bumped target Flet version from **0.82.x** to **0.83.x** across all skills, agents, commands, references, and README
- Updated `flet-app-builder` agent with 0.83.x performance and API awareness
- Updated `flet-extension-builder` agent with 0.83.x decorator and descriptor awareness
- Enriched `api-traps.md` with padding deprecation, SharedPreferences types, and scrollbar customization
- Enriched `error-guide.md` with 0.83.x-specific errors
- Enriched `new-controls.md` with 0.83.x features (scrollbars, SharedPreferences, field validation, performance)

### Target Flet Version

- Flet **0.83.x** — Performance improvements, field validation, customizable scrollbars

---

## [0.4.0] - 2026-03-24

### Added

- **New skill: `flet-imperative`** — Complete reference for imperative/procedural mode (`page.add`, `page.update`, auto-update mechanism) with 20 verified example files
- **4 reference guides** (shared by both declarative and imperative skills):
  - `references/breaking-changes.md` — 82+ breaking changes from Flet 0.x to 1.0+
  - `references/api-traps.md` — Critical API pitfalls verified with `inspect`
  - `references/error-guide.md` — Error lookup table with solutions
  - `references/new-controls.md` — 19 new controls in Flet 1.0+ (MD3 buttons, SearchBar, Shimmer, KeyboardListener, etc.)
- **20 example files** covering basic apps, async, forms, file picker, animations, dialogs, layouts, tabs, navigation, data tables, window controls, drag-and-drop, keyboard events, gestures, clipboard, media, canvas, file I/O, and chart visualization
- Flet 1.0+ breaking changes checklist in `/flet-review` command
- Flet 1.0+ anti-patterns in `flet-app-builder` agent

### Changed

- Enriched `flet-app` skill description and added reference documentation section
- All new content translated to English from [awesome-flet-development-skill](https://github.com/HnBigVolibear/awesome-flet-development-skill)

---

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
