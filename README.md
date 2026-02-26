# E-commerce Skills

AI agent skills for the E-commerce project ecosystem. Provides context-aware coding assistance for a multi-app logistics/E-commerce platform.

## Available Skills

| Skill | Description |
|-------|-------------|
| **ysl-api** | Laravel 12 + PHP 8.3 API patterns: JWT auth, repository pattern, versioned controllers, Form Requests, API Resources, jobs, services, observers |
| **ysl-admin** | Vue 2 + Bootstrap-Vue admin panel: Vuex 3 modules, component patterns, Vue Router 3, Vee-Validate 3 forms |
| **ysl-seller** | Vue 3 + CoreUI seller portal: Composition API, Pinia stores, composables, Vue Router 4 with role-based access, form validation |
| **ysl-web** | Laravel 12 public website: API gateway pattern, Guzzle HTTP client, Blade templates, device token management |
| **ysl-design-system** | Brand standards: primary color #129748, CSS conventions, component structure patterns for admin and seller apps |
| **ysl-i18n** | Three-language internationalization (en, km, zh): JSON locale files, vue-i18n 8/9 setup, translation patterns |

## Installation

```bash
claude skill install helmab/ysl-skills
```

## Claude Code Marketplace

An alternative for Claude Code users:

```sh
# Add marketplace
/plugin marketplace add helmab/ysl-skills

# Install individual skills
/plugin install ysl-admin@ysl-skills

# Install multiple skills
/plugin install ysl-admin@ysl-skills ysl-seller@ysl-skills ysl-api@ysl-skills ysl-web@ysl-skills ysl-i18n@ysl-skills
```

## Platform Overview

The E-commerce ecosystem consists of:

- **YSL API** (`young_sia_api/`) — Backend REST API with JWT auth
- **YSL Admin** (`young_sia_admin/`) — Back-office management panel
- **YSL Seller** (`young_sia_seller_ui_source/`) — Seller/editor portal
- **YSL Web** (`young_sia_web/`) — Public-facing website

## License

MIT
