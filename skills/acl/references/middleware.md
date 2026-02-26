# Middleware

## API Route Protection

### Middleware Chain

Routes are protected with a layered middleware stack:

```php
// Public (device validation only)
Route::middleware('device.token');

// Authenticated user
Route::middleware(['device.token', 'auth.token']);

// Optional auth (doesn't fail if no token)
Route::middleware(['device.token', 'auth.check']);

// Role-restricted
use App\Constants\ConstUserRole;
$adminRole = ConstUserRole::ADMIN;
Route::middleware(['device.token', 'auth.token', "user.role:$adminRole"]);
```

### UserRoleMiddleware

Validates that the authenticated user has the required role:

```php
// Usage in routes
Route::middleware(["user.role:" . ConstUserRole::ADMIN]);

// Multiple roles (comma-separated)
Route::middleware(["user.role:" . ConstUserRole::ADMIN . "," . ConstUserRole::OPERATION]);
```

### Route Group Example

```php
<?php

use App\Constants\ConstUserRole;
use App\Http\Controllers\V1_0\Back_Office\UserController;
use Illuminate\Support\Facades\Route;

$adminRole = ConstUserRole::ADMIN;

Route::prefix('back-office')
    ->name('back-office.')
    ->middleware(['device.token', 'auth.token', "user.role:$adminRole"])
    ->group(function () {
        Route::prefix('users')->name('users.')->group(function () {
            Route::get('', [UserController::class, 'index'])->name('index');
            Route::post('create', [UserController::class, 'store'])->name('store');
        });
    });
```

## Frontend Route Guards

### Admin Panel (Vue Router 3)

```javascript
router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth && !store.getters.getAccessToken) {
    next("/sign-in");
  } else {
    next();
  }
});
```

### Seller Portal (Vue Router 4)

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

  if (!authStore.isAuthenticated) {
    next({ name: "login" });
    return;
  }

  if (!checkRouteAccess(to, authStore.user.user_role_id)) {
    next({ name: "unauthorized" });
    return;
  }

  next();
});
```

### Route Meta

```javascript
// Seller portal route with role restriction
{
  path: "/catalog",
  meta: {
    requiresAuth: true,
    requiresRole: [USER_ROLE.SELLER, USER_ROLE.SELLER_EDITOR],
  },
}

// Admin-only route
{
  path: "/dashboard",
  meta: {
    requiresAuth: true,
    requiresRole: [USER_ROLE.SELLER],  // SELLER_EDITOR cannot access
  },
}
```

## API Error Codes for Auth

| HTTP Code | Meaning |
|-----------|---------|
| 400 | Bad Request (invalid token, inactive user) |
| 401 | Unauthorized (missing token) |
| 460 | Access Token Expired |
| 470 | Device Token Expired |

Frontend interceptors handle 460/470 by clearing tokens and redirecting to login.
