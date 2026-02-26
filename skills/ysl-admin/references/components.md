# Components

## Naming Convention

- **PascalCase** filenames: `UserList.vue`, `ThumbViewer.vue`
- **Descriptive names**: `AlipayAccountTransactionShowLogModal.vue`
- Views vs Components: views are page-level, components are reusable

## Component Architecture Rules

### Section Separation

- **Do not put all sections inside a single component**
- Each logical section should be its own component
- Feature-specific sub-components go in `views/{feature}/components/`
- This improves maintainability, reusability, and testability

### CSS Rules

- **Do not write scoped styles inside Vue components** (`<style scoped>` is forbidden)
- All component styles must be in the global main CSS file
- **Do not create additional CSS files**
- Use existing CSS classes from Bootstrap Vue and the design system

## View Component Structure

```
views/
├── account/
│   └── SignIn.vue
├── user/
│   ├── UserList.vue
│   └── components/       # Feature-specific sub-components
├── cargo/
│   └── CargoPickUp.vue
├── delivery/
│   └── Index.vue
├── house/
│   └── HouseList.vue
└── ... (70+ feature modules)
```

## View Component Pattern

```vue
<template>
  <div class="template-container feature-container clearfix">
    <!-- Action box -->
    <div class="action-box pt-5 pb-4 pr-4 pl-4 clearfix">
      <div class="d-flex justify-content-between btn-line-height">
        <div class="blg-summary">
          <span class="text font-weight-bold">{{ $t('feature.header.title') }}</span>
        </div>
        <div class="blg-action d-flex d-row">
          <button
            v-if="isAdmin"
            class="btn btn-success ml-2"
            @click="$refs.createModal.show()"
          >
            <span class="feather feather-plus-square" />
            <span>{{ $t("common.button.create") }}</span>
          </button>
        </div>
      </div>
    </div>

    <!-- Content box -->
    <div class="content-box pt-4 pb-4 pr-4 pl-4 clearfix">
      <!-- Filters, tables, charts -->
    </div>
  </div>
</template>

<script>
import { mapState, mapGetters } from "vuex";

export default {
  name: "FeatureList",
  computed: {
    ...mapState("feature", ["items", "pagination"]),
    ...mapGetters("user", ["isAdmin", "isWarehouse"]),
  },
  data() {
    return {
      showGraph: false,
      startDate: null,
      endDate: null,
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      const url = config.api.paths.features.list;
      this.$axios
        .get(url, HeaderUtils.header())
        .then((response) => {
          this.items = response.data.data;
          this.onResponseSuccess("Data loaded");
        })
        .catch((error) => {
          this.onResponseError(error);
        });
    },
  },
};
</script>
```

## Reusable Component Pattern

```
components/
├── image/
│   ├── ThumbViewer.vue
│   ├── MultipleThumbViewer.vue
│   └── Thumb.vue
├── comment/
│   ├── XComment.vue
│   ├── XCommentItem.vue
│   └── XCommentEmpty.vue
├── modal/
│   └── NotifyModal.vue
└── sidebar/
    └── MenuItem.vue
```

## Helpers Mixin (Global Methods)

Installed as a Vue plugin, available in all components:

```javascript
// Available via this.{method} in any component
this.getDate(date, format)       // Format date with Moment.js
this.toNumberDecimal(num, digit) // Format number
this.onResponseSuccess(title)    // Success toast notification
this.onResponseError(error)      // Error toast notification
this.getYslDefaultPagination()   // Default pagination object
```

## Key Patterns

- Use **feather icons**: `<span class="feather feather-plus-square" />`
- Use **Bootstrap Vue** components: `<b-table>`, `<b-modal>`, `<b-pagination>`
- Use **$t()** for all user-facing text (i18n in en, km, zh)
- Container class convention: `template-container {feature}-container`
- Action box + Content box layout structure
- **No scoped styles** — use global CSS only
- **Separate sections** into sub-components for maintainability
