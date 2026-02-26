# ysl-skills

AI agent skills for the Youngsia project ecosystem. Provides context-aware coding assistance for a multi-app logistics/e-commerce platform.

## Available Skills

| Skill | Description |
|-------|-------------|
| **laravel-api** | Laravel 12 + PHP 8.3 API: JWT auth, repository pattern, versioned controllers |
| **vue-admin** | Vue 2 + Bootstrap-Vue admin panel: Vuex 3, component patterns, forms |
| **vue-seller-ui** | Vue 3 + CoreUI seller portal: Pinia, Composition API, composables |
| **design-system** | Brand standards, CSS conventions, component structure |
| **acl** | Role-based access control: 14 roles, middleware, route guards |
| **i18n** | Three-language support (en, km, zh): vue-i18n, locale patterns |

## Installation

```bash
claude skill install /path/to/ysl-skills
```

Or add to your project's `.claude/settings.json`:

```json
{
  "skills": ["/path/to/ysl-skills"]
}
```

## Platform Overview

The Youngsia ecosystem consists of:

- **Laravel API** (`young_sia_api/`) — Backend REST API with JWT auth
- **Vue 2 Admin** (`young_sia_admin/`) — Back-office management panel
- **Vue 3 Seller UI** (`young_sia_seller_ui_source/`) — Seller/editor portal

## License

MIT
