# Composables

## useCommon — Core Utility Composable

The most-used composable with 20+ functions:

```javascript
// composables/useCommon.js
import { useAuthStore } from "@/store/auth";
import { format as formatDate, parseISO, isValid } from "date-fns";
import { computed } from "vue";
import { useI18n } from "vue-i18n";
import Swal from "sweetalert2";
import { useNotify } from "./useNotify";

export function useCommon() {
  const { t } = useI18n();
  const authStore = useAuthStore();

  const baseCurrencyCode = computed(() => authStore.shop?.currency?.code || "USD");

  const getDate = (date, format = "yyyy-MM-dd hh:mm:ss a") => {
    if (!date) return null;
    const parsedDate = typeof date === "string" ? parseISO(date) : new Date(date);
    return isValid(parsedDate) ? formatDate(parsedDate, format) : null;
  };

  const numberToCurrency = (num, currency = "USD") => {
    if (typeof num !== "number") num = parseFloat(num);
    return new Intl.NumberFormat("en-US", {
      style: "currency",
      currency,
      minimumFractionDigits: 2,
    }).format(num);
  };

  const onConfirm = (options, callback) => {
    Swal.fire({
      title: t("common.are_you_sure"),
      text: t("common.are_you_sure_detail"),
      icon: "warning",
      showCancelButton: true,
      confirmButtonColor: "#129748",
      cancelButtonColor: "#9da5b1",
      confirmButtonText: t("common.are_you_sure_confirmed"),
      cancelButtonText: t("button.cancel"),
      ...options,
    }).then((result) => {
      if (result.isConfirmed) callback();
    });
  };

  const extractValidations = ({ response }) => {
    if (response?.data?.errors) return response.data.errors;
    return {};
  };

  return {
    baseCurrencyCode,
    getDate,
    numberToCurrency,
    onConfirm,
    extractValidations,
    // ... more utilities
  };
}
```

## useNotify — Toast Notifications

```javascript
// composables/useNotify.js
import { useToast } from "vue-toast-notification";

const toast = useToast({ position: "top-right" });

export function useNotify() {
  const notify = (title, text = null, type = "success", callback) => {
    const message = text ? `<b>${title}</b><br/>${text}` : title;
    const toastFn = { success: toast.success, info: toast.info, warning: toast.warning, error: toast.error }[type] || toast;
    toastFn(message, { onClick: callback });
  };

  return { notify };
}
```

## useAxios — HTTP Client

```javascript
// composables/useAxios.js
import { inject } from "vue";
import { useCookies } from "vue3-cookies";

const { cookies } = useCookies();

export function useAxios() {
  const axios = inject("axios");

  const setAuthToken = (token) => {
    axios.defaults.headers.common["X-Access-Token"] = token;
    cookies.set("access_token", token);
  };

  const removeAuthToken = () => {
    delete axios.defaults.headers.common["X-Access-Token"];
    cookies.remove("access_token");
  };

  return { axios, setAuthToken, removeAuthToken };
}
```

## useFirebase — Push Notifications

```javascript
// composables/useFirebase.js
import { initializeApp } from "firebase/app";
import { getMessaging, getToken, onMessage } from "firebase/messaging";

export function useFirebase() {
  const setupFirebaseMessaging = async () => {
    const permission = await Notification.requestPermission();
    if (permission === "granted") {
      const currentToken = await getToken(messaging, {
        vapidKey: process.env.VUE_APP_FIREBASE_VAPID_KEY,
      });
      if (currentToken) await sendTokenToBackend(currentToken);
    }

    onMessage(messaging, (payload) => {
      const { data } = payload;
      notify(data.title, data.body, "success", () => {
        window.location.href = data.click_action;
      });
    });
  };

  return { setupFirebaseMessaging };
}
```

## Usage Patterns

```vue
<script setup>
import { useCommon } from "@/composables/useCommon";
import { useNotify } from "@/composables/useNotify";

const { getDate, onConfirm, extractValidations, numberToCurrency } = useCommon();
const { notify } = useNotify();

// Format date
const formatted = getDate(item.created_at);

// Confirm dialog
const handleDelete = () => {
  onConfirm({}, () => {
    // Delete action
  });
};

// Toast notification
notify("Success", "Item created successfully");

// Currency formatting
const price = numberToCurrency(29.99, "USD"); // "$29.99"

// Extract server validation errors
try {
  await saveItem();
} catch (error) {
  validations.value = extractValidations(error);
}
</script>
```
