# Controllers

## Base Controller

All controllers extend `BaseController` which inherits shared traits and repositories:

```php
<?php

namespace App\Http\Controllers;

use App\Traits\HasRecalculateShippedHouseTrait;
use App\Traits\HasShortUrlTrait;
use App\Traits\HasDigitalSalePlan;
use App\Traits\HasRequestUserHomeDeliveryFeeTrait;
use App\Traits\HasSeller;

class BaseController extends Controller
{
    use HasRecalculateShippedHouseTrait;
    use HasShortUrlTrait;
    use HasDigitalSalePlan;
    use HasRequestUserHomeDeliveryFeeTrait;
    use HasSeller;

    public function __construct()
    {
        // Shared initialization
    }
}
```

## Versioned Controllers

Controllers live under `app/Http/Controllers/V1_0/` with subdirectories:

```
V1_0/
├── AuthController.php
├── CouponController.php
├── Back_Office/
│   └── UserController.php
└── Mobile/
    └── UserPlanController.php
```

## Controller Pattern

```php
<?php

namespace App\Http\Controllers\V1_0;

use App\Http\Controllers\BaseController;
use App\Http\Requests\Feature\CreateRequest;
use App\Http\Resources\Feature\FeatureResource;
use App\Http\Resources\Pagination\PaginationResource;
use App\Repositories\FeatureRepository;
use Illuminate\Http\Request;
use Illuminate\Http\Response;

class FeatureController extends BaseController
{
    protected $featureRepository;

    public function __construct(FeatureRepository $featureRepository)
    {
        parent::__construct();

        $this->featureRepository = $featureRepository;
    }

    public function index(Request $request)
    {
        $user    = \AppHelper::getResolve('user');
        $perPage = 20;

        $items = $this->featureRepository
            ->scopeQuery(function ($query) use ($user) {
                return $query->where('user_id', $user->id);
            })
            ->orderBy('id', 'desc')
            ->paginate($perPage);

        $response = [
            'data'       => FeatureResource::collection($items),
            'pagination' => new PaginationResource($items, [
                'per_page' => $perPage,
            ]),
        ];

        return response()->json($response);
    }

    public function show($hashedId)
    {
        $id   = hashidsDecodeId($hashedId);
        $item = $this->featureRepository->find($id);

        if ($item === null) {
            return response()->json([
                'error' => ['message' => __('feature.validation.not_found')],
            ], Response::HTTP_BAD_REQUEST);
        }

        return response()->json([
            'data' => new FeatureResource($item),
        ]);
    }

    public function store(CreateRequest $request)
    {
        $user = \AppHelper::getResolve('user');

        $item = $this->featureRepository->create([
            'user_id' => $user->id,
            'name'    => $request->input('name'),
        ]);

        return response()->json([
            'data' => new FeatureResource($item),
        ], Response::HTTP_CREATED);
    }
}
```

## Key Patterns

- **Constructor injection** for repositories (not typed properties — legacy style)
- **`\AppHelper::getResolve('user')`** to get authenticated user
- **`hashidsDecodeId()`** to decode hashed IDs from URLs
- **Consistent response format**: `{ data: ..., pagination: ... }`
- **Error format**: `{ error: { message: '...' } }`
- **PaginationResource** wraps paginated results

## Versioned Routing

Routes auto-load from `routes/api/v1_0/`:

```php
// routes/api/api_version.php
Route::prefix('v1.0')->name('v1_0.')->group(function () {
    foreach (glob(__DIR__ . '/v1_0/*.php') as $filename) {
        require_once $filename;
    }
});
```

## Route Definition Pattern

```php
<?php

use App\Constants\ConstUserRole;
use App\Http\Controllers\V1_0\FeatureController;
use Illuminate\Support\Facades\Route;

Route::prefix('features')
    ->name('features.')
    ->middleware(['device.token', 'auth.token'])
    ->group(function () {
        Route::get('', [FeatureController::class, 'index'])->name('index');
        Route::get('{id}', [FeatureController::class, 'show'])->name('show');
        Route::post('create', [FeatureController::class, 'store'])->name('store');
        Route::put('{id}/update', [FeatureController::class, 'update'])->name('update');
        Route::delete('{id}', [FeatureController::class, 'destroy'])->name('destroy');
    });
```

## Back Office Routes (Role-Restricted)

```php
<?php

use App\Constants\ConstUserRole;
use App\Http\Controllers\V1_0\Back_Office\UserController;
use Illuminate\Support\Facades\Route;

$adminRole = ConstUserRole::ADMIN;

Route::prefix('user-lists')
    ->name('user-lists.')
    ->middleware(['device.token', 'auth.token', "user.role:$adminRole"])
    ->group(function () {
        Route::get('user-roles', [UserController::class, 'userRoleOptions'])
            ->name('user-roles');
        Route::post('create', [UserController::class, 'createUser'])
            ->name('create-user');
    });
```
