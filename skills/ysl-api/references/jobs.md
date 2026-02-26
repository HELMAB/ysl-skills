# Queue Jobs

## Job Structure

All jobs implement `ShouldQueue` and use Laravel's queue traits:

```php
<?php

namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class ProcessExample implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    protected $model;

    public function __construct(Model $model = null)
    {
        $this->model = $model;
        $this->queue = getQueueServerName();  // Always set queue name
    }

    public function handle()
    {
        // Job logic here
    }
}
```

## Common Patterns

### Simple Job with Trait

```php
// app/Jobs/ProcessClearCacheJsonFile.php
class ProcessClearCacheJsonFile implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
    use HasClearCacheRepository;

    protected $historyId;

    public function __construct($historyId = null)
    {
        $this->historyId = $historyId;
        $this->queue = getQueueServerName();
    }

    public function handle()
    {
        $history = CleanCacheHistory::query()->find($this->historyId);
        if (!$history) return 0;

        try {
            Artisan::call('cache:clear');
            $this->cleanCacheJsonFile();
            $history->update(['finished_at' => now()]);
        } catch (Exception $exception) {
            $history->update(['exception' => $exception->getMessage()]);
            report($exception);
        }
    }
}
```

### Job with Repository Injection

```php
// app/Jobs/ProcessAlipayAccountNotifyDeposit.php
class ProcessAlipayAccountNotifyDeposit implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
    use HasTelegramNotifyTrait;

    protected $alipayAccount;
    protected $chatId;
    protected $title;

    public function __construct(AlipayAccount $alipayAccount = null)
    {
        $this->alipayAccount = $alipayAccount;
        $this->chatId = config('custom.telegram.chat_id_payment_request');
        $this->queue = getQueueServerName();
        $this->title = "⚠️ Alipay Min Balance Alert";
    }

    public function handle()
    {
        $alipayMinBalanceAlert = \AppHelper::customConfig('alipay_account.alipay_min_balance_alert');
        
        // Inject repository in handle() method
        $alipayAccountRepository = app()->make(AlipayAccountRepository::class);
        $alipayAccounts = $alipayAccountRepository
            ->where('balance', '<', $alipayMinBalanceAlert)
            ->where('status', ConstAlipayAccountStatus::NORMAL)
            ->get();
            
        if (count($alipayAccounts) > 0) {
            $message = "<b>$this->title</b>";
            foreach($alipayAccounts as $alipayAccount) {
                $message .= $this->getMessage($alipayAccount);
            }
            $this->sendToTelegram($this->chatId, $message);
        }
    }

    private function getMessage(AlipayAccount $alipayAccount)
    {
        $balance = bcdiv($alipayAccount->balance, 1, 2);
        return "\nAccount: <b>$alipayAccount->account_name</b>\nBalance: <b>{$balance}¥</b>";
    }
}
```

## Dispatching Jobs

```php
// Dispatch immediately to queue
ProcessClearCacheJsonFile::dispatch($historyId);

// Dispatch with delay
ProcessExample::dispatch($model)->delay(now()->addMinutes(5));

// Dispatch to specific queue
ProcessExample::dispatch($model)->onQueue('high-priority');

// Dispatch from another job
public function handle()
{
    // Process current job...
    
    // Chain another job
    ProcessNextStep::dispatch($this->model);
}
```

## Job Conventions

| Convention | Example |
|------------|---------|
| Naming | `Process{Action}` or `Generate{Report}` |
| Queue name | Always use `getQueueServerName()` helper |
| Repository injection | Use `app()->make()` in `handle()`, not constructor |
| Error handling | Wrap in try-catch, update history record |
| Notifications | Use `HasTelegramNotifyTrait` for alerts |

## Common Traits Used in Jobs

| Trait | Purpose |
|-------|---------|
| `HasTelegramNotifyTrait` | Send Telegram notifications |
| `HasClearCacheRepository` | Clear cache files |
| `HasShortUrlTrait` | Generate short URLs |

## Job Categories (115 total)

| Category | Count | Examples |
|----------|-------|----------|
| Payment processing | 30+ | `ProcessAlipayAccount*`, `ProcessBranchPayment` |
| Notifications | 15+ | `Process*Notification`, `ProcessAlertSlack*` |
| Reports | 10+ | `Generate*Report`, `ProcessMonthlyProfit*` |
| Data sync | 20+ | `ProcessSync*`, `ProcessImport*` |
| Cache/cleanup | 10+ | `ProcessClear*`, `ProcessCleanup*` |
