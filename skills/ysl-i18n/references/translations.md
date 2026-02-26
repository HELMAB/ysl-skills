# Translations

## Locale File Structure

Both frontend apps organize translations per feature module:

```
locale(s)/
├── en/
│   ├── common.json       # Shared strings, buttons, labels
│   ├── account.json      # Auth, profile
│   ├── product.json      # Product-specific
│   ├── order.json        # Order-specific
│   ├── sidebar.json      # Navigation labels
│   ├── button.json       # Button text (seller UI)
│   └── ... (per feature)
├── km/                   # Khmer translations
│   ├── common.json
│   └── ...
└── zh/                   # Chinese translations
    ├── common.json
    └── ...
```

## Key Conventions

- **Dot notation** for nested keys: `common.button.create`
- **Feature-scoped modules**: `product.field.name`, `order.status.received`
- **Consistent naming**: Same key structure across all three languages

## JSON Structure Example

```json
// locales/en/common.json
{
  "hello": "Welcome to the E-commerce team",
  "account": "Account",
  "app_name": "E-commerce Purchase",
  "fields": {
    "label": {
      "creation_date": "Creation Date",
      "current_password": "Current Password",
      "email": "Email",
      "gender": "Gender",
      "name": "Name",
      "shop_name": "Shop name",
      "address": "Address",
      "exchange_rate": "Exchange rate"
    },
    "placeholder": {
      "password": "Password",
      "username": "Email/Phone Number",
      "email": "Email",
      "phone": "Phone Number"
    },
    "validation": {
      "required": "The field is required",
      "at_least_one_image_required": "At least one image is required"
    }
  },
  "pageTitles": {
    "home": "Home",
    "dashboard": "Dashboard",
    "product_list": "Product List",
    "product_create": "Create Product"
  },
  "are_you_sure": "Are you sure?",
  "are_you_sure_detail": "You won't be able to revert this!",
  "are_you_sure_confirmed": "Yes, confirm!"
}
```

## vue-i18n 8 Setup (Admin Panel)

```javascript
// locale/i18n.js
import Vue from "vue";
import VueI18n from "vue-i18n";

Vue.use(VueI18n);

function loadLocaleMessages() {
  const locales = require.context("../locale", true, /[A-Za-z0-9-_,\s]+\.json$/i);
  const messages = {};

  locales.keys().forEach((key) => {
    const matched = key.match(/([A-Za-z0-9-_]+)\//i);
    const matchedModule = key.match(/([A-Za-z0-9-_]+)\./i);

    if (matched && matchedModule) {
      const locale = matched[1];
      const module = matchedModule[1];
      if (!messages[locale]) messages[locale] = {};
      messages[locale][module] = locales(key);
    }
  });

  return messages;
}

const i18n = new VueI18n({
  locale: Vue.$cookies.isKey("locale") ? Vue.$cookies.get("locale") : "en",
  fallbackLocale: "en",
  messages: loadLocaleMessages() || [],
});

export default i18n;
```

## vue-i18n 9 Setup (Seller Portal)

```javascript
// config/i18n.js
import { createI18n } from "vue-i18n";

function loadLocaleMessages() {
  const locales = require.context("@/locales", true, /[A-Za-z0-9-_,\s]+\.json$/i);
  const messages = {};

  locales.keys().forEach((key) => {
    const matched = key.match(/([A-Za-z0-9-_]+)\//i);
    const matchedModule = key.match(/([A-Za-z0-9-_]+)\./i);

    if (matched && matchedModule) {
      const locale = matched[1];
      const module = matchedModule[1];
      if (!messages[locale]) messages[locale] = {};
      messages[locale][module] = locales(key);
    }
  });

  return messages;
}

const i18n = createI18n({
  legacy: false, // Composition API mode
  locale: "en",
  fallbackLocale: "en",
  messages: loadLocaleMessages(),
});

export default i18n;
```

## Usage in Templates

### Vue 2 (Options API)

```vue
<template>
  <div>
    <label>{{ $t("account.input.phone_number") }}</label>
    <span>{{ $t("user.header.summary.title") }}</span>

    <!-- Locale switching -->
    <a @click="setLocale('en')">EN</a>
    <a @click="setLocale('km')">KM</a>
    <a @click="setLocale('zh')">ZH</a>
  </div>
</template>
```

### Vue 3 (Composition API)

```vue
<template>
  <h1>{{ $t("common.app_name") }}</h1>
  <p>{{ $t("common.fields.label.name") }}</p>
</template>

<script setup>
import { useI18n } from "vue-i18n";

const { locale, t } = useI18n();

// Programmatic translation
const title = t("common.app_name");

// Switch locale
locale.value = "km";
</script>
```

## Laravel Backend Translations

```php
// Using __() helper
return response()->json([
    'error' => ['message' => __('auth.validation.phone.required')],
]);

// Translation files in resources/lang/{locale}/
// resources/lang/en/auth.php
return [
    'validation' => [
        'phone' => [
            'required' => 'Phone number is required.',
        ],
    ],
];
```

## Adding Translations for a New Feature

1. Create `locales/en/{feature}.json` with all keys
2. Copy to `locales/km/{feature}.json` and translate
3. Copy to `locales/zh/{feature}.json` and translate
4. Keys are auto-loaded via `require.context` — no manual registration needed
5. Access via `$t('feature.key.subkey')` in templates

## Locale Persistence

- **Admin**: Stored in cookie via `vue-cookies`, read on init
- **Seller**: Stored in cookie, fallback to `"en"`
- **API**: Read from `X-Locale` header, used for error messages
