# Backend API Implementation (young_sia_api)

**Role**: Backend Software Engineer (PHP/Laravel Expert)

## 1. Create Model

**Location**: `app/Models/{Name}.php`

**Required Structure**:
```php
<?php

declare(strict_types=1);

namespace App\Models;

use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Prettus\Repository\Contracts\Transformable;
use Prettus\Repository\Traits\TransformableTrait;

class {Name} extends BaseModel implements Transformable
{
    use TransformableTrait;

    /**
     * The attributes that are mass assignable.
     *
     * @var array<int, string>
     */
    protected $fillable = [
        'field1',
        'field2',
        // ... list all fillable fields
    ];

    /**
     * Get the attributes that should be cast.
     *
     * @return array<string, string>
     */
    protected function casts(): array
    {
      return [
          'json_field' => 'array',
          'date_field' => 'datetime',
          'boolean_field' => 'boolean',
      ];
    }

    /**
     * @return BelongsTo
     */
    public function relatedModel(): BelongsTo
    {
        return $this->belongsTo(RelatedModel::class);
    }
}
```

**Guidelines**:
- Always extend `BaseModel`
- Define all mass-assignable fields in `$fillable`
- Use `$casts` for JSON, date, datetime, and boolean columns
- Implement `Transformable` interface if custom transformation is needed
- Add relationships (belongsTo, hasMany, belongsToMany, etc.)
- Use meaningful naming conventions (PascalCase for class names)

## 1.1 ID Hashing (Client-facing IDs)

All IDs exposed to the client are **hash strings**, not raw integers.

- Use `$model->hash_id` (provided by `BaseModel`) when returning IDs.
- Decode incoming IDs from route params or request payloads with `hashidsDecodeId()`.

```php
$hashId = $model->hash_id; // string sent to client
$id = hashidsDecodeId($request->id); // int used in queries
```

## 2. Create Repository Pattern

**Repository Interface**: `app/Repositories/{Name}Repository.php`
```php
<?php

declare(strict_types=1);

namespace App\Repositories;

use Prettus\Repository\Contracts\RepositoryInterface;

interface {Name}Repository extends RepositoryInterface
{
    // Add custom method signatures here if needed
}
```

**Repository Implementation**: `app/Repositories/{Name}RepositoryEloquent.php`
```php
<?php

declare(strict_types=1);

namespace App\Repositories;

use App\Models\{Name};
use Prettus\Repository\Eloquent\BaseRepository;

class {Name}RepositoryEloquent extends BaseRepository implements {Name}Repository
{
    /**
     * @return class-string<{Name}>
     */
    public function model(): string
    {
        return {Name}::class;
    }

    // Implement custom methods here
}
```

**Service Provider Registration**: `app/Providers/RepositoryServiceProvider.php`
- Add binding **before** the `//:end-bindings:` comment marker:
```php
$this->app->bind(
    \App\Repositories\{Name}Repository::class,
    \App\Repositories\{Name}RepositoryEloquent::class
);
//:end-bindings:
```

## 3. Create Controller

**Location**: `app/Http/Controllers/V1_0/{Namespace}/{Name}Controller.php`

```php
<?php

declare(strict_types=1);

namespace App\Http\Controllers\V1_0\{Namespace};

use App\Http\Controllers\Controller;
use App\Repositories\{Name}Repository;
use App\Http\Requests\{Name}\Create{Name}Request;
use App\Http\Requests\{Name}\Update{Name}Request;
use App\Http\Resources\{Name}\{Name}Resource;
use App\Facades\AppHelperFacade as AppHelper;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Resources\Json\AnonymousResourceCollection;

class {Name}Controller extends Controller
{
    /**
     * @param {Name}Repository $repository
     */
    public function __construct(
        protected {Name}Repository $repository
    ) {}

    /**
     * @return AnonymousResourceCollection
     */
    public function index(): AnonymousResourceCollection
    {
        ${resources} = $this->repository->paginate();
        return {Name}Resource::collection(${resources});
    }

    /**
     * @param Create{Name}Request $request
     * @return {Name}Resource
     */
    public function store(Create{Name}Request $request): {Name}Resource
    {
        ${resource} = $this->repository->create($request->validated());
        return new {Name}Resource(${resource});
    }

    /**
     * @param int|string $id
     * @return {Name}Resource
     */
    public function show(int|string $id): {Name}Resource
    {
        $decodedId = hashidsDecodeId($id);
        ${resource} = $this->repository->find($decodedId);
        return new {Name}Resource(${resource});
    }

    /**
     * @param Update{Name}Request $request
     * @param int|string $id
     * @return {Name}Resource
     */
    public function update(Update{Name}Request $request, int|string $id): {Name}Resource
    {
        $decodedId = hashidsDecodeId($id);
        ${resource} = $this->repository->update($request->validated(), $decodedId);
        return new {Name}Resource(${resource});
    }

    /**
     * @param int|string $id
     * @return JsonResponse
     */
    public function destroy(int|string $id): JsonResponse
    {
        $decodedId = hashidsDecodeId($id);
        $this->repository->delete($decodedId);
        return response()->json(AppHelper::emptyObject());
    }
}
```

**Guidelines**:
- Use constructor property promotion for dependency injection
- Inject repository via constructor
- Follow CRUD method names: `index`, `store`, `show`, `update`, `destroy`
- Return API resources, not raw models
- Use Form Requests for validation
- Decode hash IDs for queries (`hashidsDecodeId`)

## 4. Create Form Requests

**Location**: `app/Http/Requests/{Name}/`

**Create Request**: `Create{Name}Request.php`
```php
<?php

declare(strict_types=1);

namespace App\Http\Requests\{Name};

use Illuminate\Foundation\Http\FormRequest;

class Create{Name}Request extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize(): bool
    {
        return true;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array<string, \Illuminate\Contracts\Validation\ValidationRule|array<mixed>|string>
     */
    public function rules(): array
    {
        return [
            'field1' => ['required', 'string', 'max:255'],
            'field2' => ['nullable', 'integer'],
            // Use array notation for all validation rules
        ];
    }

    /**
     * Get the error messages for the defined validation rules.
     *
     * @return array<string, string>
     */
    public function messages(): array
    {
        return [
            'field1.required' => __('{resource}.validation.field1.required'),
            'field1.in' => __('{resource}.validation.field1.in'),
            'field2.integer' => __('{resource}.validation.field2.integer'),
        ];
    }
}
```

**Update Request**: `Update{Name}Request.php`
```php
<?php

declare(strict_types=1);

namespace App\Http\Requests\{Name};

use Illuminate\Foundation\Http\FormRequest;

class Update{Name}Request extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize(): bool
    {
        return true;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array<string, \Illuminate\Contracts\Validation\ValidationRule|array<mixed>|string>
     */
    public function rules(): array
    {
        return [
            'field1' => ['sometimes', 'required', 'string', 'max:255'],
            'field2' => ['nullable', 'integer'],
        ];
    }

    /**
     * Get the error messages for the defined validation rules.
     *
     * @return array<string, string>
     */
    public function messages(): array
    {
        return [
            'field1.required' => __('{resource}.validation.field1.required'),
            'field1.in' => __('{resource}.validation.field1.in'),
            'field2.integer' => __('{resource}.validation.field2.integer'),
        ];
    }
}
```

**Guidelines**:
- Always use **array notation** for validation rules (not pipe notation)
- Use `sometimes` for partial updates
- Add custom validation messages if needed

## 5. Create API Resources

**Location**: `app/Http/Resources/{Name}/{Name}Resource.php`

```php
<?php

declare(strict_types=1);

namespace App\Http\Resources\{Name};

use Illuminate\Http\Resources\Json\JsonResource;

class {Name}Resource extends JsonResource
{
    /**
     * @param \Illuminate\Http\Request $request
     * @return array<string, mixed>
     */
    public function toArray($request): array
    {
        return [
            'id' => $this->hash_id,
            'field1' => $this->field1,
            'field2' => $this->field2,
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
            // Include relationships if needed
            'related' => new RelatedResource($this->whenLoaded('related')),
            // Example: hash foreign keys when returning to client
            // 'related_id' => $this->related_id ? hashidsEncodeId($this->related_id) : null,
        ];
    }
}
```

**Guidelines**:
- Transform model data to API-friendly format
- Use `whenLoaded()` for conditional relationships
- Format dates consistently
- Hide sensitive fields (passwords, tokens, etc.)
- Always expose hash IDs (`$this->hash_id`) instead of raw integers

## 6. Create Routes

**Location**: `routes/api/v1_0/{namespace}/{resource}.php`

```php
<?php

declare(strict_types=1);

use App\Constants\ConstUserRole;
use App\Http\Controllers\V1_0\{Namespace}\{Name}Controller;
use Illuminate\Support\Facades\Route;

$adminRole = ConstUserRole::ADMIN;

Route::prefix('{resource}s')
    ->name('{resource}s.')
    ->middleware(['device.token', 'auth.token', "user.role:$adminRole"])
    ->group(function () {
        Route::get('', [{Name}Controller::class, 'index']);
        Route::post('', [{Name}Controller::class, 'store']);
        // {id} is a hash string sent by client
        Route::get('{id}', [{Name}Controller::class, 'show']);
        Route::put('{id}', [{Name}Controller::class, 'update']);
        Route::delete('{id}', [{Name}Controller::class, 'destroy']);
    });
```

**Guidelines**:
- Use **kebab-case** for URL paths (e.g., `user-profiles`, not `userProfiles`)
- Use **tuple notation**: `[Controller::class, 'method']`
- Apply middleware for authentication/authorization
- Group related routes with prefixes

## 7. Create Translations

**Location**: `lang/{en,km,zh}/{resource}.php`

**Example Structure**:
```php
<?php

return [
    'validation' => [
        'field1' => [
            'required' => 'Field1 is required.',
            'in' => 'Field1 must be one of the allowed values.',
        ],
        'field2' => [
            'integer' => 'Field2 must be an integer.',
        ],
    ],
    'messages' => [
        'create_success' => '{resource} created successfully.',
        'update_success' => '{resource} updated successfully.',
        'delete_success' => '{resource} deleted successfully.',
        'not_found' => '{resource} not found.',
    ],
    'error' => [
        'server_error' => 'An unexpected error occurred. Please try again later.',
    ],
];
```

**Guidelines**:
- Create translations for all three languages: **English (en)**, **Khmer (km)**, **Chinese (zh)**
- Include field labels, button texts, success/error messages
- Use consistent key naming across languages
- Keep translations organized by category
