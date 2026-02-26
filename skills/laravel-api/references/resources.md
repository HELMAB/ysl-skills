# API Resources

## Basic Resource

```php
<?php

namespace App\Http\Resources\Currency;

use Illuminate\Http\Resources\Json\JsonResource;

class CurrencyResource extends JsonResource
{
    public function toArray($request)
    {
        return [
            'id'          => $this->hash_id,
            'country_id'  => $this->country_id,
            'currency'    => $this->currency,
            'code'        => $this->code,
            'symbol'      => $this->symbol,
            'rate'        => $this->rate,
            'buying_rate' => $this->buying_rate,
        ];
    }
}
```

## Resource with Relationships

```php
<?php

namespace App\Http\Resources\Country;

use App\Facades\AppHelperFacade;
use Illuminate\Http\Resources\Json\JsonResource;

class CountryOptionsResource extends JsonResource
{
    public function toArray($request)
    {
        return [
            'id'                => $this->id,
            'hash_id'           => $this->hash_id,
            'name'              => AppHelperFacade::toLocale($this->name, $this->name_locale, true),
            'name_en'           => $this->name,
            'name_locale'       => $this->name_locale,
            'flag'              => $this->flag,
            'created_at'        => AppHelperFacade::datetimeFormat($this->created_at, 'medium'),
            'background_image'  => $this->background_image_url,
            'agency_fee_purchase' => new AgencyFeeResource($this->whenLoaded('agencyFeePurchase')),
            'agency_fee_directly' => new AgencyFeeResource($this->whenLoaded('agencyFeeDirectly')),
        ];
    }
}
```

## Directory Structure

```
app/Http/Resources/
├── Pagination/
│   └── PaginationResource.php
├── Currency/
│   └── CurrencyResource.php
├── Country/
│   ├── CountryOptionsResource.php
│   └── AgencyFeeResource.php
├── User/
│   └── UserDataResource.php
├── Shop/
│   ├── ShopDataResource.php
│   └── ShopDefaultResource.php
├── Order/
│   ├── OrderResource.php
│   └── OrderItemResource.php
├── Coupon/
│   └── CouponTransactionResource.php
└── ... (85+ resource directories)
```

## Usage in Controllers

```php
// Single resource
return response()->json([
    'data' => new FeatureResource($item),
]);

// Collection
return response()->json([
    'data' => FeatureResource::collection($items),
]);

// With pagination
return response()->json([
    'data'       => FeatureResource::collection($paginated),
    'pagination' => new PaginationResource($paginated, [
        'per_page' => $perPage,
    ]),
]);
```

## Standard Response Shapes

```json
// Single item
{
    "data": { "id": "abc123", "name": "..." }
}

// Collection with pagination
{
    "data": [
        { "id": "abc123", "name": "..." }
    ],
    "pagination": {
        "current": 1,
        "next": 2,
        "previous": null,
        "total": 50
    }
}
```

## Conventions

- Use `hash_id` instead of raw `id` in responses (Hashids obfuscation)
- Use `AppHelperFacade::toLocale()` for localized fields
- Use `AppHelperFacade::datetimeFormat()` for date formatting
- Use `$this->whenLoaded()` for conditional relationship loading
- Group resources by **domain**: `User/`, `Order/`, `Shop/`
- One resource class per transformation concern
