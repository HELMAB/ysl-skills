# API Architecture

Root path: `young_sia_api/`

```text
young_sia_api/
├── app/
│   ├── Constants/          # Class-based constants (ConstUserRole, ConstOrderStatus)
│   ├── Exceptions/         # Custom exceptions with render()
│   ├── Facades/            # AppHelper, AppJwt, PushHelper
│   ├── Helpers/            # Global helper functions
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── BaseController.php
│   │   │   └── V1_0/       # Versioned controllers
│   │   │       ├── Back_Office/
│   │   │       └── Mobile/
│   │   ├── Middleware/     # device.token, auth.token, user.role
│   │   ├── Requests/       # Form Requests extending BaseRequest
│   │   └── Resources/      # API Resource transformations
│   ├── Models/             # Eloquent models with Transformable
│   ├── Repositories/       # Interface + Eloquent implementation pairs
│   ├── Services/           # Business logic services
│   └── Traits/             # Reusable traits
├── config/
└── routes/
    └── api/
        ├── api_version.php
        └── v1_0/            # Versioned route files
            ├── back_office/
            └── mobile/
```
