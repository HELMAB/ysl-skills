# Routing

## Router Setup with Role-Based Access

```javascript
// router/index.js
import { createRouter, createWebHistory } from "vue-router";
import { useAuthStore } from "@/store/auth";
import { USER_ROLE } from "@/config/constants";
import { $cookies } from "@/config/cookies";

import catalog from "./catalog";
import order from "./order";
import account from "./account";
import dashboard from "./dashboard";
import reports from "./reports";
import system from "./system";

const routes = [
  {
    path: "/",
    name: "home",
    component: DefaultLayout,
    redirect: "/dashboard",
    meta: {
      title: "common.pageTitles.home",
      requiresAuth: true,
      requiresRole: [USER_ROLE.SELLER, USER_ROLE.SELLER_EDITOR],
    },
    children: [account, order, catalog, system, dashboard, reports],
  },
  {
    path: "/login",
    name: "login",
    meta: { title: "common.pageTitles.login", requiresAuth: false },
    component: () => import("@/views/Login.vue"),
  },
  {
    path: "/:pathMatch(.*)*",
    name: "not_found",
    component: () => import("@/views/404.vue"),
  },
];

const router = createRouter({
  history: createWebHistory(process.env.BASE_URL),
  routes,
  scrollBehavior() {
    return { top: 0 };
  },
});
```

## Navigation Guard

```javascript
const checkRouteAccess = (to, userRoleId) => {
  for (const record of to.matched) {
    if (record.meta?.requiresRole) {
      if (!record.meta.requiresRole.includes(userRoleId)) {
        return false;
      }
    }
  }
  return true;
};

router.beforeEach(async (to, from, next) => {
  const authStore = useAuthStore();
  const { user } = authStore;
  const accessToken = $cookies.get("access_token");

  if (!user && accessToken) {
    // Fetch user data and validate access
    await authStore
      .getUserData()
      .then(({ data }) => {
        authStore.setUserData(data.data);
        if (checkRouteAccess(to, data.data.user.user_role_id)) {
          to.name === "login" ? next({ name: authStore.defaultRoute() }) : next();
        } else {
          next({ name: "unauthorized" });
        }
      })
      .catch(() => next({ name: "not_found" }));
  } else if (!authStore.isAuthenticated) {
    to.name !== "login" ? next({ name: "login" }) : next();
  } else {
    checkRouteAccess(to, user.user_role_id) ? next() : next({ name: "unauthorized" });
  }
});
```

## Modular Route Files

```javascript
// router/catalog.js
import { USER_ROLE } from "@/config/constants";

export default {
  path: "/catalog",
  name: "catalog",
  component: () => import("@/views/catalog/Index.vue"),
  redirect: "/catalog/products",
  meta: {
    title: "common.pageTitles.catalog",
    requiresAuth: true,
    requiresRole: [USER_ROLE.SELLER, USER_ROLE.SELLER_EDITOR],
  },
  children: [
    {
      path: "products",
      component: () => import("@/views/catalog/products/Index.vue"),
      children: [
        {
          path: "",
          name: "list_products",
          meta: { breadcrumb: "list", title: "common.pageTitles.product_list" },
          component: () => import("@/views/catalog/products/List.vue"),
        },
        {
          path: "create",
          name: "create_products",
          meta: { title: "common.pageTitles.product_create" },
          component: () => import("@/views/catalog/products/Create.vue"),
        },
        {
          path: ":id",
          name: "show_products",
          component: () => import("@/views/catalog/products/Show.vue"),
        },
        {
          path: ":id/edit",
          name: "edit_products",
          component: () => import("@/views/catalog/products/Edit.vue"),
        },
      ],
    },
    {
      path: "attributes",
      name: "group_attributes",
      component: () => import("@/views/catalog/attributes/Index.vue"),
    },
  ],
};
```

## Roles

| Role | ID | Access |
|------|----|--------|
| SELLER | 8 | Full seller portal access |
| SELLER_EDITOR | 14 | Product catalog only |

## Sidebar Menu (Role-Based)

```javascript
// config/sidebarMenuItems.js
import { USER_ROLE } from "./constants";

export default [
  {
    component: "CNavItem",
    name: "sidebar.dashboard",
    to: "/dashboard",
    icon: "cil-speedometer",
    requiresRole: [USER_ROLE.SELLER],
  },
  {
    component: "CNavGroup",
    name: "sidebar.catalog",
    to: "/catalog",
    icon: "cil-inbox",
    requiresRole: [USER_ROLE.SELLER, USER_ROLE.SELLER_EDITOR],
    items: [
      { component: "CNavItem", name: "sidebar.products", to: "/catalog/products" },
      { component: "CNavItem", name: "sidebar.categories", to: "/catalog/categories" },
    ],
  },
];
```
