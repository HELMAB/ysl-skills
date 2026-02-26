# Services

## Service Pattern

Services encapsulate business logic, keeping controllers thin and repositories focused on data access.

```
app/Services/
├── NotificationService.php      # Push notifications
├── PartialPaymentService.php    # Payment processing
├── PlaceOrderNextService.php    # Order orchestration
└── ...
```

## Service Structure

```php
<?php

namespace App\Services;

use App\Repositories\ExampleRepository;

class ExampleService
{
    public function __construct(
        private ExampleRepository $exampleRepository
    ) {}

    public function process(array $data): array
    {
        // Business logic here
        return ['success' => true];
    }
}
```

## Example: NotificationService

Clean separation of concerns with private helper methods:

```php
// app/Services/NotificationService.php
class NotificationService
{
    public function __construct(
        private UserDeviceRepository $userDeviceRepository
    ) {}

    public function pushNotifications(object $pushObj, array $userIds, string $code = 'admin-02')
    {
        $devices = $this->getActiveDevices($userIds);

        if ($devices->isEmpty()) {
            return null;
        }

        $deviceData = $this->groupDevicesByOsAndLocale($devices);
        $messages = $this->prepareMessages($deviceData, $pushObj, $code);

        if (empty($messages)) {
            return null;
        }

        return PushHelper::pushMultiNotification($messages);
    }

    private function getActiveDevices(array $userIds): Collection
    {
        return $this->userDeviceRepository
            ->whereIn('user_id', $userIds)
            ->where([
                ['user_devices.push_token', '<>', null],
                'user_devices.fcm_is_active' => true,
            ])
            ->get();
    }

    private function groupDevicesByOsAndLocale(Collection $devices): array
    {
        $deviceData = [];
        foreach ($devices as $device) {
            if (!$device->push_token) continue;
            $deviceData[$device->os][$device->locale][] = $device->push_token;
        }
        return $deviceData;
    }

    private function prepareMessages(array $deviceData, object $pushObj, string $code): array
    {
        $messages = [];
        $osConfig = AppHelper::customConfig('user.device.os');

        foreach ($deviceData as $os => $localeTokens) {
            foreach ($localeTokens as $locale => $tokens) {
                $notificationData = $this->buildNotificationData($pushObj, $locale);
                $pushData = $this->buildPushData($pushObj, $code);

                $messages[] = [
                    'tokens' => $tokens,
                    'notification' => [],
                    'android_data' => $os === $osConfig['ios'] ? [] : $this->buildAndroidData($notificationData, $pushData, $pushObj),
                    'ios_data' => $os === $osConfig['ios'] ? $this->buildIosData($notificationData, $pushData, $pushObj) : [],
                ];
            }
        }

        return $messages;
    }

    // Additional private methods: buildNotificationData, buildPushData, buildAndroidData, buildIosData
}
```

## Example: Payment Service with Transactions

```php
// app/Services/PartialPaymentService.php
class PartialPaymentService
{
    public function __construct(
        private OrderRepository $orderRepository,
        private WalletRepository $walletRepository,
        private PaymentRepository $paymentRepository
    ) {}

    public function pay(Order $order, float $amount): array
    {
        return DB::transaction(function () use ($order, $amount) {
            // Lock wallet for concurrency
            $wallet = $this->walletRepository
                ->lockForUpdate()
                ->find($order->user->wallet_id);

            // Validate balance
            if ($wallet->balance < $amount) {
                throw new InsufficientBalanceException();
            }

            // Deduct and create payment
            $wallet->decrement('balance', $amount);
            
            $payment = $this->paymentRepository->create([
                'order_id' => $order->id,
                'amount' => $amount,
                'status' => ConstPaymentStatus::COMPLETED,
            ]);

            return ['success' => true, 'payment' => $payment];
        });
    }
}
```

## Using Services in Controllers

```php
// app/Http/Controllers/V1_0/Back_Office/OrderController.php
class OrderController extends BaseController
{
    public function __construct(
        private PlaceOrderNextService $placeOrderService,
        private PartialPaymentService $paymentService
    ) {}

    public function store(StoreOrderRequest $request)
    {
        $result = $this->placeOrderService->placeOrderNext($request->validated());
        
        return $this->sendSuccess($result, 'Order created');
    }

    public function pay(PaymentRequest $request, Order $order)
    {
        $result = $this->paymentService->pay($order, $request->amount);
        
        return $this->sendSuccess($result);
    }
}
```

## Service Conventions

| Convention | Description |
|------------|-------------|
| Location | `app/Services/{Name}Service.php` |
| Injection | Constructor injection of repositories |
| Return type | Array with `success` key or domain object |
| Transactions | Use `DB::transaction()` for multi-step operations |
| Validation | Throw exceptions for business rule violations |
| Helpers | Private methods for code organization |
