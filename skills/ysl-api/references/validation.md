# Validation

## BaseRequest

All form requests extend `BaseRequest` which provides a consistent error response:

```php
<?php

namespace App\Http\Requests;

use Illuminate\Contracts\Validation\Validator;
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Http\Exceptions\HttpResponseException;
use Symfony\Component\HttpFoundation\Response;

class BaseRequest extends FormRequest
{
    public function authorize()
    {
        return true;
    }

    protected function failedValidation(Validator $validator)
    {
        $errors = [
            'error' => [
                'message' => $validator->errors()->first(),
                'summary' => $validator->errors(),
            ],
        ];

        $response = response()->json($errors, Response::HTTP_BAD_REQUEST);

        throw new HttpResponseException($response);
    }
}
```

## Form Request Pattern

```php
<?php

namespace App\Http\Requests\Auth;

use App\Http\Requests\BaseRequest;
use App\Rules\PhoneNumberAdminRule;

class SignInRequest extends BaseRequest
{
    public function rules()
    {
        return [
            'phone'    => ['required', new PhoneNumberAdminRule()],
            'password' => 'required',
        ];
    }

    public function messages()
    {
        return [
            'phone.required'    => __('auth.validation.phone.required'),
            'password.required' => __('auth.validation.password.required'),
        ];
    }
}
```

## Directory Structure

```
app/Http/Requests/
├── BaseRequest.php
├── Auth/
│   ├── SignInRequest.php
│   ├── UserRegisterRequest.php
│   └── PasswordChangeRequest.php
├── Coupon/
│   ├── CouponCreateRequest.php
│   └── CouponUserRequest.php
├── Cart/
└── ... (grouped by feature)
```

## Error Response Format

All validation failures return HTTP 400 with:

```json
{
    "error": {
        "message": "The phone field is required.",
        "summary": {
            "phone": ["The phone field is required."],
            "password": ["The password field is required."]
        }
    }
}
```

## Custom Validation Rules

Located in `app/Rules/`:

```php
<?php

namespace App\Rules;

use Illuminate\Contracts\Validation\Rule;

class PhoneNumberAdminRule implements Rule
{
    public function passes($attribute, $value)
    {
        return preg_match('/^[0-9]{9,20}$/', $value);
    }

    public function message()
    {
        return __('auth.validation.phone.format');
    }
}
```

## Conventions

- Use **array notation** for rules: `['required', 'email']`
- Group requests by **feature domain**: `Auth/`, `Coupon/`, `Cart/`
- Use **translation keys** for messages: `__('auth.validation.phone.required')`
- Always extend **BaseRequest**, not FormRequest directly
- `authorize()` returns `true` by default (auth handled by middleware)
