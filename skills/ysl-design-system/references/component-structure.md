# Component Structure

## View Page Layout

### Admin Panel (Vue 2 + Bootstrap-Vue)

```vue
<template>
  <div class="template-container feature-container clearfix">
    <!-- Action Box: Title + Action Buttons -->
    <div class="action-box pt-5 pb-4 pr-4 pl-4 clearfix">
      <div class="d-flex justify-content-between btn-line-height">
        <div class="blg-summary">
          <span class="text font-weight-bold">{{ $t("feature.header.title") }}</span>
        </div>
        <div class="blg-action d-flex d-row">
          <button class="btn btn-success ml-2" @click="$refs.createModal.show()">
            <span class="feather feather-plus-square" />
            <span>{{ $t("common.button.create") }}</span>
          </button>
        </div>
      </div>
    </div>

    <!-- Content Box: Filters + Data -->
    <div class="content-box pt-4 pb-4 pr-4 pl-4 clearfix">
      <!-- Filter card, data table, pagination -->
    </div>
  </div>
</template>
```

### Seller Portal (Vue 3 + CoreUI)

```vue
<template>
  <div class="d-flex flex-column gap-4">
    <!-- Page Header: Title + Actions -->
    <div class="d-flex algin-items-center justify-content-between mt-4">
      <h5 class="mb-0 text-center">{{ $t("sidebar.feature") }}</h5>
      <div class="d-flex algin-items-center gap-1 justify-content-center">
        <RouterLink :to="{ name: 'create_feature' }">
          <CButton color="primary">
            <CIcon name="cilPlus" /> {{ $t("button.create") }}
          </CButton>
        </RouterLink>
      </div>
    </div>

    <!-- Filter Component -->
    <FeatureFilter :on-filter="handleFilter" />

    <!-- Data Card with Tabs -->
    <CCard class="mb-4 mb-md-0">
      <CCardHeader>
        <CNav variant="tabs" class="card-header-tabs">
          <CNavItem>
            <CNavLink :active="tab === 'active'" @click="switchTab('active')">
              {{ $t("common.active") }}
            </CNavLink>
          </CNavItem>
        </CNav>
      </CCardHeader>
      <CCardBody>
        <FeatureList :items="items" />
      </CCardBody>
    </CCard>
  </div>
</template>
```

## Form Layout

### Admin (Bootstrap-Vue)

```vue
<div class="row">
  <div class="col-12">
    <label for="field">{{ $t("feature.field") }}</label>
  </div>
  <div class="col-12">
    <ValidationProvider v-slot="{ errors }" name="field" :rules="{ required: true }">
      <div :class="errors.length > 0 ? 'form-group is-invalid' : 'form-group'">
        <input id="field" v-model="field" type="text" class="form-control" />
        <div v-if="errors.length > 0" class="invalid-feedback">{{ errors[0] }}</div>
      </div>
    </ValidationProvider>
  </div>
</div>
```

### Seller (CoreUI)

```vue
<CRow class="mb-3">
  <CFormLabel for="field" class="col-sm-3 text-end col-form-label required">
    {{ $t("common.fields.label.field") }}
  </CFormLabel>
  <div class="col-sm-9">
    <CFormInput
      v-model="field"
      type="text"
      id="field"
      :class="validations.field ? 'is-invalid' : null"
    />
    <template v-if="validations.field">
      <div class="invalid-feedback">{{ validations.field[0] }}</div>
    </template>
  </div>
</CRow>
```

## Naming Conventions

### Files

| Type | Convention | Example |
|------|-----------|---------|
| Views | PascalCase | `ProductList.vue`, `SignIn.vue` |
| Components | PascalCase, descriptive | `ProductFilter.vue`, `ThumbViewer.vue` |
| Stores | camelCase | `product.js`, `auth.js` |
| Routes | camelCase | `catalog.js`, `order.js` |
| Translations | snake_case | `product.json`, `common.json` |

### CSS Classes

| Platform | Convention | Example |
|----------|-----------|---------|
| Admin | Bootstrap + custom | `action-box`, `content-box`, `blg-summary` |
| Seller | CoreUI utilities | `d-flex`, `gap-1`, `mb-3`, `text-end` |

## Confirmation Dialog Pattern

```javascript
// SweetAlert2 with brand colors
Swal.fire({
  title: t("common.are_you_sure"),
  text: t("common.are_you_sure_detail"),
  icon: "warning",
  showCancelButton: true,
  confirmButtonColor: "#129748",  // Brand green
  cancelButtonColor: "#9da5b1",   // Gray
  confirmButtonText: t("common.are_you_sure_confirmed"),
  cancelButtonText: t("button.cancel"),
});
```
