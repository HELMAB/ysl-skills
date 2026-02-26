# Forms & Validation

## Validation Approach

The Seller UI uses **server-side validation** integrated with Pinia stores, displayed reactively on form fields. This differs from traditional Vee-Validate Form/Field component approach.

## Validation Flow

```
Form Submit → API Call → 422 Response → Extract Errors → Store in Pinia → Display on Fields
```

## Core Validation Pattern

### 1. Pinia Store with Validations

```javascript
// store/product.js
import { defineStore } from 'pinia';

export const useProductStore = defineStore('product', {
  state: () => ({
    product: {},
    validations: {},  // Server validation errors
  }),
  actions: {
    setValidations(errors) {
      this.validations = errors;
    },
    clearValidations() {
      this.validations = {};
    },
  },
});
```

### 2. Container Component (Create/Edit)

```vue
<!-- views/catalog/products/Create.vue -->
<script setup>
import { ref } from 'vue';
import { useProductStore } from '@/store/product';
import { useAxios } from '@/composables/useAxios';
import ProductForm from './components/ProductForm.vue';

const productStore = useProductStore();
const { axios } = useAxios();
const validations = ref({});

const handleSave = async () => {
  productStore.clearValidations();
  
  try {
    const formData = prepareFormData();
    await axios.post('/api/products', formData);
    // Success handling
  } catch (error) {
    const { status, data } = error.response || {};
    if (status === 422 && data.errors) {
      validations.value = data.errors;
      productStore.setValidations(data.errors);
    }
  }
};
</script>

<template>
  <ProductForm :validations="validations" @save="handleSave" />
</template>
```

### 3. Form Component with Validation Display

```vue
<!-- views/catalog/products/forms/ProductInfoForm.vue -->
<script setup>
import { computed } from 'vue';
import { storeToRefs } from 'pinia';
import { useProductStore } from '@/store/product';
import { useCommon } from '@/composables/useCommon';

const productStore = useProductStore();
const { product, validations } = storeToRefs(productStore);
const { checkIfKeyExist, getFeedback } = useCommon();
</script>

<template>
  <!-- Text Input with Validation -->
  <CRow class="mb-3">
    <CFormLabel class="col-sm-2 col-form-label required">
      {{ $t('form.product_stock_quantity') }}
    </CFormLabel>
    <CCol sm="10">
      <CFormInput
        type="number"
        v-model="product.stock_quantity"
        :invalid="checkIfKeyExist(validations, 'stock_quantity')"
      />
      <template v-if="checkIfKeyExist(validations, 'stock_quantity')">
        <AppFeedback :validations="validations" field="stock_quantity" />
      </template>
    </CCol>
  </CRow>

  <!-- Input with Currency Group -->
  <CRow class="mb-3">
    <CFormLabel class="col-sm-2 col-form-label required">
      {{ $t('form.product_price') }}
    </CFormLabel>
    <CCol sm="10">
      <CInputGroup>
        <CFormInput
          type="number"
          v-model="product.product_local_currency_price"
          :invalid="checkIfKeyExist(validations, 'product_local_currency_price')"
        />
        <CInputGroupText>{{ baseCurrencyCode }}</CInputGroupText>
        <AppFeedback :validations="validations" field="product_local_currency_price" />
      </CInputGroup>
    </CCol>
  </CRow>

  <!-- Multi-Select with Validation -->
  <CRow class="mb-3">
    <CFormLabel class="col-sm-2 col-form-label">
      {{ $t('form.store_categories') }}
    </CFormLabel>
    <CCol sm="10">
      <AppMultiSelect
        :multiple="true"
        :options="productCategories"
        v-model="product.categories"
        :class="{ 'is-invalid': validations && validations.categories }"
      />
      <AppFeedback :validations="validations" field="categories" />
    </CCol>
  </CRow>
</template>
```

## Validation Helper Functions

```javascript
// composables/useCommon.js
export function useCommon() {
  const checkIfKeyExist = (validations, key) => {
    return validations && validations[key] !== undefined;
  };

  const getFeedback = (validations, key) => {
    if (!validations || !validations[key]) return '';
    return Array.isArray(validations[key]) 
      ? validations[key][0] 
      : validations[key];
  };

  const extractValidations = ({ response }) => {
    if (response?.data?.errors) return response.data.errors;
    return {};
  };

  return { checkIfKeyExist, getFeedback, extractValidations };
}
```

## AppFeedback Component

```vue
<!-- components/Utils/AppFeedback.vue -->
<script setup>
const props = defineProps({
  validations: { type: Object, default: () => ({}) },
  field: { type: String, required: true },
});

const errorMessage = computed(() => {
  const errors = props.validations[props.field];
  if (!errors) return '';
  return Array.isArray(errors) ? errors[0] : errors;
});
</script>

<template>
  <div v-if="errorMessage" class="invalid-feedback d-block">
    {{ errorMessage }}
  </div>
</template>
```

## Validation Styling

```scss
// assets/scss/components/forms.scss
.multiselect.is-invalid {
  border: 1px solid red;
  border-radius: 6px;
}

.invalid-feedback {
  color: #dc3545;
  font-size: 0.875em;
  margin-top: 0.25rem;
}

.required::after {
  content: " *";
  color: red;
}
```

## Client-Side Validation (Optional)

For immediate feedback before API call:

```vue
<script setup>
const clientValidationErrors = ref({});

const validateForm = () => {
  const errors = {};
  
  if (!product.value.product_name) {
    errors.product_name = ['Product name is required'];
  }
  if (product.value.stock_quantity < 0) {
    errors.stock_quantity = ['Stock quantity must be positive'];
  }
  
  clientValidationErrors.value = errors;
  return Object.keys(errors).length === 0;
};

const handleSave = async () => {
  if (!validateForm()) return;
  // Proceed with API call...
};
</script>
```

## Form Conventions

| Convention | Description |
|------------|-------------|
| Error storage | Pinia store `validations` state |
| Error display | `AppFeedback` component |
| Invalid styling | `:invalid` prop + `is-invalid` class |
| Required fields | `.required` class on label |
| Clear on submit | `clearValidations()` before API call |
