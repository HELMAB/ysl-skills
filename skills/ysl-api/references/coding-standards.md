# PHP 8.3+ & Laravel Best Practices

## Coding Standards

All new PHP code MUST follow these standards:

### 1. Type Declarations (Required)

```php
// ✅ GOOD: Full type declarations
public function process(string $name, int $count, ?array $options = null): JsonResponse
{
    return response()->json(['data' => $result]);
}

// ❌ BAD: Missing types
public function process($name, $count, $options = null)
{
    return response()->json(['data' => $result]);
}
```

### 2. Constructor Property Promotion (Preferred)

```php
// ✅ GOOD: Constructor property promotion
class OrderService
{
    public function __construct(
        private OrderRepository $orderRepository,
        private PaymentService $paymentService,
    ) {}
}

// ❌ AVOID: Old-style constructor
class OrderService
{
    private $orderRepository;
    private $paymentService;
    
    public function __construct(OrderRepository $orderRepository, PaymentService $paymentService)
    {
        $this->orderRepository = $orderRepository;
        $this->paymentService = $paymentService;
    }
}
```

### 3. Typed Properties (Required)

```php
// ✅ GOOD: Typed properties
class ProcessPayment implements ShouldQueue
{
    public int $orderId;
    public float $amount;
    public ?string $note = null;
}

// ❌ BAD: Untyped properties
class ProcessPayment implements ShouldQueue
{
    public $orderId;
    public $amount;
    public $note;
}
```

### 4. Match Expressions (Preferred over switch)

```php
// ✅ GOOD: Match expression
public function getStatusText(int $status): string
{
    return match ($status) {
        ConstOrderStatus::PENDING => __('order.status.pending'),
        ConstOrderStatus::PROCESSING => __('order.status.processing'),
        ConstOrderStatus::COMPLETED => __('order.status.completed'),
        default => __('order.status.unknown'),
    };
}

// ❌ AVOID: Switch statement for simple returns
public function getStatusText(int $status): string
{
    switch ($status) {
        case ConstOrderStatus::PENDING:
            return __('order.status.pending');
        // ...
    }
}
```

### 5. Null Safe Operator (Required when appropriate)

```php
// ✅ GOOD: Null safe operator
'role_name' => $this->userRole?->name,
'shop_name' => $this->user?->shop?->name,
'created_at' => $this->created_at?->toAtomString(),

// ❌ AVOID: Verbose null checks
'role_name' => $this->userRole ? $this->userRole->name : null,
'created_at' => $this->created_at ? $this->created_at->toAtomString() : null,
```

### 6. Named Arguments (For readability)

```php
// ✅ GOOD: Named arguments for clarity
$notification = Notification::create(
    title: $request->title,
    message: $request->message,
    type: ConstNotificationType::SYSTEM,
    is_active: true,
);

// OK: Positional for simple cases
$user = User::find($id);
```

### 7. Readonly Properties (For immutable data)

```php
// ✅ GOOD: Readonly for immutable DTOs
class OrderData
{
    public function __construct(
        public readonly int $orderId,
        public readonly string $status,
        public readonly float $total,
    ) {}
}
```

### 8. Return Types (Required)

```php
// ✅ GOOD: Explicit return types
public function index(Request $request): JsonResponse
public function store(StoreRequest $request): JsonResponse
public function find(int $id): ?Model
public function process(): void
public function getData(): array

// ❌ BAD: Missing return types
public function index(Request $request)
public function process()
```

## Laravel Best Practices

### Controllers

```php
// ✅ GOOD: Single responsibility, typed
class ProductController extends BaseController
{
    public function __construct(
        private ProductRepository $productRepository,
    ) {}

    public function index(IndexRequest $request): JsonResponse
    {
        $products = $this->productRepository
            ->with(['category', 'shop'])
            ->paginate($request->per_page ?? 15);

        return $this->sendSuccess(
            ProductResource::collection($products),
            __('product.index.success')
        );
    }
}
```

### Form Requests

```php
// ✅ GOOD: Typed rules method
class StoreProductRequest extends BaseRequest
{
    public function rules(): array
    {
        return [
            'name' => ['required', 'string', 'max:255'],
            'price' => ['required', 'numeric', 'min:0'],
            'category_id' => ['required', 'exists:categories,id'],
        ];
    }

    public function messages(): array
    {
        return [
            'name.required' => __('validation.product.name.required'),
        ];
    }
}
```

### API Resources

```php
// ✅ GOOD: Typed with null safe operators
class ProductResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => hashidsEncodeId($this->id),
            'name' => $this->name,
            'price' => $this->price,
            'category' => $this->whenLoaded('category', fn() => [
                'id' => hashidsEncodeId($this->category->id),
                'name' => $this->category->name,
            ]),
            'shop_name' => $this->shop?->name,
            'created_at' => $this->created_at?->toAtomString(),
        ];
    }
}
```

### Eloquent Queries

```php
// ✅ GOOD: Query builder with proper typing
$orders = Order::query()
    ->where('status', ConstOrderStatus::PENDING)
    ->where('created_at', '>=', now()->subDays(30))
    ->with(['items', 'user:id,name'])
    ->orderByDesc('created_at')
    ->paginate(perPage: 15);

// ✅ GOOD: Scopes for reusable queries
// In Model:
public function scopePending(Builder $query): Builder
{
    return $query->where('status', ConstOrderStatus::PENDING);
}

// Usage:
Order::pending()->get();
```

## Summary Checklist

| Requirement | Status |
|-------------|--------|
| Return type on all methods | Required |
| Typed properties | Required |
| Type hints on parameters | Required |
| Constructor property promotion | Preferred |
| Match expressions over switch | Preferred |
| Null safe operator (`?->`) | Required when applicable |
| Named arguments | Recommended for clarity |
| Readonly for immutable data | Recommended |
