# Routing

## Modular Route Loading

Routes are organized in modular files and auto-loaded via glob pattern:

```php
// routes/web.php
<?php

use Illuminate\Support\Facades\Route;

Route::get('products/{url_key}', 'ProductController@share')->name('product.share');

// Auto-load all route files from web/ directory
foreach (glob(__DIR__ . '/web/*.php') as $filename) {
    require_once $filename;
}
```

## Route File Structure

```
routes/
├── web.php              # Main entry point
└── web/
    ├── app.php          # App-related routes
    ├── image.php        # Image serving
    ├── page.php         # Static pages
    ├── post.php         # Blog posts
    ├── product.php      # Product pages
    ├── top_up.php       # Top-up features
    └── user.php         # User account routes
```

## Example Route Files

### Product Routes

```php
// routes/web/product.php
<?php

use App\Http\Controllers\ProductController;
use Illuminate\Support\Facades\Route;

Route::prefix('products')->group(function () {
    Route::get('{id}/detail', [ProductController::class, 'show'])->name('product.show');
    Route::get('{url_key}/share', [ProductController::class, 'share'])->name('product.share');
});
```

### User Routes

```php
// routes/web/user.php
<?php

use App\Http\Controllers\AccountController;
use Illuminate\Support\Facades\Route;

Route::prefix('account')->group(function () {
    Route::get('/', [AccountController::class, 'index'])->name('account.index');
    Route::get('referral', [AccountController::class, 'referral'])->name('account.referral');
});
```

## Route Conventions

| Convention | Description |
|------------|-------------|
| File location | `routes/web/{feature}.php` |
| Naming | Use `->name('feature.action')` |
| Grouping | Use `Route::prefix()` for related routes |
| Controllers | Use array syntax `[Controller::class, 'method']` |

## Adding New Routes

1. Create route file: `routes/web/{feature}.php`
2. Define routes with prefix and names
3. File is auto-loaded via glob pattern (no manual registration needed)
