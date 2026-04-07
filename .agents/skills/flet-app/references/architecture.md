# Flet App Architecture — Clean Architecture Pattern

> Recommended project structure for production Flet apps. Follows clean architecture principles with clear separation between data, domain, and presentation layers.

---

## Directory Structure

```
flet_project/
├── assets/
│   ├── fonts/
│   ├── icons/
│   └── images/
│
├── src/
│   │
│   ├── core/                     # Application core (shared base)
│   │   ├── constants.py         # Global constants
│   │   ├── config.py            # General configuration
│   │   ├── exceptions.py        # Error handling
│   │   └── logger.py            # Application logging
│   │
│   ├── data/                    # Data layer (input/output)
│   │   ├── sources/             # APIs, local database, etc.
│   │   │   ├── api_source.py
│   │   │   └── local_source.py
│   │   │
│   │   ├── models/              # Data transport models (DTOs)
│   │   │   └── user_model.py
│   │   │
│   │   └── repositories/        # Concrete implementations
│   │       └── user_repository_impl.py
│   │
│   ├── domain/                  # Business rules (system core)
│   │   ├── entities/            # Pure entities
│   │   │   └── user.py
│   │   │
│   │   ├── repositories/        # Interfaces (contracts)
│   │   │   └── user_repository.py
│   │   │
│   │   └── usecases/            # Use cases (business rules)
│   │       └── login_usecase.py
│   │
│   ├── presentation/            # User interface (Flet)
│   │   │
│   │   ├── components/          # Reusable components
│   │   │   ├── common/
│   │   │   │   ├── buttons.py
│   │   │   │   └── inputs.py
│   │   │   │
│   │   │   └── dialogs/
│   │   │       └── dialog_factory.py
│   │   │
│   │   ├── pages/               # Main screens
│   │   │   ├── auth/
│   │   │   │   ├── login_page.py
│   │   │   │   └── signup_page.py
│   │   │   │
│   │   │   ├── home/
│   │   │   │   ├── home_page.py
│   │   │   │   └── home_controller.py
│   │   │   │
│   │   │   └── settings/
│   │   │       └── settings_page.py
│   │   │
│   │   ├── navigation/          # Routing and navigation
│   │   │   ├── app_router.py
│   │   │   └── navigation_service.py
│   │   │
│   │   ├── themes/              # Visual themes (colors, styles)
│   │   │   ├── app_theme.py
│   │   │   └── colors.py
│   │   │
│   │   ├── hooks/               # Reusable hooks (state + logic)
│   │   │   ├── use_auth.py
│   │   │   ├── use_navigation.py
│   │   │   └── use_theme.py
│   │   │
│   │   └── state_management/    # Global state
│   │       ├── global_state.py
│   │       └── user_state.py
│   │
│   ├── services/                # External services (infrastructure)
│   │   ├── api_service.py
│   │   └── storage_service.py
│   │
│   └── utils/                   # Helper functions
│       ├── validators.py
│       └── string_extensions.py
│
├── main.py                      # Application entry point
├── app.py                       # Flet app configuration
│
├── tests/                       # Automated tests
│   ├── unit/
│   ├── widget/
│   └── integration/
│
├── pyproject.toml               # Dependencies
├── uv.lock
└── README.md
```

---

## Layer Responsibilities

### `core/` — Shared Base

Cross-cutting concerns used by all layers.

```python
# core/constants.py
APP_NAME = "MyApp"
API_BASE_URL = "https://api.example.com"
DEFAULT_TIMEOUT = 30

# core/config.py
from dataclasses import dataclass

@dataclass
class AppConfig:
    debug: bool = False
    api_url: str = API_BASE_URL
    log_level: str = "INFO"

# core/exceptions.py
class AppException(Exception):
    def __init__(self, message: str, code: str | None = None):
        self.message = message
        self.code = code
        super().__init__(message)

class NetworkException(AppException): ...
class AuthException(AppException): ...
class ValidationException(AppException): ...
```

---

### `domain/` — Business Rules (No Flet Dependency)

Pure Python. No imports from `flet`, `data/`, or `presentation/`. This layer defines **what** the system does.

```python
# domain/entities/user.py
from dataclasses import dataclass

@dataclass
class User:
    id: str
    name: str
    email: str
    is_active: bool = True

# domain/repositories/user_repository.py (interface / contract)
from abc import ABC, abstractmethod

class UserRepository(ABC):
    @abstractmethod
    async def get_by_id(self, user_id: str) -> User: ...

    @abstractmethod
    async def save(self, user: User) -> None: ...

# domain/usecases/login_usecase.py
class LoginUseCase:
    def __init__(self, user_repo: UserRepository, auth_service):
        self._user_repo = user_repo
        self._auth_service = auth_service

    async def execute(self, email: str, password: str) -> User:
        token = await self._auth_service.authenticate(email, password)
        user = await self._user_repo.get_by_id(token.user_id)
        return user
```

---

### `data/` — Data Layer (Input/Output)

Implements domain interfaces. Handles API calls, local storage, serialization.

```python
# data/models/user_model.py
from dataclasses import dataclass
from domain.entities.user import User

@dataclass
class UserModel:
    id: str
    name: str
    email: str
    is_active: bool = True

    @staticmethod
    def from_json(data: dict) -> "UserModel":
        return UserModel(**data)

    def to_entity(self) -> User:
        return User(id=self.id, name=self.name, email=self.email, is_active=self.is_active)

# data/sources/api_source.py
import httpx

class ApiSource:
    def __init__(self, base_url: str):
        self._client = httpx.AsyncClient(base_url=base_url)

    async def get(self, path: str) -> dict:
        response = await self._client.get(path)
        response.raise_for_status()
        return response.json()

# data/repositories/user_repository_impl.py
from domain.repositories.user_repository import UserRepository
from domain.entities.user import User
from data.sources.api_source import ApiSource
from data.models.user_model import UserModel

class UserRepositoryImpl(UserRepository):
    def __init__(self, api: ApiSource):
        self._api = api

    async def get_by_id(self, user_id: str) -> User:
        data = await self._api.get(f"/users/{user_id}")
        return UserModel.from_json(data).to_entity()

    async def save(self, user: User) -> None:
        await self._api.post(f"/users/{user.id}", data=vars(user))
```

---

### `presentation/` — User Interface (Flet)

All Flet-specific code lives here. Components, pages, navigation, hooks, and state management.

#### Pages (Declarative Mode)

```python
# presentation/pages/auth/login_page.py
import flet as ft

@ft.component
def LoginPage():
    ctx = ft.use_context(AppCtx)
    email, set_email = ft.use_state("")
    password, set_password = ft.use_state("")
    error, set_error = ft.use_state("")

    async def handle_login(e):
        try:
            user = await ctx.login_usecase.execute(email, password)
            ctx.state.current_user = user
            ctx.state.current_page = "home"
        except AuthException as ex:
            set_error(ex.message)

    return ft.Column([
        ft.TextField(label="Email", value=email, on_change=lambda e: set_email(e.control.value)),
        ft.TextField(label="Password", value=password, password=True,
                     on_change=lambda e: set_password(e.control.value)),
        ft.Text(error, color=ft.Colors.RED) if error else ft.Container(),
        ft.FilledButton(content=ft.Text("Sign In"), on_click=handle_login),
    ])
```

#### Pages (Imperative Mode)

```python
# presentation/pages/auth/login_page.py
import flet as ft

def login_page(page: ft.Page, login_usecase):
    email_field = ft.TextField(label="Email")
    password_field = ft.TextField(label="Password", password=True)
    error_text = ft.Text("", color=ft.Colors.RED)

    async def handle_login(e):
        try:
            user = await login_usecase.execute(email_field.value, password_field.value)
            page.go("/home")
        except AuthException as ex:
            error_text.value = ex.message
            page.update()

    return ft.Column([
        email_field,
        password_field,
        error_text,
        ft.FilledButton(content=ft.Text("Sign In"), on_click=handle_login),
    ])
```

#### Hooks (Declarative Mode Only)

```python
# presentation/hooks/use_auth.py
import flet as ft

def use_auth():
    """Custom hook for authentication state and actions."""
    ctx = ft.use_context(AppCtx)
    is_loading, set_loading = ft.use_state(False)

    async def login(email: str, password: str):
        set_loading(True)
        try:
            user = await ctx.login_usecase.execute(email, password)
            ctx.state.current_user = user
        finally:
            set_loading(False)

    async def logout():
        ctx.state.current_user = None
        ctx.state.current_page = "login"

    return ctx.state.current_user, is_loading, login, logout
```

#### State Management (Declarative Mode)

```python
# presentation/state_management/global_state.py
from dataclasses import dataclass, field
import flet as ft

@ft.observable
@dataclass
class GlobalState:
    current_page: str = "login"
    current_user: User | None = None
    theme_mode: str = "light"
    notifications: list = field(default_factory=list)
```

#### Themes

```python
# presentation/themes/app_theme.py
import flet as ft

def light_theme() -> ft.Theme:
    return ft.Theme(
        color_scheme_seed=ft.Colors.BLUE,
        font_family="Roboto",
    )

def dark_theme() -> ft.Theme:
    return ft.Theme(
        color_scheme_seed=ft.Colors.BLUE,
        font_family="Roboto",
    )
```

#### Navigation

```python
# presentation/navigation/app_router.py
from presentation.pages.auth.login_page import LoginPage
from presentation.pages.home.home_page import HomePage
from presentation.pages.settings.settings_page import SettingsPage

PAGE_BUILDERS = {
    "login": LoginPage,
    "home": HomePage,
    "settings": SettingsPage,
}
```

---

### `services/` — External Services

Infrastructure wrappers not tied to any specific feature.

```python
# services/api_service.py
import httpx
from core.config import AppConfig

class ApiService:
    def __init__(self, config: AppConfig):
        self._client = httpx.AsyncClient(
            base_url=config.api_url,
            timeout=30.0,
        )

    async def get(self, path: str) -> dict: ...
    async def post(self, path: str, data: dict) -> dict: ...

# services/storage_service.py
import flet as ft

class StorageService:
    def __init__(self, page: ft.Page):
        self._prefs = ft.SharedPreferences()
        page.services.append(self._prefs)

    async def get(self, key: str) -> str | None:
        return await self._prefs.get(key)

    async def set(self, key: str, value: str) -> None:
        await self._prefs.set(key, value)
```

---

### Entry Points

```python
# main.py
import flet as ft
from app import create_app

if __name__ == "__main__":
    ft.run(create_app)

# app.py — Declarative mode
import flet as ft
from core.config import AppConfig
from data.sources.api_source import ApiSource
from data.repositories.user_repository_impl import UserRepositoryImpl
from domain.usecases.login_usecase import LoginUseCase
from presentation.state_management.global_state import GlobalState
from presentation.themes.app_theme import light_theme

def create_app(page: ft.Page):
    # Configuration
    config = AppConfig()
    page.title = "My App"
    page.theme = light_theme()

    # Data layer
    api = ApiSource(config.api_url)
    user_repo = UserRepositoryImpl(api)

    # Domain layer
    login_usecase = LoginUseCase(user_repo, auth_service)

    # State
    state = GlobalState()

    # Context and render
    ctx = AppContext(state=state, login_usecase=login_usecase)
    page.render_views(App, ctx)
```

---

## Entry Point Pattern — Flutter Parallel

The `main.py` + `app.py` separation follows the same convention used in Flutter projects:

| Flutter | Flet | Role |
|---------|------|------|
| `lib/main.dart` | `main.py` | Entry point: `runApp()` / `ft.run()`, global initialization |
| `lib/app.dart` | `app.py` | Root configuration: theme, routes, DI wiring, context setup |

**Both files live at the project root, outside of `src/`.** They are bootstrap/orchestration files that connect all layers — they don't belong to any single layer.

### `main.py` — Entry Point

Minimal file. Initializes global services and calls `ft.run()`.

```python
# main.py
import flet as ft
from app import create_app
from src.core.logger import configure_logging

configure_logging()

if __name__ == "__main__":
    ft.run(create_app, assets_dir="assets")
```

### `app.py` — Application Configuration

Equivalent to Flutter's `MaterialApp`. Configures:
- Theme (light/dark)
- Routing and navigation
- Dependency injection (state, services, repositories)
- Context provider setup
- `page.render_views()` call with the root layout component

```python
# app.py
import flet as ft
from src.presentation.app import RootLayout
from src.presentation.state_management.global_state import AppState

def create_app(page: ft.Page):
    page.title = "My App"
    page.theme = light_theme()
    page.dark_theme = dark_theme()

    state = AppState()
    # ... wire dependencies, handlers, routing ...

    ctx = AppContext(state=state, ...)
    page.render_views(RootLayout, ctx, state, services)
```

---

## Layout Patterns — Scaffold Strategy

In Flutter, each screen typically includes its own `Scaffold` (appbar + drawer + body + FAB). When multiple screens share the same shell, two patterns exist:

### Pattern 1: Scaffold Per Screen (Flutter Default)

Each page defines its own Scaffold. Best when screens have different appbars, drawers, or FABs.

```
presentation/
 ├── pages/
 │    ├── home/
 │    │    └── home_page.py      ← includes its own header, drawer, FAB
 │    └── settings/
 │         └── settings_page.py  ← includes its own header, no drawer
 └── components/
```

```python
# presentation/pages/home/home_page.py
@ft.component
def HomePage() -> ft.View:
    return ft.View(
        controls=[AppHeader(), HomeContent()],
        drawer=AppDrawer(),
        floating_action_button=MyFAB(),
    )
```

### Pattern 2: Shared Layout (Common in Larger Apps)

A root layout component wraps all pages with a common structure. Best when most screens share the same header, drawer, and FAB.

```
presentation/
 ├── app.py                  ← root layout: View + shared drawer + header + content slot
 ├── pages/
 │    ├── home/
 │    │    └── home_page.py  ← only the content, no scaffold
 │    └── settings/
 │         └── settings_page.py
 └── components/
```

```python
# presentation/app.py — Root layout (shared Scaffold)
@ft.component
def App(ctx_value, state, services=None) -> ft.View:
    return ft.View(
        controls=[
            AppHeader(...),
            ft.Container(content=PageContent(), expand=True),
        ],
        drawer=AppDrawer(...),
        floating_action_button=BmcButton(),
        services=services or [],
    )
```

### When to Use Each Pattern

| Scenario | Recommended Pattern |
|----------|-------------------|
| Screens with different appbars/drawers | Per Screen |
| Most screens share the same shell | Shared Layout |
| Mix of shared and unique layouts | Shared Layout + per-screen overrides |
| Small app (1-3 pages) | Per Screen (simpler) |
| Large app (5+ pages, same shell) | Shared Layout |

---

## Dependency Flow

```
presentation/ → domain/ ← data/
     ↓              ↑         ↓
  services/      entities   sources/
     ↓                        ↓
   core/ ←←←←←←←←←←←←←←← core/
```

**Rules:**
- `domain/` depends on **nothing** (pure Python)
- `data/` implements `domain/` interfaces
- `presentation/` uses `domain/` entities and use cases
- `services/` provides infrastructure to `data/` and `presentation/`
- `core/` is shared by all layers

---

## When to Use This Architecture

| Project Size | Recommendation |
|-------------|----------------|
| Prototype / single page | Skip — use flat `main.py` |
| Small app (2-5 pages) | Use `presentation/` + `core/` only |
| Medium app (5-15 pages) | Full structure without `domain/` |
| Large app / team project | Full clean architecture |

---

## Key Principles

1. **Dependency inversion** — `domain/` defines interfaces, `data/` implements them
2. **Separation of concerns** — UI knows nothing about API calls or database
3. **Testability** — Each layer can be tested independently with mocks
4. **Feature folders** — Pages grouped by feature (`auth/`, `home/`), not by type
5. **No Flet in domain** — Business rules are framework-independent
