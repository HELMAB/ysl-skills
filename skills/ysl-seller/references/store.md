# Pinia Stores

## Options API Store (Primary Pattern)

```javascript
// store/auth.js
import { defineStore } from "pinia";
import axios from "axios";
import { $cookies } from "@/config/cookies";
import api from "@/config/api";
import { USER_ROLE } from "@/config/constants";

export const useAuthStore = defineStore("authStore", {
  state: () => ({
    user: null,
    accessToken: null,
    isAuthenticated: false,
    shop: null,
  }),
  actions: {
    setUserData(payload) {
      const { user, access_token, shop } = payload;
      this.user = user;
      this.accessToken = access_token;
      this.isAuthenticated = true;
      this.shop = shop;
      axios.defaults.headers.common["X-Access-Token"] = access_token;
      $cookies.set("access_token", access_token, null, "/");
    },

    async getUserData() {
      const accessToken = $cookies.get("access_token");
      axios.defaults.headers.common["X-Access-Token"] = accessToken;
      return axios.get(api.getUserData);
    },

    isSeller() {
      return this.user?.user_role_id === USER_ROLE.SELLER;
    },

    isSellerEditor() {
      return this.user?.user_role_id === USER_ROLE.SELLER_EDITOR;
    },

    defaultRoute() {
      if (this.isSellerEditor()) return "list_products";
      return "dashboard";
    },

    logout() {
      this.user = null;
      this.accessToken = null;
      this.isAuthenticated = false;
    },
  },
});
```

## Feature Store Pattern

```javascript
// store/product.js
import { defineStore } from "pinia";
import axios from "axios";
import api from "@/config/api";

const defaultProductStates = {
  product_name: "",
  product_images: [],
  product_price: null,
  stock_quantity: null,
  // ...
};

export const useProductStore = defineStore("productStore", {
  state: () => ({
    type: "active",
    items: [],
    pagination: { current: 1, total: 0, next: null, previous: null },
    product: { ...defaultProductStates },
    validations: {},
  }),
  actions: {
    async listProducts(params) {
      if (this.search) params = { ...params, search: this.search };
      if (this.categoryId) params = { ...params, category_id: this.categoryId };

      const { data } = await axios.get(api.product.list, { params });
      this.items = data.items;
      this.pagination = data.pagination;
    },

    async saveProduct({ params, isUpdate = false, product_id = null }) {
      if (!isUpdate) {
        const response = await this.storeProduct(params);
        product_id = response?.data?.data?.id;
      } else {
        await this.updateProduct({ product_id, params });
      }
      return product_id;
    },

    resetProductState() {
      Object.assign(this.product, { ...defaultProductStates });
      this.validations = {};
    },
  },
});
```

## Composition API Store

```javascript
// store/upload.js
import { defineStore } from "pinia";
import { ref, computed } from "vue";
import axios from "axios";
import api from "@/config/api";

export const useUploadStore = defineStore("uploadStore", () => {
  const uploads = ref(new Map());
  const finishedUploaded = ref([]);

  const activeUploads = computed(() => {
    return Array.from(uploads.value.values());
  });

  const isUploading = computed(() => {
    return activeUploads.value.some(
      (upload) => upload.status === "uploading" || upload.status === "processing"
    );
  });

  const uploadVideo = async (file, thumbnail_url, product_id) => {
    const uploadId = Date.now().toString();
    const controller = new AbortController();
    const formData = new FormData();
    formData.append("video", file);
    formData.append("product_id", product_id);

    uploads.value.set(uploadId, {
      id: uploadId,
      progress: 0,
      status: "uploading",
      controller,
    });

    try {
      await axios.post(api.uploadVideo, formData, {
        signal: controller.signal,
        onUploadProgress: (e) => {
          const progress = Math.round((e.loaded * 100) / e.total);
          uploads.value.set(uploadId, { ...uploads.value.get(uploadId), progress });
        },
      });
      uploads.value.delete(uploadId);
    } catch (error) {
      uploads.value.set(uploadId, {
        ...uploads.value.get(uploadId),
        status: "error",
      });
    }
  };

  return { uploads, activeUploads, isUploading, uploadVideo, finishedUploaded };
});
```

## Loading Store

```javascript
// store/loading.js
import { defineStore } from "pinia";

export const useLoadingStore = defineStore("loadingStore", {
  state: () => ({
    isLoading: false,
  }),
  actions: {
    toggleLoading(value) {
      this.isLoading = value;
    },
  },
});
```

## Usage in Components

```vue
<script setup>
import { useProductStore } from "@/store/product";
import { useLoadingStore } from "@/store/loading";
import { storeToRefs } from "pinia";

// Destructure store
const productStore = useProductStore();
const { items, pagination, type } = storeToRefs(productStore); // Reactive refs
const { listProducts, resetProductState } = productStore; // Actions (not reactive)

// Loading pattern
const loadingStore = useLoadingStore();
const { toggleLoading } = loadingStore;

const fetchData = () => {
  toggleLoading(true);
  listProducts({ page: 1 }).finally(() => {
    toggleLoading(false);
  });
};
</script>
```
