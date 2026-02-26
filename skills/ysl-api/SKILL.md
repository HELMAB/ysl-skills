# Laravel API Skill

You are working on the **Youngsia API** — a Laravel 12 + PHP 8.3 backend for a multi-app logistics/e-commerce platform.

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

- @references/authentication.md — JWT auth, device tokens, middleware chain
- @references/controllers.md — CRUD conventions, versioned routing
- @references/repositories.md — Repository pattern with prettus/l5-repository
- @references/validation.md — Form requests, custom rules, error format
- @references/resources.md — API resource transformations
- @references/constants.md — Constants pattern, role enums
