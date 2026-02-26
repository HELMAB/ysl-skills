---
name: ysl-api
description: "Laravel 12 + PHP 8.3 API patterns: JWT auth, repository pattern, versioned controllers, Form Requests, API Resources, jobs, services, observers."
---

# Laravel API Skill

You are working on the **E-commerce API** — a Laravel 12 + PHP 8.3 backend for a multi-app logistics/e-commerce platform.

## Required Skills

- **ysl-i18n** — Three-language internationalization (en, km, zh). All user-facing messages must use Laravel's `__()` or `trans()` helpers.

## Rules

### General Rules

- Follow best practices of Laravel framework
- Follow the code style of Young Sia API project
- Don't create any markdown files
- Don't ask to write tests
- Must have todo list and mark as completed after completing each point
- After completing a task, write a summary of changes and suggest improvements

## Coding Standards (MUST Follow)

All new PHP code MUST use PHP 8.3+ features and Laravel best practices:

- **Type declarations** — Required on all methods and properties
- **Constructor property promotion** — Preferred for dependency injection
- **Match expressions** — Preferred over switch statements
- **Null safe operator (`?->`)** — Required when accessing nullable properties
- **Return types** — Required on all methods (`JsonResponse`, `array`, `void`, etc.)

See @references/coding-standards.md for complete guidelines with examples.

## Tech Stack

- Laravel 12, PHP 8.3
- JWT Authentication (tymon/jwt-auth ^2.2)
- Repository Pattern (prettus/l5-repository ^2.10)
- Hashids for ID obfuscation
- Intervention Image for media processing

## Architecture Overview

```
young_sia_api/
├── app/
│   ├── Constants/          # Class-based constants (ConstUserRole, ConstOrderStatus)
│   ├── Exceptions/         # Custom exceptions with render()
│   ├── Facades/            # AppHelper, AppJwt, PushHelper
│   ├── Helpers/            # Global helper functions
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── BaseController.php
│   │   │   └── V1_0/      # Versioned controllers
│   │   │       ├── Back_Office/
│   │   │       └── Mobile/
│   │   ├── Middleware/     # device.token, auth.token, user.role
│   │   ├── Requests/       # Form Requests extending BaseRequest
│   │   └── Resources/      # API Resource transformations
│   ├── Models/             # Eloquent models with Transformable
│   ├── Repositories/       # Interface + Eloquent implementation pairs
│   ├── Services/           # Business logic services
│   └── Traits/             # 50+ reusable traits
├── config/
└── routes/
    └── api/
        ├── api_version.php
        └── v1_0/           # Versioned route files
            ├── back_office/
            └── mobile/
```

## Workflow

When building a new feature:

1. **Define constants** if needed → `app/Constants/Const{Name}.php`
2. **Create/update model** → `app/Models/{Name}.php`
3. **Create repository pair** → Interface + Eloquent implementation
4. **Create Form Request** → `app/Http/Requests/{Feature}/{Action}Request.php`
5. **Create API Resource** → `app/Http/Resources/{Feature}/{Name}Resource.php`
6. **Create controller** → `app/Http/Controllers/V1_0/{Name}Controller.php`
7. **Add routes** → `routes/api/v1_0/{feature}.php`
8. **Create migration** → Only `up()` method, no `down()`

## References

- @references/coding-standards.md — PHP 8.3+ and Laravel best practices (MUST follow)
- @references/authentication.md — JWT auth, device tokens, middleware chain
- @references/controllers.md — CRUD conventions, versioned routing
- @references/repositories.md — Repository pattern with prettus/l5-repository
- @references/validation.md — Form requests, custom rules, error format
- @references/resources.md — API resource transformations
- @references/constants.md — Constants pattern, role enums
- @references/services.md — Business logic services
- @references/jobs.md — Queue jobs (115 jobs)
- @references/observers.md — Model event observers (15 observers)
- @ysl-i18n — Three-language internationalization (REQUIRED)
