---
name: flet-app-workflow
description: "Guided workflow for building a Flet declarative app from scratch (Flet 0.82.x+)."
---

# Flet App Workflow

You are guiding the user through building a **Flet declarative app** step by step. Follow these 5 phases in order.

Before starting, confirm the app's purpose and target platforms with the user.

---

## Phase 1: Requirements

Ask the user about:
1. **App purpose** — What does the app do?
2. **Target platforms** — Web, mobile (Android/iOS), desktop (macOS/Windows/Linux)?
3. **Pages needed** — List the main screens/pages
4. **External services** — Any Flet extensions or APIs needed?
5. **State shape** — What data needs to be shared across pages?

Summarize the requirements before proceeding.

---

## Phase 2: Project Setup

Create the project structure:

```
my_app/
├── pyproject.toml          # [tool.flet.app] path = "src"
└── src/
    ├── main.py
    ├── config.py
    ├── state.py
    ├── context.py
    ├── components/
    │   └── __init__.py
    └── pages/
        └── __init__.py
```

1. Create `pyproject.toml` with Flet dependency and `[tool.flet.app] path = "src"`
2. Create `state.py` with `@ft.observable @dataclass AppState` based on requirements
3. Create `context.py` with `AppContext` and `AppCtx = ft.create_context(None)`
4. Create `config.py` with constants and navigation configuration

---

## Phase 3: UI & Pages

For each page identified in Phase 1:
1. Create a `@ft.component` function in `pages/<name>.py`
2. Use `ft.use_context(AppCtx)` to access state and services
3. Use `ft.use_state()` for local form fields
4. Implement async handlers with try/except
5. Register in `pages/__init__.py` PAGE_BUILDERS dict

Create reusable components in `components/`:
- Navigation drawer (if multi-page)
- Log viewer, status bar, or other shared UI

---

## Phase 4: Logic & State

1. Wire up `main.py`:
   - Create state outside components
   - Initialize services with event handlers
   - Add services to `page.services`
   - Call `page.render_views(App, state, ...services)`
2. Implement the `App` root component:
   - Create `AppContext` with state + services
   - Build `ft.View` with `AppBar`, drawer, and `PageContent`
   - Use `view_ref` pattern for drawer
   - Provide context via `AppCtx(ctx, build_view)`
3. Implement `PageContent` component:
   - Read `state.current_page` from context
   - Render corresponding page from `PAGE_BUILDERS`

---

## Phase 5: Polish & Test

1. **Test locally**: `flet run src` or `cd` to project root and `flet run`
2. **Add theming**: `page.theme = ft.Theme(color_scheme_seed=ft.Colors.BLUE)`
3. **Add responsive layout**: Use `ft.ResponsiveRow` or breakpoint-based layouts
4. **Error handling**: Wrap service calls in try/except
5. **Build for target platforms**: `flet build web`, `flet build apk`, etc.

---

## Reminders

- Always use `ft.run(main)` — never `ft.app()`
- Always use declarative mode — no `page.add()` or `page.update()`
- `@ft.observable` BEFORE `@dataclass` (order matters)
- `ft.use_state` for form fields, `@ft.observable` for shared state
- Factory function for handlers in loops
- `cleanup=` parameter in `use_effect`, not return value
