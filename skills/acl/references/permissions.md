# Permissions

## Backend: Role Constants

```php
// app/Constants/ConstUserRole.php
<?php

namespace App\Constants;

class ConstUserRole
{
    const ADMIN              = 1;
    const OPERATION          = 2;
    const WAREHOUSE          = 3;
    const USER               = 4;
    const ACCOUNTANT         = 5;
    const PUBLISHER          = 6;
    const DRIVER             = 7;
    const SELLER             = 8;
    const CUSTOMER_SERVICE   = 9;
    const DIGITAL_MARKETING  = 10;
    const DIGITAL_SALE       = 11;
    const PARTNER            = 12;
    const SELLER_STAFF       = 13;
    const SELLER_EDITOR      = 14;
}
```

## Frontend: Role Constants

### Admin Panel (Vue 2)

```javascript
// constants/index.js
export const UserRole = {
  ADMIN: 1,
  OPERATION: 2,
  WAREHOUSE: 3,
  USER: 4,
  // ...
};
```

### Seller Portal (Vue 3)

```javascript
// config/constants/user_role.js
export const USER_ROLE = {
  ADMIN: 1,
  OPERATION: 2,
  WAREHOUSE: 3,
  USER: 4,
  ACCOUNTANT: 5,
  PUBLISHER: 6,
  DRIVER: 7,
  SELLER: 8,
  CUSTOMER_SERVICE: 9,
  DIGITAL_MARKETING: 10,
  DIGITAL_SALE: 11,
  PARTNER: 12,
  SELLER_STAFF: 13,
  SELLER_EDITOR: 14,
};
```

## Role Checking Patterns

### Backend (Controller/Service)

```php
use App\Constants\ConstUserRole;

$user = \AppHelper::getResolve('user');

if ($user->user_role_id == ConstUserRole::ADMIN) {
    // Admin-only logic
}

if (in_array($user->user_role_id, [ConstUserRole::SELLER, ConstUserRole::SELLER_EDITOR])) {
    // Seller access
}
```

### Admin Panel (Vue 2)

```vue
<template>
  <button v-if="isAdmin" class="btn btn-success" @click="create">
    {{ $t("common.button.create") }}
  </button>
</template>

<script>
import { mapGetters } from "vuex";

export default {
  computed: {
    ...mapGetters("user", ["isAdmin", "isWarehouse"]),
  },
};
</script>
```

### Seller Portal (Vue 3)

```vue
<script setup>
import { useAuthStore } from "@/store/auth";

const authStore = useAuthStore();

// Check roles
if (authStore.isSeller()) {
  // Full seller access
}
if (authStore.isSellerEditor()) {
  // Catalog-only access
}
</script>
```

## Role-Based UI Visibility

### Seller Portal Sidebar

```javascript
// Items only shown to specific roles
{
  component: "CNavItem",
  name: "sidebar.dashboard",
  to: "/dashboard",
  requiresRole: [USER_ROLE.SELLER],       // Dashboard: sellers only
},
{
  component: "CNavGroup",
  name: "sidebar.catalog",
  to: "/catalog",
  requiresRole: [USER_ROLE.SELLER, USER_ROLE.SELLER_EDITOR],  // Catalog: both roles
}
```

## Seller Role Hierarchy

| Role | Dashboard | Orders | Products | Settings | Reports |
|------|-----------|--------|----------|----------|---------|
| SELLER (8) | Yes | Yes | Yes | Yes | Yes |
| SELLER_EDITOR (14) | No | No | Yes | No | No |

The `SELLER_EDITOR` role is restricted to product catalog management only. Their default route redirects to `list_products` instead of `dashboard`.
