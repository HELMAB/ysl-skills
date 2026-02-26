# Components

## `<script setup>` Pattern

All components use Composition API with `<script setup>`:

```vue
<template>
  <div class="d-flex flex-column gap-4">
    <div class="d-flex algin-items-center justify-content-between mt-4">
      <h5 class="mb-0 text-center">{{ $t("sidebar.products") }}</h5>
      <div class="d-flex algin-items-center gap-1 justify-content-center">
        <RouterLink :to="{ name: 'create_products' }">
          <CButton color="primary">
            <CIcon name="cilPlus" /> {{ $t("button.new_product") }}
          </CButton>
        </RouterLink>
      </div>
    </div>

    <ProductFilter :get-list-products="getListProducts" />

    <CCard class="mb-4 mb-md-0">
      <CCardHeader>
        <CNav variant="tabs" class="card-header-tabs">
          <CNavItem>
            <CNavLink
              href="javascript:void(0);"
              :active="type === 'active'"
              @click="switchTabs('active')"
            >
              {{ $t("common.active") }}
            </CNavLink>
          </CNavItem>
        </CNav>
      </CCardHeader>
      <CCardBody>
        <ProductList :page="page" :get-list-products="getListProducts" />
      </CCardBody>
    </CCard>
  </div>
</template>

<script setup>
import ProductList from "./components/ProductList.vue";
import ProductFilter from "./components/ProductFilter.vue";
import { useProductStore } from "@/store/product";
import { useLoadingStore } from "@/store/loading";
import { storeToRefs } from "pinia";
import { onMounted, ref } from "vue";

const loadingStore = useLoadingStore();
const { toggleLoading } = loadingStore;

const productStore = useProductStore();
const { type } = storeToRefs(productStore);
const { listProducts } = productStore;

const page = ref(1);

const getListProducts = (p = 1) => {
  toggleLoading(true);
  listProducts({ page: p, type: type.value }).finally(() => {
    toggleLoading(false);
  });
};

const switchTabs = (newTab) => {
  type.value = newTab;
};

onMounted(() => {
  getListProducts();
});
</script>

<style lang="scss" scoped></style>
```

## CoreUI Components

Common CoreUI components used throughout:

```vue
<!-- Layout -->
<CContainer fluid>
<CRow><CCol md="6">...</CCol></CRow>

<!-- Cards -->
<CCard><CCardHeader>...</CCardHeader><CCardBody>...</CCardBody></CCard>

<!-- Navigation -->
<CNav variant="tabs"><CNavItem><CNavLink>...</CNavLink></CNavItem></CNav>

<!-- Forms -->
<CForm @submit.prevent="onSubmit">
<CFormLabel class="required">Name</CFormLabel>
<CFormInput v-model="name" :class="validations.name ? 'is-invalid' : null" />
<CFormSelect v-model="status">...</CFormSelect>

<!-- Buttons -->
<CButton color="primary">Save</CButton>
<CButton color="info">Update</CButton>
<CButton color="secondary">Cancel</CButton>

<!-- Icons -->
<CIcon name="cilPlus" />
<CIcon icon="cil-menu" size="lg" />
```

## Form Component Pattern

```vue
<template>
  <CForm @submit.prevent="updateProfile">
    <CRow class="mb-3">
      <CFormLabel for="name" class="col-sm-3 text-end col-form-label required">
        {{ $t("common.fields.label.name") }}
      </CFormLabel>
      <div class="col-sm-9">
        <CFormInput
          v-model="name"
          type="text"
          id="name"
          :placeholder="$t('common.fields.label.name')"
          :class="`${validations.name ? 'is-invalid' : null}`"
        />
        <template v-if="validations.name">
          <div class="invalid-feedback">{{ validations.name[0] }}</div>
        </template>
      </div>
    </CRow>

    <CRow>
      <div class="col-sm-9 offset-sm-3">
        <div class="d-flex gap-1">
          <CButton type="submit" color="info">{{ $t("button.update") }}</CButton>
          <CButton color="secondary" @click="handleCancel">{{ $t("button.cancel") }}</CButton>
        </div>
      </div>
    </CRow>
  </CForm>
</template>

<script setup>
import { ref, toRefs } from "vue";
import { useAuthStore } from "@/store/auth";
import { useNotify } from "@/composables/useNotify";
import { useCommon } from "@/composables/useCommon";
import { useI18n } from "vue-i18n";
import { storeToRefs } from "pinia";

const { extractValidations, onResponseError } = useCommon();
const { t } = useI18n();
const { notify } = useNotify();
const authStore = useAuthStore();
const { user } = storeToRefs(authStore);
const { name, gender } = toRefs(user.value);

const validations = ref({});
const emit = defineEmits(["handleCancel"]);

const updateProfile = () => {
  validations.value = {};
  authStore
    .updateSellerProfile({ name: name.value, gender: gender.value })
    .then(({ data }) => {
      notify(t("common.update_profile"), t("common.update_profile_success"));
      emit("handleCancel");
    })
    .catch((error) => {
      validations.value = extractValidations(error);
      onResponseError(error);
    });
};
</script>
```

## Auto-Registered Components

All `App*.vue` files in `components/Utils/` are auto-registered globally:

- `AppDatePicker.vue` — Date picker wrapper
- `AppLanguageSwitcher.vue` — Locale switcher
- `AppBreadcrumb.vue` — Dynamic breadcrumbs

## File Organization

```
views/{feature}/
├── Index.vue          # Layout/wrapper (optional)
├── List.vue           # List page
├── Create.vue         # Create page
├── Show.vue           # Detail page
├── Edit.vue           # Edit page
└── components/        # Feature-specific sub-components
    ├── ProductList.vue
    ├── ProductFilter.vue
    └── ProductForm.vue
```

## Key Patterns

- `storeToRefs()` for reactive store state destructuring
- `toRef()` / `toRefs()` for nested reactive references
- `defineEmits()` for component events
- `defineProps()` for component props
- Server-side validation errors mapped to `validations` ref
- Loading state via `useLoadingStore().toggleLoading()`
- All text via `$t()` or `t()` from `useI18n()`
