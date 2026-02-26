# Frontend Admin Implementation (young_sia_admin)

**Role**: Frontend Software Engineer (Vue.js & Bootstrap Vue Expert)

## 1. Menu Configuration

### Create Menu Item
**Location**: `young_sia_admin/resources/js/components/sidebar/MenuItem/{ResourceName}.vue`

```vue
<template>
  <li v-if="hasPermission">
    <router-link :to="{ name: 'resource.index' }" class="menu-item">
      <i class="icon-class"></i>
      <span>{{ $t('menu.resource_name') }}</span>
    </router-link>
  </li>
</template>

<script>
export default {
  name: 'ResourceMenuItem',
  computed: {
    hasPermission() {
      // Add authorization check logic
      return this.$store.getters['auth/hasPermission']('resource.view');
    }
  }
}
</script>
```

### Register Menu Item
**Location**: `young_sia_admin/resources/js/views/App.vue`

Add the new menu item component to the sidebar section with proper authorization checks.

## 2. API Configuration

**Location**: `young_sia_admin/resources/js/config/{resource}.js`

```javascript
export default {
  base: '/api/v1.0/resource-name',
  endpoints: {
    list: '',
    create: '',
    show: (id) => `/${id}`,
    update: (id) => `/${id}`,
    delete: (id) => `/${id}`,
  }
}
```

## 3. Vuex Store Module

**Location**: `young_sia_admin/resources/js/store/modules/{resource}.js`

```javascript
import api from '@/config/{resource}'

const state = {
  items: [],
  currentItem: null,
  loading: false,
  pagination: {
    current_page: 1,
    per_page: 15,
    total: 0
  }
}

const getters = {
  items: state => state.items,
  currentItem: state => state.currentItem,
  loading: state => state.loading,
  pagination: state => state.pagination
}

const mutations = {
  SET_ITEMS(state, items) {
    state.items = items
  },
  SET_CURRENT_ITEM(state, item) {
    state.currentItem = item
  },
  SET_LOADING(state, loading) {
    state.loading = loading
  },
  SET_PAGINATION(state, pagination) {
    state.pagination = pagination
  }
}

const actions = {
  async fetchItems({ commit }, params = {}) {
    commit('SET_LOADING', true)
    try {
      const response = await this.$axios.get(api.base + api.endpoints.list, { params })
      commit('SET_ITEMS', response.data.data)
      commit('SET_PAGINATION', response.data.meta)
      return response.data
    } catch (error) {
      throw error
    } finally {
      commit('SET_LOADING', false)
    }
  },

  async fetchItem({ commit }, id) {
    commit('SET_LOADING', true)
    try {
      const response = await this.$axios.get(api.base + api.endpoints.show(id))
      commit('SET_CURRENT_ITEM', response.data.data)
      return response.data
    } catch (error) {
      throw error
    } finally {
      commit('SET_LOADING', false)
    }
  },

  async createItem({ dispatch }, data) {
    const response = await this.$axios.post(api.base + api.endpoints.create, data)
    return response.data
  },

  async updateItem({ dispatch }, { id, data }) {
    const response = await this.$axios.put(api.base + api.endpoints.update(id), data)
    return response.data
  },

  async deleteItem({ dispatch }, id) {
    const response = await this.$axios.delete(api.base + api.endpoints.delete(id))
    return response.data
  }
}

export default {
  namespaced: true,
  state,
  getters,
  mutations,
  actions
}
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
