# Admin Architecture

Root path: `young_sia_admin/resources/js/`

```text
young_sia_admin/resources/js/
├── vueapp.js                # Entry point, plugin registration
├── components/              # Reusable components
│   ├── image/               # ThumbViewer, MultipleThumbViewer
│   ├── comment/             # XComment, XCommentItem
│   ├── modal/               # NotifyModal
│   └── sidebar/             # MenuItem, MenuItemCargoesStatistic
├── views/                   # Page-level components (feature modules)
│   └── {feature}/
│       ├── Index.vue
│       └── components/      # Feature-specific sub-components
├── store/
│   ├── index.js             # Root store with global state
│   └── modules/             # Namespaced Vuex modules
├── router/
│   ├── index.js             # Main router with guards
│   └── {feature}.js         # Modular route files
├── config/
│   └── index.js             # API endpoint definitions
├── locale/
│   ├── i18n.js
│   └── {en,km,zh}/          # Translation files per module
├── utils/
│   ├── http.js              # Axios instance with interceptors
│   ├── helpers.js           # Global mixin with utilities
│   └── utils.js             # Device token, server base
├── constants/               # Application constants
└── plugins/                 # Vue plugins, FontAwesome
```
