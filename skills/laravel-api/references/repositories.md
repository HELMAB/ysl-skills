# Repository Pattern

## Overview

Uses **prettus/l5-repository ^2.10** with interface + Eloquent implementation pairs.

## File Structure

```
app/Repositories/
├── UserRepository.php                  # Interface
├── UserRepositoryEloquent.php          # Implementation
├── OrderRepository.php
├── OrderRepositoryEloquent.php
└── ... (70+ repository pairs)
```

## Interface Pattern

```php
<?php

namespace App\Repositories;

use Prettus\Repository\Contracts\RepositoryInterface;

interface FeatureRepository extends RepositoryInterface
{
    public function getFeature($where);

    public function getFeatures($where, $perPage);
}
```

## Eloquent Implementation

```php
<?php

namespace App\Repositories;

use Prettus\Repository\Eloquent\BaseRepository;
use Prettus\Repository\Criteria\RequestCriteria;
use App\Models\Feature;

class FeatureRepositoryEloquent extends BaseRepository implements FeatureRepository
{
    public function model()
    {
        return Feature::class;
    }

    public function boot(): void
    {
        $this->pushCriteria(app(RequestCriteria::class));
    }

    public function getFeature($where)
    {
        try {
            $item = $this->model
                ->withTrashed()
                ->where($where)
                ->first();

            $this->resetModel();
        } catch (Exception $exception) {
            Log::error($exception);
        }

        return $item;
    }

    public function getFeatures($where, $perPage = 20)
    {
        $filterBy = array_key_exists('filter_by', $where) ? $where['filter_by'] : null;
        unset($where['filter_by']);

        $items = $this->model
            ->where($where)
            ->when($filterBy == 'active', function ($query) {
                return $query->where('is_active', true);
            })
            ->paginate($perPage);

        return $items;
    }
}
```

## Binding (Service Provider)

Repository bindings are registered in `RepositoryServiceProvider`:

```php
$this->app->bind(
    FeatureRepository::class,
    FeatureRepositoryEloquent::class
);
```

## Common Repository Methods

Inherited from `BaseRepository`:

```php
// Find by ID
$item = $this->featureRepository->find($id);

// Find with conditions
$items = $this->featureRepository->findWhere(['user_id' => $userId]);
$item  = $this->featureRepository->findWhere(['id' => $id])->first();

// Find by single field
$items = $this->featureRepository->findByField('status', 'active');

// Scoped queries
$items = $this->featureRepository
    ->scopeQuery(function ($query) use ($filters) {
        return $query->where($filters);
    })
    ->orderBy('id', 'desc')
    ->paginate($perPage);

// Create
$item = $this->featureRepository->create([
    'name'    => $request->input('name'),
    'user_id' => $userId,
]);

// Update
$item = $this->featureRepository->update($data, $id);

// Delete
$this->featureRepository->delete($id);

// Paginate
$items = $this->featureRepository->paginate(20);
```

## Usage in Controllers

```php
class FeatureController extends BaseController
{
    protected $featureRepository;

    public function __construct(FeatureRepository $featureRepository)
    {
        parent::__construct();
        $this->featureRepository = $featureRepository;
    }

    public function index()
    {
        $user    = \AppHelper::getResolve('user');
        $perPage = 20;

        $items = $this->featureRepository
            ->scopeQuery(function ($query) use ($user) {
                return $query->where('user_id', $user->id);
            })
            ->orderBy('id', 'desc')
            ->paginate($perPage);

        return response()->json([
            'data'       => FeatureResource::collection($items),
            'pagination' => new PaginationResource($items),
        ]);
    }
}
```

## Important Notes

- Always call `$this->resetModel()` after direct model queries in custom methods
- Use `scopeQuery()` for complex query building in controllers
- Use `findWhere()` for simple condition-based lookups
- Repository interfaces are type-hinted in controller constructors for dependency injection
