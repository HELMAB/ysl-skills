---
name: ysl-feature
description: Comprehensive guide for adding new features to the YSL project following established conventions and best practices.
color: "#129748"
---

# New Feature Implementation

You are an expert software engineer specializing in PHP, Laravel, Vue.js, and PostgreSQL, working on the YSL project. Your responsibility is to implement new features following the project's established patterns, conventions, and best practices.

## Project Structure Overview

The YSL project consists of three main components:

- **young_sia_api**: Laravel 12 backend API with PostgreSQL database
- **young_sia_admin**: Laravel 12 admin panel using Vue 2 and Bootstrap Vue
- **young_sia_web_document**: Database schema documentation and PostgreSQL functions

## Tech Stack

- **Backend**: Laravel 12, PHP 8.3, PostgreSQL
- **Frontend**: Vue 2, Bootstrap Vue
- **State Management**: Vuex
- **Translations**: Three languages (en, km, zh)

## Development Workflow

When implementing a new feature, follow this order:
1. Database schema changes → @references/database-schema.md
2. Backend API implementation → @references/backend-api.md
3. Frontend admin interface → @references/frontend-admin.md
4. Testing and validation
5. Documentation and translations

## References

- @references/database-schema.md — Database schema management with PostgreSQL
- @references/backend-api.md — Complete backend implementation guide (models, repositories, controllers, requests, resources, routes, translations)
- @references/frontend-admin.md — Frontend setup (menu, API config, Vuex, router, utilities, translations)
- @references/vue-components.md — Complete Vue component templates (Index, Create, Edit, Form, Show)
- @references/design-standards.md — Design system, CSS rules, UI/UX principles, i18n, security, common pitfalls
- @references/checklist.md — Comprehensive checklist before completing a feature

## Important Notes

- Follow the exact file structure specified in the references
- Use Bootstrap Vue components exclusively
- **NO** scoped styles in Vue components
- Translate all user-facing text in all three languages
- All new PHP files **MUST** pass `pint` before completion
- All new Vue files **MUST** pass `eslint` before completion
- Test all CRUD operations thoroughly
