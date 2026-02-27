# Frontend Admin Implementation (young_sia_admin)

**Role**: Frontend Software Engineer (Vue.js & Bootstrap Vue Expert)

## 1. Menu Configuration

### Create Menu Item
**Location**: `young_sia_admin/resources/js/components/sidebar/MenuItem/{ResourceName}.vue`

```vue
<template>
  <div
    v-if="isAdmin"
    class="nav__item"
  >
    <router-link
      :to="{ name: 'resource.index' }"
      class="link"
    >
      <i class="icon-class"></i>
      <span>{{ $t('menu.resource_name') }}</span>
    </router-link>
  </div>
</template>

<script>
import { mapGetters } from "vuex";

export default {
  name: "ResourceMenuItem",
  computed: {
    ...mapGetters({
      isAdmin: "user/isAdmin"
    })
  }
};
</script>

<style lang="scss" scoped></style>
```

### Register Menu Item
**Location**: `young_sia_admin/resources/js/views/App.vue`

Add the new menu item component to the sidebar section with proper authorization checks.

## 2. API Configuration

**Location**: `young_sia_admin/resources/js/config/{resource}.js`

```javascript
export const shippingType = {
  list: "back-office/{resource}s",
  show: "back-office/{resource}s",
  update: "back-office/{resource}s"
};
```

## 3. Vuex Store Module

**Location**: `young_sia_admin/resources/js/store/modules/post.js`

```javascript
import { getField, updateField } from "vuex-map-fields";
import config from "../../config";
import AxiosInstance from "../../utils/http";

const state = () => {
  return {
    posts: [],
    title: null,
    pagination: {
      current: 1,
      next: null,
      previous: null,
      total: 0
    }
  };
};

const getters = {
  getField
};

const mutations = {
  updateField,
  setPosts(state, { posts, pagination }) {
    state.posts = posts;
    state.pagination = pagination;
  }
};

const actions = {
  getPosts({ commit }, params) {
    const query = new URLSearchParams(params);
    const base = config.api.base;
    const posts = config.api.paths.posts;
    return new Promise((resolve, reject) => {
      AxiosInstance.get(`${base}/${posts.list}?${query}`)
        .then(({ data }) => {
          commit("setPosts", {
            posts: data.data,
            pagination: data.pagination
          });
          resolve(data);
        })
        .catch(error => {
          reject(error);
        });
    });
  },
  getPost(_, id) {
    const base = config.api.base;
    const posts = config.api.paths.posts;
    return new Promise((resolve, reject) => {
      AxiosInstance.get(`${base}/${posts.show}/${id}`)
        .then(({ data }) => {
          resolve(data);
        })
        .catch(error => {
          reject(error);
        });
    });
  },
  createPost(_, payload) {
    const base = config.api.base;
    const posts = config.api.paths.posts;
    return new Promise((resolve, reject) => {
      AxiosInstance.post(`${base}/${posts.create}`, payload)
        .then(response => {
          resolve(response);
        })
        .catch(error => {
          reject(error);
        });
    });
  },
  updatePost(_, {id, payload}) {
    const base = config.api.base;
    const posts = config.api.paths.posts;
    return new Promise((resolve, reject) => {
      AxiosInstance.post(`${base}/${posts.edit}/${id}`, payload)
        .then(response => {
          resolve(response);
        })
        .catch(error => {
          reject(error);
        });
    });
  },
  deletePost(_, id) {
    const base = config.api.base;
    const posts = config.api.paths.posts;
    return new Promise((resolve, reject) => {
      AxiosInstance.delete(`${base}/${posts.delete}/${id}`)
        .then(response => {
          resolve(response);
        })
        .catch(error => {
          reject(error);
        });
    });
  },
  deletePostImage(_, id) {
    const base = config.api.base;
    const posts = config.api.paths.posts;
    return new Promise((resolve, reject) => {
      AxiosInstance.post(`${base}/${posts.deleteImage}`, { id })
        .then(response => {
          resolve(response);
        })
        .catch(error => {
          reject(error);
        });
    });
  },
};

export default {
  namespaced: true,
  state,
  getters,
  mutations,
  actions
};
```

## 4. Router Configuration

**Location**: `young_sia_admin/resources/js/router/{resource}.js`

```javascript
export default [
  {
    path: '/resource-name',
    name: 'resource.index',
    component: () => import('@/views/{Resource}/Index.vue'),
    meta: {
      title: 'Resource Management',
      requiresAuth: true,
      permission: 'resource.view'
    }
  },
  {
    path: '/resource-name/create',
    name: 'resource.create',
    component: () => import('@/views/{Resource}/Create.vue'),
    meta: {
      title: 'Create Resource',
      requiresAuth: true,
      permission: 'resource.create'
    }
  },
  {
    path: '/resource-name/:id',
    name: 'resource.show',
    component: () => import('@/views/{Resource}/Show.vue'),
    meta: {
      title: 'View Resource',
      requiresAuth: true,
      permission: 'resource.view'
    }
  },
  {
    path: '/resource-name/:id/edit',
    name: 'resource.edit',
    component: () => import('@/views/{Resource}/Edit.vue'),
    meta: {
      title: 'Edit Resource',
      requiresAuth: true,
      permission: 'resource.edit'
    }
  }
]
```

## 5. Reusable Components and Utilities

- **Existing Components**: `young_sia_admin/resources/js/components/`
  - Look for data tables, modals, form inputs, filters
  - Reuse instead of creating duplicates
  
- **Helper Functions**: `young_sia_admin/resources/js/utils/`
  - Date formatting, validation helpers, API utilities
  - Import and use existing utilities

## 6. Translations

**Location**: `young_sia_admin/resources/js/locale/{en,km,zh}/{resource}.js`

```javascript
export default {
  title: 'Resource Management',
  list: 'Resource List',
  create: 'Create New Resource',
  edit: 'Edit Resource',
  view: 'View Resource',
  delete: 'Delete Resource',
  
  fields: {
    field1: 'Field 1',
    field2: 'Field 2',
  },
  
  actions: {
    save: 'Save',
    cancel: 'Cancel',
    delete: 'Delete',
    edit: 'Edit',
    view: 'View',
  },
  
  messages: {
    createSuccess: 'Resource created successfully',
    updateSuccess: 'Resource updated successfully',
    deleteSuccess: 'Resource deleted successfully',
    deleteConfirm: 'Are you sure you want to delete this resource?',
  }
}
```

## 7. Vue.js Best Practices

- **Use Vue 2 Options API** (not Composition API)
- **Use Bootstrap Vue components** extensively
- Follow component naming conventions (PascalCase for component names)
- Use Vuex for state management
- Implement proper error handling and loading states
- Add authorization checks for actions
- Use `mapGetters` and `mapActions` for cleaner code
- Emit events from child to parent components
- Use props for parent-to-child communication
