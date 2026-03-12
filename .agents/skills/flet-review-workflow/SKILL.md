---
name: flet-review-workflow
description: "Checklist-driven review for Flet apps and extensions (Flet 0.82.x+)."
---

# Flet Review Workflow

You are performing a comprehensive code review of Flet code. First, determine whether the code is a **Flet app** or a **Flet extension**, then apply the appropriate checklist.

---

## Step 1: Identify Code Type

Scan the codebase to determine:
- **App**: Contains `ft.run(main)`, `@ft.component`, `page.render_views()`
- **Extension**: Contains `@ft.control("Name")`, `ft.Service` or `ft.LayoutControl`
- **Both**: App that uses a custom extension

---

## Step 2: App Checklist

If reviewing a Flet app, check all of these:

### Entry Point & Rendering
- [ ] Uses `ft.run(main)` — NOT `ft.app()`
- [ ] Uses `page.render_views()` or `page.render()` — NOT `page.add()`
- [ ] Does NOT call `page.update()` manually (declarative mode handles this)
- [ ] `page.render(Component)` passes reference, NOT `page.render(Component())`

### State Management
- [ ] `@ft.observable` comes BEFORE `@dataclass` (order matters)
- [ ] `ft.use_state()` used for local form fields (not `@ft.observable`)
- [ ] Observable lists use `.append()` / `.clear()` to trigger re-renders
- [ ] State created outside components in `main()` when handlers need access
- [ ] No direct mutation of observable list elements (`items[0] = x`)

### Hooks
- [ ] All hooks called inside `@ft.component` only (never in regular functions)
- [ ] Hooks NOT inside conditionals or loops (fixed order required)
- [ ] `use_effect` cleanup uses `cleanup=` parameter (NOT return value)
- [ ] `use_effect` dependencies array is correct ([] for mount-only)
- [ ] `use_ref` used for mutable refs that shouldn't trigger re-renders

### Components
- [ ] All components use `@ft.component` decorator
- [ ] Context accessed via `ft.use_context(AppCtx)` (not passing `page` as prop)
- [ ] Factory functions used in loops to avoid closure traps
- [ ] Async handlers use try/except for error handling

### Navigation
- [ ] `view_ref[0].show_drawer()` used (NOT `page.show_drawer()`)
- [ ] PAGE_BUILDERS dict maps page IDs to component functions
- [ ] Router clears `page.views` before adding new views
- [ ] `on_view_pop` handler implemented for router-based navigation

### Styling
- [ ] Uses `ft.Colors.NAME` (uppercase) not `ft.colors.NAME`
- [ ] Uses `ft.Icons.NAME` (uppercase) not `ft.icons.NAME`
- [ ] Theme uses `color_scheme_seed` or `color_scheme` (not `primary_swatch`)

---

## Step 3: Extension Checklist

If reviewing a Flet extension, check all of these:

### Python Side
- [ ] `@ft.control("PascalCaseName")` has explicit string name
- [ ] Correct base class: `ft.Service` (no UI) or `ft.LayoutControl` (visual)
- [ ] Internal fields use `field(default=None, init=False, metadata={"skip": True})`
- [ ] Events typed as `Optional[ft.EventHandler[EventType]]`
- [ ] Event classes extend `ft.Event["ControlClassName"]`
- [ ] Enums use `Enum(str, Enum)` or `class Name(Enum)` with string values
- [ ] Platform validated in `_invoke_method` override (not `before_update`)
- [ ] `__init__.py` has `__all__` with all public exports
- [ ] No imports from `flet.core.*` (use `import flet as ft`)
- [ ] No `_set_attr()` usage (use dataclass fields)

### Dart Side
- [ ] `control.type` case matches `@ft.control("Name")` exactly
- [ ] `control.addInvokeMethodListener` called in `init()`
- [ ] Args converted: `Map<String, dynamic>.from(args)` before use
- [ ] Booleans returned as `.toString()` (Python expects "true"/"false")
- [ ] All listeners wrapped in try/catch → `_handleError()`
- [ ] Listener setup guarded with `_listenersSetup` flag
- [ ] `control.triggerEvent("name", data)` names match Python `on_name`
- [ ] `_handleError` reports to `FlutterError.reportError`
- [ ] `dispose()` cleans up resources and subscriptions
- [ ] Service: `createService()` in extension.dart
- [ ] UI Control: `createWidget()` in extension.dart

### Project Configuration
- [ ] `pyproject.toml` has `[tool.setuptools.package-data]` for Flutter code
- [ ] `pubspec.yaml` has `publish_to: none`
- [ ] Example app has `[tool.flet.app] path = "src"` if using src/ layout
- [ ] Extension registered in app's `[tool.flet.extensions]`

---

## Step 4: Report

Generate a review report with:

1. **Summary** — Overall assessment (pass / needs fixes)
2. **Issues Found** — List each issue with:
   - Severity: Critical / Warning / Info
   - File and line number
   - What's wrong
   - How to fix it
3. **Good Practices** — Note things done correctly
4. **Suggestions** — Optional improvements

Present the report in a clear, actionable format. Offer to fix critical issues automatically.
