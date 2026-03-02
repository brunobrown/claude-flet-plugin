---
description: Guided workflow for creating a Flet extension package (Service or UI Control)
argument-hint: Optional Flutter package name (e.g. onesignal_flutter)
---

# /flet-extension — Create a Flet Extension

You are guiding the user through creating a **Flet extension package** step by step. Follow these 6 phases in order.

Before starting, ask the user what Flutter package they want to wrap and whether it's a Service (no UI) or UI Control (visual widget).

---

## Phase 1: Planning

1. **Identify the Flutter package** — name on pub.dev, latest version
2. **Determine extension type**:
   - **Service** (`ft.Service`) — for SDKs, background services, platform APIs
   - **UI Control** (`ft.LayoutControl`) — for widgets that render on screen
3. **Review the Flutter API** — key classes, methods, events, callbacks
4. **Plan the Python API**:
   - Properties (sent to Flutter)
   - Methods (invoke Flutter methods)
   - Events (receive from Flutter)
   - Sub-modules (if complex SDK with namespaces)
5. **Plan type mappings** — Dart types → Python types

Summarize the plan before proceeding.

---

## Phase 2: Scaffold

Use `flet-pkg` CLI to generate the project skeleton:

```bash
# Auto-analyze the Flutter package (recommended)
flet-pkg create my-extension --analyze

# Or manually if flet-pkg is not available
```

If scaffolding manually, create:
```
flet-my-extension/
├── pyproject.toml
└── src/
    ├── flet_my_extension/
    │   ├── __init__.py
    │   ├── my_control.py
    │   └── types.py
    └── flutter/flet_my_extension/
        ├── pubspec.yaml
        └── lib/
            ├── flet_my_extension.dart
            └── src/
                ├── extension.dart
                └── my_service.dart
```

Ensure `pyproject.toml` includes:
```toml
[tool.setuptools.package-data]
"flutter.flet_my_extension" = ["**/*"]
```

---

## Phase 3: Review Generated Code

Check and fix the generated code:

1. **Type mappings** — verify Dart→Python type conversions are correct
2. **Method names** — `camel_to_snake` conversion from Dart
3. **Invoke keys** — `_invoke_method("key")` must match Dart switch cases
4. **Event names** — `triggerEvent("name")` → `on_name: EventHandler`
5. **@ft.control name** — must match `control.type` switch in Dart
6. **Enum values** — string values that match Dart representations
7. **Platform validation** — service-only or cross-platform?

---

## Phase 4: Customize Implementation

### Python side:
1. Add any missing methods, properties, or events
2. Implement sub-modules if the SDK has namespaces
3. Add proper type annotations and docstrings
4. Set up `__init__.py` with `__all__` exports
5. Add `ErrorEvent` and `on_error` handler

### Dart side:
1. Implement SDK initialization in `init()`
2. Wire up `_onInvokeMethod` with all methods
3. Set up SDK listeners → `control.triggerEvent()`
4. Add `_handleError` with `FlutterError.reportError`
5. Guard against duplicate listeners
6. Add `dispose()` cleanup

---

## Phase 5: Test

1. **Create example app** in `examples/`:
   ```python
   import flet as ft
   import flet_my_extension as fme

   async def main(page: ft.Page):
       service = fme.MyService(
           on_event=lambda e: print(f"Event: {e.data}"),
           on_error=lambda e: print(f"Error: {e.message}"),
       )
       page.services.append(service)  # or page.add() for UI
       # ... test methods

   ft.run(main)
   ```
2. **Run example**: `cd examples/flet_my_extension_example && flet run`
3. **Unit tests**: test Python classes, type conversions, enum values
4. **Integration test**: verify invoke/trigger flow on target platforms

---

## Phase 6: Publish

1. **Update metadata** in `pyproject.toml` (description, author, URLs)
2. **Create README.md** with installation and usage examples
3. **Create CHANGELOG.md**
4. **Build**: `python -m build`
5. **Publish to PyPI**: `twine upload dist/*`
6. **Note**: Dart code is NOT published to pub.dev separately (`publish_to: none`)

---

## Reminders

- `@ft.control("Name")` — PascalCase string must match Dart `control.type`
- `metadata={"skip": True}` for all internal fields
- Always convert Dart args: `Map<String, dynamic>.from(args)`
- Return bools as `.toString()` in Dart
- `[tool.setuptools.package-data]` is mandatory in pyproject.toml
- Service → `page.services.append()` | UI → `page.add()`
- FletExtension → `createService()` for services, `createWidget()` for UI