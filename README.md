# YSL Skills

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GitHub stars](https://img.shields.io/github/stars/helmab/ysl-skills)](https://github.com/helmab/ysl-skills/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/helmab/ysl-skills)](https://github.com/helmab/ysl-skills/network/members)

AI agent skills for the YSL project ecosystem. Provides context-aware coding assistance for a multi-app logistics/e-commerce platform.

## Available Skills

### Development Skills

| Skill | Description | Requires |
|-------|-------------|----------|
| **ysl-api** | Laravel 12 + PHP 8.3 API patterns: JWT auth, repository pattern, versioned controllers, Form Requests, API Resources, jobs, services, observers | ysl-i18n |
| **ysl-admin** | Vue 2 + Bootstrap-Vue admin panel: Vuex 3 modules, component patterns, Vue Router 3, Vee-Validate 3 forms | ysl-design-system, ysl-i18n |
| **ysl-seller** | Vue 3 + CoreUI seller portal: Composition API, Pinia stores, composables, Vue Router 4 with role-based access, form validation | ysl-design-system, ysl-i18n |
| **ysl-web** | Laravel 12 public website: API gateway pattern, Guzzle HTTP client, Blade templates, device token management | — |
| **ysl-feature** | Comprehensive guide for implementing new features across database, backend API, and frontend admin | ysl-api, ysl-admin, ysl-i18n, ysl-design-system |

### Foundation Skills

| Skill | Description |
|-------|-------------|
| **ysl-design-system** | Brand standards: primary color #129748, CSS conventions, component structure patterns for admin and seller apps |
| **ysl-i18n** | Three-language internationalization (en, km, zh): JSON locale files, vue-i18n 8/9 setup, translation patterns |

### Code Review Skills

| Skill | Description |
|-------|-------------|
| **ysl-code-reviewer** | Comprehensive code review agent: compares current branch against master, identifies bugs, security vulnerabilities, performance issues |

## Skill Structure

Each skill follows a consistent structure:

```
skills/{skill-name}/
├── SKILL.md              # Main entry point with overview and references
└── references/           # Detailed documentation files
    ├── *.md             # Specific implementation guides
```

**Benefits:**
- **Concise main files** — SKILL.md stays under 100 lines with clear overview
- **Detailed references** — Complex patterns and examples in separate files
- **Easy navigation** — Reference files are self-contained and focused
- **Real code examples** — Actual code from the project, not generic examples

## Installation

```bash
claude skill install helmab/ysl-skills
```

## Claude Code Marketplace

An alternative for Claude Code users:

```sh
# Add marketplace
/plugin marketplace add helmab/ysl-skills

# Install for API development (includes i18n)
/plugin install ysl-api@ysl-skills ysl-i18n@ysl-skills

# Install for Admin development (includes design system + i18n)
/plugin install ysl-admin@ysl-skills ysl-design-system@ysl-skills ysl-i18n@ysl-skills

# Install for Seller development (includes design system + i18n)
/plugin install ysl-seller@ysl-skills ysl-design-system@ysl-skills ysl-i18n@ysl-skills

# Install for new feature development
/plugin install ysl-feature@ysl-skills ysl-api@ysl-skills ysl-admin@ysl-skills ysl-i18n@ysl-skills ysl-design-system@ysl-skills

# Install code reviewer
/plugin install ysl-code-reviewer@ysl-skills

# Install all skills
/plugin install ysl-api@ysl-skills ysl-admin@ysl-skills ysl-seller@ysl-skills ysl-web@ysl-skills ysl-feature@ysl-skills ysl-code-reviewer@ysl-skills ysl-design-system@ysl-skills ysl-i18n@ysl-skills
```

## Usage Guide

### When to Use Each Skill

**Development Workflows:**

- **Working on Backend API?** → Use `ysl-api`
  - Creating models, repositories, controllers, API resources
  - Implementing JWT authentication
  - Writing Form Requests and validation
  - Following Laravel 12 + PHP 8.3 best practices

- **Building Admin UI?** → Use `ysl-admin`
  - Creating Vue 2 components with Options API
  - Setting up Vuex modules and actions
  - Implementing forms with Vee-Validate
  - Building data tables and CRUD interfaces

- **Developing Seller Portal?** → Use `ysl-seller`
  - Creating Vue 3 components with Composition API
  - Managing state with Pinia stores
  - Writing composables and utilities
  - Implementing role-based access control

- **Building Public Website?** → Use `ysl-web`
  - Creating Blade templates
  - Implementing API gateway patterns with Guzzle
  - Managing device tokens
  - Frontend-backend integration

- **Implementing Full Features?** → Use `ysl-feature`
  - Step-by-step guide from database to frontend
  - Complete CRUD implementation across all layers
  - Ensures consistency across backend and frontend
  - Includes checklist for feature completion
  - Auto-loads `ysl-api`, `ysl-admin`, `ysl-i18n`, and `ysl-design-system`

**Supporting Workflows:**

- **Need Design/UI Guidance?** → Use `ysl-design-system`
  - Brand colors and styling
  - CSS conventions (no scoped styles!)
  - Component structure patterns

- **Adding Translations?** → Use `ysl-i18n`
  - Three-language support (en, km, zh)
  - Translation file patterns
  - Using `$t()` and `__()` helpers

- **Reviewing Code?** → Use `ysl-code-reviewer`
  - Comprehensive branch comparison against master
  - Identifies bugs, security issues, performance problems
  - Provides actionable improvement suggestions
  - Structured review format with severity levels

### Quick Start Examples

**Example 1: Adding a New API Endpoint**
```bash
# Install API skill
/plugin install ysl-api@ysl-skills ysl-i18n@ysl-skills

# Then in your code editor:
# "Create a new User Profile API endpoint with CRUD operations"
```

**Example 2: Building a New Admin Page**
```bash
# Install admin and design skills
/plugin install ysl-admin@ysl-skills ysl-design-system@ysl-skills ysl-i18n@ysl-skills

# Then in your code editor:
# "Create an admin page for managing products with list, create, edit views"
```

**Example 3: Implementing a Complete Feature**
```bash
# Install feature skill (includes api, admin, i18n, and design guidance)
/plugin install ysl-feature@ysl-skills ysl-api@ysl-skills ysl-admin@ysl-skills ysl-i18n@ysl-skills ysl-design-system@ysl-skills

# Then in your code editor:
# "Implement a complete order management feature from database to UI"
```

**Example 4: Code Review Before Merge**
```bash
# Install code reviewer
/plugin install ysl-code-reviewer@ysl-skills

# Then in your code editor:
# "Review my current branch changes against master"
```

## Platform Overview

The YSL project ecosystem consists of:

- **young_sia_api** — Backend REST API with JWT auth (Laravel 12 + PHP 8.3)
- **young_sia_admin** — Back-office management panel (Vue 2 + Bootstrap Vue)
- **young_sia_seller_ui_source** — Seller/editor portal (Vue 3 + CoreUI)
- **young_sia_web** — Public-facing website (Laravel 12)
- **young_sia_web_document** — Database schema and PostgreSQL functions

## Contributing

See [@AGENTS.md](AGENTS.md) for contribution guidelines and skill writing best practices.

## License

MIT
