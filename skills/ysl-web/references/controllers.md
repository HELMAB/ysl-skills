# Controllers

## BaseController

All controllers extend `BaseController` which initializes device tokens for API authentication:

```php
// app/Http/Controllers/BaseController.php
namespace App\Http\Controllers;

use Carbon\Carbon;
use GuzzleHttp\Client;
use Illuminate\Support\Facades\Log;

class BaseController extends Controller
{
    protected $deviceToken;
    protected $data = [];

    public function __construct()
    {
        $this->initDeviceToken();
    }

    private function initDeviceToken()
    {
        $deviceToken = null;

        if (!isset($_COOKIE['device_token'])) {
            try {
                $client = new Client();
                $response = $client->request(
                    'GET',
                    env('SERVER_API') . '/v1.0/gateway/web/device/token',
                    [
                        'headers' => [
                            'X-Api-Key' => env('API_KEY'),
                        ],
                    ]
                );
                
                if ($response->getStatusCode() == 200) {
                    $json = json_decode($response->getBody(), true);
                    $now = Carbon::now();
                    $deviceToken = $json['data']['device_token'];
                    $_COOKIE['device_token'] = $deviceToken;
                    setcookie('device_token', $deviceToken, $now->endOfMonth()->timestamp, '/');
                }
            } catch (\Exception $exception) {
                Log::error($exception);
            }
        } else {
            $deviceToken = $_COOKIE['device_token'];
        }

        if ($deviceToken) {
            $this->deviceToken = $deviceToken;
        }

        $this->data['device_token'] = $this->deviceToken;
    }
}
```

## API Call Patterns

### Using Guzzle Client

```php
// app/Http/Controllers/ProductController.php
class ProductController extends Controller
{
    public function share(string $url_key)
    {
        $data = ['url_key' => $url_key];
        $client = new Client();
        $options = [
            'headers' => ['X-Api-Key' => config('custom.common.api_key')],
            'query' => ['url_key' => $url_key]
        ];

        try {
            $res = $client->request('GET', env('SERVER_API') . '/v1.0/products/share/detail', $options);
            if ($res->getStatusCode() == 200) {
                $data['product'] = json_decode($res->getBody()->getContents(), FALSE);
            }
        } catch (Throwable $exception) {
            Log::error($exception);
        }

        return view('share.product.index', $data);
    }
}
```

### Using Laravel HTTP Facade

```php
use Illuminate\Support\Facades\Http;

public function show(string $id)
{
    $data = [];
    try {
        $response = Http::withHeaders([
            'X-Api-Key' => config('custom.common.api_key'),
            'Accept' => 'application/json',
        ])->get(env('SERVER_API') . "/v1.0/site/products/{$id}/show", [
            'product_id' => $id,
            'product_source_id' => 7,
        ]);
        
        if ($response['data']) {
            $data['product'] = $response['data'];
        }
    } catch (Throwable $exception) {
        Log::error($exception);
        abort(404);
    }

    return view('app.product_detail', $data);
}
```

## Controller Conventions

| Convention | Description |
|------------|-------------|
| Base class | Extend `BaseController` for device token |
| API calls | Use Guzzle or Laravel HTTP facade |
| Headers | Always include `X-Api-Key` |
| Error handling | Try-catch with `Log::error()` |
| Return | Blade view with data array |

## Common Headers

```php
$headers = [
    'X-Api-Key' => config('custom.common.api_key'),
    'X-Device-Token' => $this->deviceToken,
    'Accept' => 'application/json',
];
```
