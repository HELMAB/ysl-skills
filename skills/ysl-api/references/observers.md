# Observers

## Observer Pattern

Model observers handle Eloquent lifecycle events without cluttering model classes.

```
app/Observers/
├── Product/
│   └── ProductObserver.php
├── Stock/
│   └── StockObserver.php
├── OrderItem/
│   └── OrderItemObserver.php
└── ...
```

## Observer Structure

```php
<?php

namespace App\Observers\{Feature};

use App\Models\{Model};

class {Model}Observer
{
    public function creating({Model} $model)
    {
        // Before model is created
    }

    public function created({Model} $model)
    {
        // After model is created
    }

    public function updating({Model} $model)
    {
        // Before model is updated
    }

    public function updated({Model} $model)
    {
        // After model is updated
    }

    public function deleted({Model} $model)
    {
        // After model is deleted
    }
}
```

## Example: ProductObserver

Auto-generates URLs and share links on creation:

```php
// app/Observers/Product/ProductObserver.php
namespace App\Observers\Product;

use App\Models\Product;
use App\Traits\HasShortUrlTrait;

class ProductObserver
{
    use HasShortUrlTrait;

    public function creating(Product $product)
    {
        $productId = (int) $product->product_id;
        if ($productId > 0) {
            $product->share_link = $this->generateShareLink($product);
        }
    }

    public function created(Product $product)
    {
        $productId = (int) $product->product_id;
        if (empty($productId) || $productId == 0) {
            $product->product_id = $product->id;
            $product->product_url = $this->generateProductUrl($product);
            $product->share_link = $this->generateShareLink($product);
            $product->save();
        }
    }

    private function generateProductUrl(Product $product)
    {
        return config('custom.server.server_base') . '/products/' . hashidsEncodeId($product->id) . '/detail';
    }
}
```

## Example: OrderItemObserver

Audit trail for express number changes:

```php
// app/Observers/OrderItem/OrderItemObserver.php
namespace App\Observers\OrderItem;

use App\Constants\ConstHouseAudit;
use App\Models\HouseAudit;
use App\Models\OrderItem;

class OrderItemObserver
{
    public function created(OrderItem $orderItem)
    {
        //
    }

    public function updated(OrderItem $orderItem)
    {
        // Track changes to express_number field
        if ($orderItem->isDirty('express_number')) {
            $performer = \AppHelper::getResolve('user') ?? null;
            
            HouseAudit::query()->create([
                'log_name'      => ConstHouseAudit::UPDATE_EXPRESS_NUMBER,
                'house_id'      => $orderItem->house_id,
                'order_item_id' => $orderItem->id,
                'updated_by'    => $performer ? $performer->id : config('custom.user')['admin_id'],
                'old_value'     => ['express_number' => $orderItem->getOriginal('express_number')],
                'new_value'     => ['express_number' => $orderItem->express_number]
            ]);
        }
    }

    public function deleted(OrderItem $orderItem)
    {
        //
    }

    public function restored(OrderItem $orderItem)
    {
        //
    }

    public function forceDeleted(OrderItem $orderItem)
    {
        //
    }
}
```

## Registering Observers

Register in `AppServiceProvider` or dedicated `ObserverServiceProvider`:

```php
// app/Providers/AppServiceProvider.php
use App\Models\Product;
use App\Models\OrderItem;
use App\Observers\Product\ProductObserver;
use App\Observers\OrderItem\OrderItemObserver;

public function boot()
{
    Product::observe(ProductObserver::class);
    OrderItem::observe(OrderItemObserver::class);
}
```

## Observer Conventions

| Convention | Description |
|------------|-------------|
| Location | `app/Observers/{Feature}/{Model}Observer.php` |
| Naming | `{Model}Observer` |
| Traits | Use traits for shared logic (e.g., `HasShortUrlTrait`) |
| Audit | Use `isDirty()` to track field changes |
| User context | Get current user via `\AppHelper::getResolve('user')` |

## Lifecycle Events

| Event | Timing | Use Case |
|-------|--------|----------|
| `creating` | Before insert | Generate default values, validate |
| `created` | After insert | Generate IDs, URLs, send notifications |
| `updating` | Before update | Validate changes |
| `updated` | After update | Audit trail, sync related data |
| `deleting` | Before delete | Prevent deletion, cleanup |
| `deleted` | After delete | Remove related data |
| `restored` | After restore (soft delete) | Re-sync data |

## Existing Observers (15 total)

| Observer | Purpose |
|----------|---------|
| `ProductObserver` | Generate product URLs and share links |
| `StockObserver` | Auto-generate sequential stock IDs |
| `OrderItemObserver` | Audit express number changes |
| `OrderObserver` | Track order status changes |
| `UserObserver` | Handle user creation/updates |
