# ysl-skills

AI agent skills for the Youngsia project ecosystem. Provides context-aware coding assistance for a multi-app logistics/e-commerce platform.

## Available Skills

| Skill | Description |
|-------|-------------|
| **laravel-api** | Laravel 12 + PHP 8.3 API patterns: JWT auth, repository pattern, versioned controllers, Form Requests, API Resources, constants |
| **vue-admin** | Vue 2 + Bootstrap-Vue admin panel: Vuex 3 modules, component patterns, Vue Router 3, Vee-Validate 3 forms |
| **vue-seller-ui** | Vue 3 + CoreUI seller portal: Composition API, Pinia stores, composables, Vue Router 4 with role-based access |
| **design-system** | Brand standards: primary color #129748, CSS conventions, component structure patterns for admin and seller apps |
| **acl** | Access control: 14 predefined roles, role-based middleware, frontend route guards, permission enforcement |
| **i18n** | Three-language internationalization (en, km, zh): JSON locale files, vue-i18n 8/9 setup, translation patterns |

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
/plugin install create-adaptable-composable@ysl-skills

# Install multiple skills
/plugin install create-adaptable-composable@ysl-skills vue-best-practices@ysl-skills
```

## Platform Overview

The Youngsia ecosystem consists of:

- **Laravel API** (`young_sia_api/`) — Backend REST API with JWT auth
- **Vue 2 Admin** (`young_sia_admin/`) — Back-office management panel
- **Vue 3 Seller UI** (`young_sia_seller_ui_source/`) — Seller/editor portal

## License

MIT
