# Styling

## SCSS Architecture (Seller UI)

```scss
// assets/scss/_variables.scss
$primary: #129748;
$success: #00c0ef;
$danger: #ff4136;
$secondary: #9da5b1;
$sidebar-bg: $primary;

$enable-ltr: true;
$enable-rtl: true;
$font-family-sans-serif: "Lato", "Kantumruy Pro", sans-serif;
```

## SCSS Import Order

```scss
// assets/scss/app.scss
@import "variables";

// CoreUI base
@import "~@coreui/coreui/scss/coreui";
@import "~@coreui/coreui-pro/scss/variables";
@import "~@coreui/coreui-pro/scss/calendar";
@import "~@coreui/coreui-pro/scss/date-picker";

// Charts
@import "~@coreui/chartjs/scss/tooltips";

// Vue plugins
@import "vue-toast-notification/dist/theme-bootstrap.css";

// Custom component styles
@import "components/notification";
@import "components/layout";
@import "components/custom";
@import "components/loading";
@import "components/button";
@import "components/table";
@import "components/product";
@import "components/sweet_alert";
@import "components/multi_select";
@import "components/common";
@import "components/thumb";
@import "common/upload_progress";

// Page styles
@import "pages/404.scss";
@import "pages/403.scss";
```

## Global CSS Patterns

### Required Field Indicator

```scss
.required::after {
  content: " *";
  color: red;
}
```

### Button Color Override

```scss
.btn-primary,
.btn-info,
.btn-secondary,
.btn-success,
.btn-warning,
.btn-danger {
  color: white !important;
}
```

### Utility Classes

```scss
.cursor-pointer {
  cursor: pointer;
}

.min-width-actions {
  width: 120px;
}

.multiselect.is-invalid {
  border: 1px solid red;
  border-radius: 6px;
}
```

## Style Rules

1. **Global CSS preferred** — Add styles to SCSS component files under `assets/scss/components/`
2. **Empty scoped blocks** — Components include `<style lang="scss" scoped></style>` but keep it empty; styles go in global SCSS
3. **No inline styles** — Use classes defined in global SCSS
4. **CoreUI utility classes** — Use CoreUI/Bootstrap utilities (`d-flex`, `gap-1`, `mb-3`, etc.)
5. **SweetAlert2 theming** — Confirm button uses `#129748`, cancel uses `#9da5b1`

## Admin Panel CSS

The admin panel uses Bootstrap Vue's built-in styling with custom overrides:

- Container classes: `template-container {feature}-container`
- Action layout: `action-box` + `content-box`
- Icons: Feather icons via `<span class="feather feather-{name}" />`
- Buttons: Bootstrap classes (`btn btn-success`, `btn btn-outline-secondary`)

## Seller UI CSS

The seller portal uses CoreUI's built-in styling:

- Layout: `CContainer`, `CRow`, `CCol`
- Cards: `CCard`, `CCardHeader`, `CCardBody`
- Forms: `CFormInput`, `CFormSelect`, `CFormLabel`
- Validation: `is-invalid` class + `invalid-feedback` div
- Icons: CoreUI icons via `<CIcon name="cilPlus" />`
