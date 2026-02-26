# Routing

## Router Setup

```javascript
// router/index.js
import VueRouter from "vue-router";
import AccountSignIn from "./../views/account/SignIn";
import store from "../store";

const routes = [
  {
    path: "/sign-in",
    component: AccountSignIn,
    meta: { requiresAuth: false },
  },
  {
    path: "/users",
    component: UserList,
    meta: { requiresAuth: true },
  },
  {
    path: "/staff",
    component: StaffIndex,
    meta: { requiresAuth: true, roles: ["admin"] },
  },
  // ... 100+ routes
];

const router = new VueRouter({
  mode: "history",
  routes,
  scrollBehavior() {
    return { x: 0, y: 0 };
  },
});

// Auth guard
router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth && !store.getters.getAccessToken) {
    next("/sign-in");
  } else {
    next();
  }
});

export default router;
```

## Modular Route Files

Routes are split across 40+ files:

```
router/
├── index.js
├── user_shipping_fee.js
├── shipping_type.js
├── order.js
├── merchants.js
└── ... (35+ more)
```

Each file exports route configuration that is spread into the main routes array.

## Route Meta

```javascript
{
  path: "/feature",
  component: FeatureView,
  meta: {
    requiresAuth: true,    // Requires access token
    roles: ["admin"],      // Role restriction (optional)
  }
}
```

## Navigation

```javascript
// Programmatic navigation
this.$router.push("/users");
this.$router.push({ name: "userDetail", params: { id: userId } });

// In templates
<router-link to="/users">Users</router-link>
```
