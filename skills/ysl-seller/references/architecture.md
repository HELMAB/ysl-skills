# Seller UI Architecture

Root path: `young_sia_seller_ui_source/src/`

```text
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
│   ├── useCommon.js             # Utility functions
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
│   ├── upload.js                # Video upload with progress
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
