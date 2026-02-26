# Vue Seller UI Skill

You are working on the **E-commerce Seller Portal** — a Vue 3 + CoreUI application for sellers/editors to manage products, orders, and shop settings.

## Tech Stack

- Vue 3.2.45, Vue Router 4.1.6, Pinia 3.0.2
- CoreUI Vue 4.5.0 / CoreUI Pro 5.2.0
- Vee-Validate 4.9.6
- vue-i18n 9.2.2 (Composition API mode)
- Axios 1.13.2, date-fns, SweetAlert2, Firebase

## Architecture Overview

```
young_sia_seller_ui_source/src/
├── App.vue                      # Root with <script setup>
├── main.js                      # Pinia, Router, i18n, CoreUI setup
├── components/
│   ├── Layouts/                 # DefaultLayout, AppHeader, AppSidebar, AppFooter
│   ├── Utils/                   # Auto-registered App*.vue components
│   ├── common/                  # Shared components
│   └── Svgs/                    # SVG icon components
├── composables/                 # Reusable composition functions
│   ├── useAxios.js
│   ├── useCommon.js             # 20+ utility functions
│   ├── useNotify.js             # Toast notifications
│   ├── useFirebase.js           # FCM push notifications
│   ├── usePageTitle.js
│   └── usePaymentStatus.js
├── config/
│   ├── api/                     # API endpoint definitions per module
│   ├── constants/               # USER_ROLE, ORDER_STATUS enums
│   ├── axios.js                 # Interceptors (X-Access-Token, X-Platform)
│   ├── i18n.js                  # vue-i18n 9 setup
│   └── sidebarMenuItems.js      # Role-based navigation
├── locales/{en,km,zh}/          # Translation files per feature
├── plugins/
│   └── globalComponents.js      # Auto-register App*.vue from Utils/
├── router/
│   ├── index.js                 # Guards, role-based access
│   └── {catalog,order,...}.js   # Modular route files
├── store/                       # Pinia stores
│   ├── auth.js                  # User, token, shop
│   ├── product.js               # Product CRUD state
│   ├── upload.js                # Video upload with progress (Composition API)
│   ├── loading.js               # Global loading flag
│   └── coreui.js                # Sidebar state
├── utils/                       # Standalone utilities
└── views/                       # Page components
    ├── catalog/products/
    │   ├── List.vue
    │   ├── Create.vue
    │   ├── Show.vue
    │   ├── Edit.vue
    │   └── components/          # ProductList, ProductFilter, etc.
    ├── orders/
    ├── account/
    ├── dashboard/
    └── reports/
```

## Workflow

When building a new feature:

1. **Create Pinia store** → `store/{feature}.js`
2. **Add API endpoints** → `config/api/{feature}.js` + register in `config/api/index.js`
3. **Create page views** → `views/{feature}/List.vue`, `Create.vue`, etc.
4. **Create sub-components** → `views/{feature}/components/`
5. **Add routes** → `router/{feature}.js` + import in `router/index.js`
6. **Add translations** → `locales/{en,km,zh}/{feature}.json`
7. **Add sidebar item** → `config/sidebarMenuItems.js` with `requiresRole`

## References

- @references/components.md — Composition API, CoreUI, `<script setup>` patterns
- @references/store.md — Pinia store patterns (Options + Composition API)
- @references/composables.md — Reusable composables
- @references/routing.md — Vue Router 4, role-based access
- @references/forms.md — Form validation with server-side errors
