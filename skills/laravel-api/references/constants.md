# Constants

## Pattern

Class-based constants in `app/Constants/`:

```php
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

## Naming Convention

- Class name: `Const{DomainName}` (e.g., `ConstOrderStatus`, `ConstBankCode`)
- Constants: `UPPER_SNAKE_CASE`
- Namespace: `App\Constants`

## Common Constants

| Class | Purpose |
|-------|---------|
| `ConstUserRole` | 14 user roles (ADMIN through SELLER_EDITOR) |
| `ConstOrderStatus` | Order lifecycle states |
| `ConstOrderItemStatus` | Order item states |
| `ConstProductSource` | Product origin types |
| `ConstPurchaseType` | Purchase type identifiers |
| `ConstCountry` | Country identifiers |
| `ConstBankCode` | Payment bank codes |
| `ConstApiKeyStatus` | API key validation states |
| `ConstCouponTransactionType` | Coupon transaction types |
| `ConstCargoIssue` | Cargo issue categories |
| `ConstUserNotification` | Notification type identifiers |

## Usage

```php
use App\Constants\ConstUserRole;
use App\Constants\ConstOrderStatus;

// In middleware
Route::middleware(["user.role:" . ConstUserRole::ADMIN]);

// In conditions
if ($user->user_role_id == ConstUserRole::SELLER) {
    // Seller-specific logic
}

// In queries
$orders = $this->orderRepository
    ->findWhere(['status' => ConstOrderStatus::PURCHASED]);
```

## Creating New Constants

```php
<?php

namespace App\Constants;

class ConstPaymentMethod
{
    const CASH          = 1;
    const BANK_TRANSFER = 2;
    const WALLET        = 3;
}
```
