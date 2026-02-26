# Forms & Validation

## Vee-Validate 3 Setup

```javascript
// vueapp.js
import { ValidationProvider, ValidationObserver } from "vee-validate";

Vue.component("ValidationProvider", ValidationProvider);
Vue.component("ValidationObserver", ValidationObserver);
```

## Form Pattern

```vue
<template>
  <ValidationObserver ref="form" v-slot="{ invalid }" slim>
    <form class="form-account" @submit.prevent="onSubmit">
      <div class="row">
        <div class="col-12">
          <label for="phone">{{ $t("account.input.phone_number") }}</label>
        </div>
        <div class="col-12">
          <ValidationProvider
            v-slot="{ errors }"
            name="phone"
            :rules="{ required: true, min_phone: 9, max_phone: 20 }"
            slim
          >
            <div :class="errors.length > 0 ? 'form-group is-invalid' : 'form-group'">
              <input
                id="phone"
                v-model="phone"
                type="text"
                class="form-control"
                :placeholder="$t('account.input.phone_number')"
              />
              <div v-if="errors.length > 0" class="invalid-feedback">
                {{ errors[0] }}
              </div>
            </div>
          </ValidationProvider>
        </div>
      </div>

      <div class="row">
        <div class="col-12">
          <label for="password">{{ $t("account.input.password") }}</label>
        </div>
        <div class="col-12">
          <ValidationProvider
            v-slot="{ errors }"
            name="password"
            :rules="{ required: true, min_password: 8 }"
            slim
          >
            <div :class="errors.length > 0 ? 'form-group is-invalid' : 'form-group'">
              <input
                id="password"
                v-model="password"
                type="password"
                class="form-control"
                :placeholder="$t('account.input.password')"
              />
              <div v-if="errors.length > 0" class="invalid-feedback">
                {{ errors[0] }}
              </div>
            </div>
          </ValidationProvider>
        </div>
      </div>

      <button type="submit" class="btn btn-success" :disabled="invalid">
        {{ $t("common.button.submit") }}
      </button>
    </form>
  </ValidationObserver>
</template>

<script>
export default {
  name: "SignIn",
  data() {
    return {
      phone: null,
      password: null,
    };
  },
  methods: {
    onSubmit() {
      this.$refs.form.validate().then((success) => {
        if (success) {
          // Submit form via API
        }
      });
    },
  },
};
</script>
```

## Key Patterns

- **ValidationObserver** wraps the entire form with `ref="form"` and `slim`
- **ValidationProvider** wraps each field with scoped slot `v-slot="{ errors }"`
- **`slim`** attribute prevents extra DOM wrapper elements
- Error display: conditional class `is-invalid` + `invalid-feedback` div
- Form submission: `@submit.prevent` + manual `this.$refs.form.validate()`
- Rules use **object syntax**: `{ required: true, min_phone: 9 }`

## Available Validation Rules

- `required` — Field must have a value
- `email` — Valid email format
- `min_phone` / `max_phone` — Phone number length
- `min_password` — Minimum password length
- Custom rules can be added via `extend()`

## API Error Handling

```javascript
methods: {
  onSubmit() {
    this.$axios
      .post(url, formData)
      .then((response) => {
        this.onResponseSuccess("Created successfully");
      })
      .catch((error) => {
        // Display API validation errors
        this.onResponseError(error);
      });
  },
}
```
