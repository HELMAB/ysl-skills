# Vuex Store

## Root Store

```javascript
// store/index.js
import Vue from "vue";
import Vuex from "vuex";
import user from "./modules/user";
import { cargo } from "./modules/cargo";
// ... 60+ module imports

Vue.use(Vuex);

export default new Vuex.Store({
  modules: {
    user,
    cargo,
    // ... all modules registered
  },
  state: {
    actionTitle: null,
    accessToken: localStorage.getItem("access_token") || "",
    deviceToken: Utils.getDeviceToken(),
    serverBase: Utils.getServerBase(),
    serverApi: Utils.getServerApi(),
    apiVersion: Utils.getApiVersion(),
    loadingOverlay: false,
    skipLoadingOverlay: false,
    isAppInitLoading: false,
  },
  mutations: {
    updateAccessToken(state, accessToken) {
      state.accessToken = accessToken;
    },
    updateActionTitle(state, actionTitle) {
      state.actionTitle = actionTitle;
    },
    setLoadingOverlay(state, value) {
      /* ... */
    },
  },
  getters: {
    getAccessToken: (state) => state.accessToken,
    getDeviceToken: (state) => state.deviceToken,
    getServerBase: (state) => state.serverBase,
    getLoadingOverlay: (state) => state.loadingOverlay,
  },
});
```

## Module Pattern

```javascript
// store/modules/cargo.js
import { getField, updateField } from "vuex-map-fields";

export const cargo = {
  namespaced: true,
  state: {
    activeTab: "available_pickup",
    tabs: tabs,
    shippedHouseIds: [],
    filter: defaultFilter,
    cargoes: {
      data: [],
      import: [],
      houses: [],
      histories: [],
      total: { weights: null, volumes: null },
    },
  },
  getters: {
    getField,
    // Custom getters
  },
  mutations: {
    updateField,
    // Custom mutations
    resetFilter(state) {
      state.filter = { ...defaultFilter };
    },
  },
  actions: {
    async fetchCargoes({ commit, state }, params) {
      // API calls
    },
  },
};
```

## Key Patterns

- **Namespaced modules** for isolation
- **`vuex-map-fields`** for two-way binding with `getField`/`updateField`
- **60+ modules** managing different features
- Global root state for auth and loading overlay
- Access token stored in both Vuex and `localStorage`

## Usage in Components

```vue
<script>
import { mapState, mapGetters, mapMutations, mapActions } from "vuex";

export default {
  computed: {
    ...mapState("cargo", ["cargoes", "filter"]),
    ...mapGetters("user", ["isAdmin"]),
  },
  methods: {
    ...mapMutations("cargo", ["resetFilter"]),
    ...mapActions("cargo", ["fetchCargoes"]),
  },
};
</script>
```

## Two-Way Binding with vuex-map-fields

```vue
<script>
import { mapFields } from "vuex-map-fields";

export default {
  computed: {
    ...mapFields("cargo", ["filter.startDate", "filter.endDate", "filter.status"]),
  },
};
</script>
```
