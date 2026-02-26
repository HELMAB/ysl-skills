# Authentication

## Multi-Layer Token System

The API uses JWT (tymon/jwt-auth ^2.2) with a three-layer middleware chain:

```
device.token → auth.token → user.role:{roleId}
```

### Middleware Stack

| Middleware | Purpose | Resolves |
|-----------|---------|----------|
| `device.token` | Validates device token from header/query | `app()->instance('device', $device)` |
| `auth.token` | Validates JWT access token | `app()->instance('user', $user)` |
| `auth.check` | Optional auth (doesn't fail if missing) | User if available |
| `user.role:{id}` | Restricts to specific role(s) | — |

### Request Headers

```
X-Device-Token: {device_token}
X-Access-Token: {jwt_access_token}
X-Platform: web|mobile|factory
X-Locale: en|km|zh
```

### DeviceTokenMiddleware

Validates device identity via header `X-Device-Token` or query param `?device_token=`:

```php
// Resolves device and binds to container
app()->instance('device', $device);
```

### AuthTokenMiddleware

Validates JWT and resolves user:

```php
<?php

namespace App\Http\Middleware;

use App\Repositories\UserRepository;
use Closure;
use Symfony\Component\HttpFoundation\Response;

class AuthTokenMiddleware
{
    protected $userRepository;

    public function __construct(UserRepository $userRepository)
    {
        $this->userRepository = $userRepository;
    }

    public function handle($request, Closure $next)
    {
        $accessToken = \AppHelper::getRequestValue('access_token', 'X-Access-Token');

        if ($accessToken === null) {
            return response()->json([
                'error' => ['message' => __('error.unauthorized_access')],
            ], Response::HTTP_UNAUTHORIZED);
        }

        $checked = \AppJwt::check($accessToken);

        if ($checked === 'TOKEN_EXPIRED') {
            return response()->json([
                'error' => ['message' => __('auth.validation.access_token.expired')],
            ], 460);
        } elseif ($checked == 'TOKEN_INVALID') {
            return response()->json([
                'error' => ['message' => __('auth.validation.access_token.invalid')],
            ], Response::HTTP_BAD_REQUEST);
        }

        $user = $this->userRepository
            ->findWhere(['id' => hashidsDecodeAccessToken($checked->get('sub'))])
            ->first();

        if ($user === null || $user->is_active == false) {
            return response()->json([
                'error' => ['message' => __('auth.error.user.enabled')],
            ], Response::HTTP_BAD_REQUEST);
        }

        app()->instance('user', $user);

        return $next($request);
    }
}
```

### JWT Facade (AppJwt)

```php
// Check token validity
$payload = \AppJwt::check($token);
// Returns: Payload object | 'TOKEN_EXPIRED' | 'TOKEN_INVALID' | 'TOKEN_UNKNOWN_ERROR'
```

### Custom Error Codes

| Code | Meaning |
|------|---------|
| 400 | Bad Request / Validation Error |
| 401 | Unauthorized |
| 460 | Access Token Expired |
| 470 | Device Token Expired |

### Accessing Authenticated User

```php
// In controllers — use AppHelper facade
$user = \AppHelper::getResolve('user');
$userId = $user->id;

// Device access
$device = \AppHelper::getResolve('device');
```

### Route Middleware Examples

```php
// Public (device only)
Route::middleware('device.token');

// Authenticated user
Route::middleware(['device.token', 'auth.token']);

// Optional auth
Route::middleware(['device.token', 'auth.check']);

// Role-restricted
use App\Constants\ConstUserRole;
$adminRole = ConstUserRole::ADMIN;
Route::middleware(['device.token', 'auth.token', "user.role:$adminRole"]);
```
