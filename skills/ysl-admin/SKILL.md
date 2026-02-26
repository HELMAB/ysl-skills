---
name: ysl-admin
description: Vue 2 + Bootstrap-Vue admin panel: Vuex 3 modules, component patterns, Vue Router 3, Vee-Validate 3 forms.
---

# Vue Admin Skill

You are working on the **E-commerce Admin Panel** — a Vue 2 + Bootstrap-Vue back-office application for logistics/e-commerce management.

## Required Skills

This skill requires **ysl-design-system** for brand colors, CSS conventions, and component structure patterns. It also requires **ysl-i18n** for three-language internationalization (en, km, zh). Always follow these guidelines when building UI components.

## Tech Stack

- Vue 2.5.17, Vue Router 3.3.4, Vuex 3.5.1
- Bootstrap Vue 2.15.0
- Vee-Validate 3.3.7
- Axios 0.19
- vue-i18n 8.20.0
- ApexCharts, TinyMCE 3, Moment.js

## Architecture Overview

```
young_sia_admin/resources/js/
├── vueapp.js                # Entry point, plugin registration
├── components/              # Reusable components
│   ├── image/               # ThumbViewer, MultipleThumbViewer
│   ├── comment/             # XComment, XCommentItem
│   ├── modal/               # NotifyModal
│   └── sidebar/             # MenuItem, MenuItemCargoesStatistic
├── views/                   # Page-level components (70+ feature modules)
│   └── {feature}/
│       ├── Index.vue
│       └── components/      # Feature-specific sub-components
├── store/
│   ├── index.js             # Root store with global state
│   └── modules/             # Namespaced Vuex modules (60+)
├── router/
│   ├── index.js             # Main router with guards
│   └── {feature}.js         # Modular route files (40+)
├── config/
│   └── index.js             # API endpoint definitions (100+)
├── locale/
│   ├── i18n.js
│   └── {en,km,zh}/          # Translation files per module
├── utils/
│   ├── http.js              # Axios instance with interceptors
│   ├── helpers.js            # Global mixin with utilities
│   └── utils.js             # Device token, server base
├── constants/               # Application constants (18 files)
└── plugins/                 # Vue plugins, FontAwesome
```

## Workflow

When building a new feature:

1. **Create Vuex module** → `store/modules/{feature}.js`
2. **Create view component** → `views/{feature}/Index.vue`
3. **Create sub-components** → `views/{feature}/components/`
4. **Add routes** → `router/{feature}.js` + register in `router/index.js`
5. **Add API config** → `config/index.js` under `api.paths`
6. **Add translations** → `locale/{en,km,zh}/{feature}.json`

## References

- @references/components.md — Component structure, naming, view patterns
- @references/store.md — Vuex 3 module patterns, root state
- @references/routing.md — Vue Router 3, auth guards, modular routes
- @references/forms.md — Vee-Validate 3, form validation patterns
- @ysl-design-system — Brand colors, CSS conventions (REQUIRED)
- @ysl-i18n — Three-language internationalization (REQUIRED)
