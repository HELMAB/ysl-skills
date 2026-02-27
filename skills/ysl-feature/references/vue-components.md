# Vue Component Templates (young_sia_admin)

**Scope**: Real Vue 2 + Bootstrap Vue patterns from `young_sia_admin/resources/js/views/`.

## Quick Reference

| View type | Example path | Notes |
| --- | --- | --- |
| Index (list) | `young_sia_admin/resources/js/views/bank/Index.vue` | Table listing with action buttons and permission-aware UI. |
| Create | `young_sia_admin/resources/js/views/bank/Create.vue` | Header actions, form component, submit handler. |
| Edit | `young_sia_admin/resources/js/views/bank/Edit.vue` | Fetch by route param, reuse form, update handler. |
| Form | `young_sia_admin/resources/js/views/country/Form.vue` | Validations, multilingual inputs, file uploads. |
| Show (detail) | `young_sia_admin/resources/js/views/merchants/Show.vue` | Detail view composed of sub-components. |

| Component name | Example from script | Notes |
| --- | --- | --- |
| Index view | `BanksIndex` | Uses `mapFields` and `mapActions` for list data. |
| Create view | `BankCreate` | Uses `BankForm` + `createBranch`. |
| Edit view | `BankEdit` | Uses `BankForm` + `updateBranch` + `getBank`. |
| Form view | `CountryForm` | Builds `FormData` in `getFormData`. |
| Show view | `MerchantDetail` | Uses `showMerchant` action and child detail components. |

## Index.vue (List View)

**Example**: `young_sia_admin/resources/js/views/bank/Index.vue`

```vue
<template>
  <div class="template-container clearfix">
    <XHeader :title="$t('banks.header.title')">
      <router-link
        :to="{ name: 'banks.create' }"
        type="button"
        class="btn btn-success btn-common"
      >
        <span class="feather feather-plus-square" />
        <span class="text"> {{ $t("banks.button.add") }}</span>
      </router-link>
    </XHeader>

    <div class="content-box pb-4 pr-4 pl-4 clearfix">
      <div class="card p-4">
        <div class="overflow-auto stick-button">
          <table class="table app-table table-analyze">
            <thead class="thead-light text-nowrap">
              <tr>
                <th scope="col">
                  {{ $t("banks.input.icon") }}
                </th>
                <th scope="col">
                  {{ $t("banks.input.logo") }}
                </th>
                <th scope="col">
                  {{ $t("banks.input.bank_slip") }}
                </th>
                <th scope="col">
                  {{ $t("banks.input.name") }}
                </th>
                <th scope="col">
                  {{ $t("banks.input.code") }}
                </th>
                <th scope="col">
                  {{ $t("banks.input.account_name") }}
                </th>
                <th scope="col">
                  {{ $t("banks.input.account_number") }}
                </th>
                <th scope="col">
                  {{ $t("banks.input.ordering") }}
                </th>
                <th scope="col">
                  {{ $t("common.navigation.is_active") }}
                </th>
                <th scope="col">
                  {{ $t("banks.input.is_top_up_allowed") }}
                </th>
                <th scope="col">
                  {{ $t("banks.input.is_withdraw_allowed") }}
                </th>
                <th scope="col">
                  {{ $t("banks.input.is_allow_partner") }}
                </th>
                <th
                  scope="col"
                  class="item-action text-right"
                >
                  {{ $t("common.navigation.actions") }}
                </th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="(bank, key) in banks"
                :key="key"
              >
                <td>
                  <XThumb
                    :url="bank.circle_logo"
                    class="img-square"
                  />
                </td>
                <td>
                  <XThumb
                    :url="bank.logo"
                    class="img-square"
                  />
                </td>
                <td>
                  <XThumb
                    :url="bank.bank_slip"
                    class="img-square"
                  />
                </td>
                <td>{{ bank.name }}<br><small>{{ bank.description }}</small></td>
                <td>{{ bank.code }}</td>
                <td>{{ bank.account_name }}</td>
                <td>{{ bank.account_number }}</td>
                <td>{{ bank.ordering }}</td>
                <td><XBoolean :value="bank.is_active" /></td>
                <td><XBoolean :value="bank.is_top_up_allowed" /></td>
                <td><XBoolean :value="bank.is_withdraw_allowed" /></td>
                <td><XBoolean :value="bank.is_partner" /></td>
                <td class="text-right">
                  <button
                    v-b-tooltip.hover.bottom="$t('common.button.edit').toUpperCase()"
                    type="button"
                    class="btn btn-primary btn-icon-small btn-icon-small-light text-white"
                    @click="() => $router.push({ name: 'banks.edit', params: { id: bank.id }})"
                  >
                    <span class="feather feather-edit-2" />
                  </button>
                </td>
              </tr>
              <tr v-if="(banks.length || 0) === 0">
                <td colspan="12">
                  {{ $t("common.empty_data") }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>
```

```vue
<script>
import { mapFields } from "vuex-map-fields";
import { mapActions } from "vuex";

export default {
  name: "BanksIndex",
  computed: {
    ...mapFields("bank", [
      "banks"
    ])
  },
  created() {
    this.getBanks();
  },
  methods: {
    ...mapActions("bank", ["getBanks"])
  },
};
</script>
```

## Create.vue (Create View)

**Example**: `young_sia_admin/resources/js/views/bank/Create.vue`

```vue
<template>
  <div class="template-container package-container clearfix">
    <XHeader :title="$t('banks.header.create')">
      <router-link
        :to="{name: 'banks.index'}"
        class="btn btn-secondary btn-common mr-2"
      >
        <span class="feather feather-arrow-left" />
        <span class="text">{{ $t("common.button.cancel") }}</span>
      </router-link>
      <button
        class="btn btn-success text-white btn-common"
        @click="handleCreate"
      >
        <span class="feather feather-save" />
        <span class="text">{{ $t("common.button.save") }}</span>
      </button>
    </XHeader>
    <div class="content-box pt-4 pb-4 pr-4 pl-4 clearfix">
      <div class="card p-4">
        <div class="row">
          <div class="col-12 col-xl-12">
            <BankForm
              ref="bankForm"
              :validations="validations"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
```

```vue
<script>
import { mapActions } from "vuex";
import BankForm from "./Form.vue";
import { mapMutations } from "vuex";

export default {
  name: "BankCreate",
  components: {
    BankForm,
  },
  data() {
    return {
      validations: {},
    };
  },
  methods: {
    ...mapActions("bank", ["createBranch"]),
    ...mapMutations("bank", ["setBank"]),
    handleCreate() {
      this.validations = {};
      let formData = this.$refs.bankForm.getFormData();

      this.createBranch(formData)
        .then(() => {
          this.onResponseSuccess(
            this.$t("banks.header.create"),
            this.$t("banks.notification.description_create"),
          );
          this.setBank();
          this.$router.back();
        })
        .catch((error) => {
          this.validations = this.extractValidations(error);
          this.onResponseError(error);
        });
    }
  }
};
</script>
```

## Edit.vue (Edit View)

**Example**: `young_sia_admin/resources/js/views/bank/Edit.vue`

```vue
<template>
  <div class="template-container package-container clearfix">
    <XHeader :title="$t('banks.header.update')">
      <router-link
        :to="{name: 'banks.index'}"
        class="btn btn-secondary btn-common mr-2"
      >
        <span class="feather feather-arrow-left" />
        <span class="text">{{ $t("common.button.cancel") }}</span>
      </router-link>
      <button
        class="btn btn-success text-white btn-common"
        @click="handleUpdate"
      >
        <span class="feather feather-save" />
        <span class="text">{{ $t("common.button.update") }}</span>
      </button>
    </XHeader>
    <div class="content-box pt-4 pb-4 pr-4 pl-4 clearfix">
      <div class="card p-4">
        <div class="row">
          <div class="col-12 col-xl-12">
            <BankForm
              ref="bankForm"
              :validations="validations"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
```

```vue
<script>
import { mapActions } from "vuex";
import BankForm from "./Form.vue";
import { mapMutations } from "vuex";

export default {
  name: "BankEdit",
  components: {
    BankForm,
  },
  data() {
    return {
      validations: {},
    };
  },
  created() {
    this.getBank(this.$route.params.id);
  },
  methods: {
    ...mapActions("bank", ["updateBranch", "getBank"]),
    ...mapMutations("bank", ["setBank"]),
    handleUpdate() {
      this.validations = {};
      let formData = this.$refs.bankForm.getFormData();

      this.updateBranch(formData)
        .then(() => {
          this.onResponseSuccess(
            this.$t("banks.header.update"),
            this.$t("banks.notification.description_update"),
          );
          this.setBank();
          this.$router.back();
        })
        .catch((error) => {
          this.validations = this.extractValidations(error);
          this.onResponseError(error);
        });
    }
  }
};
</script>
```

## Form.vue (Reusable Form Component)

**Example**: `young_sia_admin/resources/js/views/country/Form.vue`

```vue
<template>
  <form @submit.prevent>
    <div class="row form-group">
      <label
        class="label required col-form-label col-4"
        for="name"
      >{{
        $t("common.navigation.name")
      }}</label>
      <div class="col-8 gap-3 d-flex flex-column">
        <div class="input-group">
          <div class="input-group-prepend">
            <span class="flag input-group-text"><flag
              class="size flag-icon"
              iso="gb"
            /></span>
          </div>
          <input
            id="name"
            ref="name"
            v-model="country.name"
            type="text"
            name="name"
            class="form-control"
            aria-describedby="name"
            :placeholder="$t('common.navigation.name')"
            :class="{
              'is-invalid': validations && validations.name,
            }"
          >
          <template v-if="validations && validations.name">
            <div class="invalid-feedback">
              {{ validations.name[0] }}
            </div>
          </template>
        </div>
        <div class="input-group">
          <div class="input-group-prepend">
            <span class="flag input-group-text"><flag
              class="size flag-icon"
              iso="kh"
            /></span>
          </div>

          <input
            id="name-km"
            v-model="country.name_km"
            :placeholder="$t('common.navigation.name')"
            class="form-control"
          >
        </div>
        <div class="input-group">
          <div class="input-group-prepend">
            <span class="flag input-group-text"><flag
              class="size flag-icon"
              iso="cn"
            /></span>
          </div>

          <input
            id="name-zh"
            v-model="country.name_zh"
            :placeholder="$t('common.navigation.name')"
            class="form-control"
          >
        </div>
      </div>
    </div>
```

```vue
<script>
export default {
  name: "CountryForm",
  components: {
    UploadImage,
    AgencyPurchase,
  },
  props: {
    validations: {
      required: true,
      type: Object,
    }
  },
  data() {
    return {
      isUpdate: true,
    };
  },
  computed: {
    ...mapFields("country", ["country"]),
    isPurchaseServiceShippingTypeInvalid() {
      if (
        this.validations && 
          (this.validations["purchase_service_shipping_type_ids"] || this.validations["purchase_service_shipping_type_ids.0"])
      ) {
        return true;
      }
      return false;
    }
  },
  destroyed() {
    this.setCountry();
  },
  beforeUpdate() {
    if (this.$route.params && this.$route.params.id && this.isUpdate) {
      if (this.country.background_image)
        this.$refs.bgImage.addImage(this.country.background_image);
      if (this.country.flag_icon)
        this.$refs.flagIcon.addImage(this.country.flag_icon);
      this.isUpdate = false;
    }
  },
  methods: {
    ...mapMutations("country", ["setCountry"]),
    getFormData() {
      let formData = new FormData();

      this.country.name_locale = null;
      if (this.country.name_km || this.country.name_zh) {
        this.country.name_locale = JSON.stringify({
          "km": this.country.name_km,
          "zh": this.country.name_zh,
        });
      }

      if (this.country.agency_purchase_label_km || this.country.agency_purchase_label_zh) {
        this.country.agency_purchase_label_locale = JSON.stringify({
          "km": this.country.agency_purchase_label_km || this.country.agency_purchase_label,
          "zh": this.country.agency_purchase_label_zh || this.country.agency_purchase_label,
        });
      }

      let filterNullFormData = this.objectFilterNull(this.country);
      for (var key in filterNullFormData) {
        if (key === "purchase_service_shipping_types") {
          if (this.country.purchase_service_shipping_types && this.country.purchase_service_shipping_types.length > 0) {
            this.country.purchase_service_shipping_types.forEach((shippingType, index) => {
              formData.append(`purchase_service_shipping_type_ids[${index}]`, shippingType.id);
            });
          }
        } else if (key == "default_shipping_type") {
          if (this.country.default_shipping_type && this.country.default_shipping_type.id) {
            formData.append("default_shipping_type_id", this.country.default_shipping_type.id);
          }
        } else if (["shipping_types"].includes(key)) {
          continue;
        } else {
          formData.append(key, filterNullFormData[key]);
        }
      }

      return formData;
    },
    updateImage(image) {
      this.country.background_image = image;
    },
    updateFlagIcon(image) {
      this.country.flag_icon = image;
    }
  }
};
</script>
```

## Show.vue (Detail View)

**Example**: `young_sia_admin/resources/js/views/merchants/Show.vue`

```vue
<template>
  <div class="template-container merchant-container-detail clearfix">
    <div class="action-box pt-5 pb-4 pr-4 pl-4 clearfix">
      <div class="d-flex justify-content-between">
        <div class="blg-summary">
          <span class="text font-weight-bold">
            {{ $t("merchant.header.merchant_detail") }}
          </span>
        </div>
        <div class="blg-action">
          <button
            type="button"
            class="btn btn-secondary btn-common"
            @click="$router.push({ name: 'merchants.list' })"
          >
            <span class="feather feather-arrow-left" />
            <span class="text">
              {{ $t("common.button.back") }}
            </span>
          </button>
        </div>
      </div>
    </div>

    <div
      v-if="shop"
      class="content-box pt-4 pb-4 pr-4 pl-4 clearfix"
    >
      <div class="row">
        <div class="col-12 mb-4">
          <div class="row">
            <MerchantOwnerDetail
              v-if="shop.owner"
              :owner="shop.owner"
            />
            <MerchantInfoDetail :shop="shop" />
            <MerchantConfigurationDetail :shop="shop" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
```

```vue
<script>
import { mapActions } from "vuex";
import MerchantOwnerDetail from "./components/MerchantOwnerDetail.vue";
import MerchantInfoDetail from "./components/MerchantInfoDetail.vue";
import MerchantConfigurationDetail from "./components/MerchantConfigurationDetail.vue";

export default {
  name: "MerchantDetail",
  components: {
    MerchantConfigurationDetail,
    MerchantOwnerDetail,
    MerchantInfoDetail,
  },
  data() {
    return {
      shop: {},
      owner: {},
    };
  },
  mounted() {
    this.getMerchantDetail();
  },
  methods: {
    ...mapActions("merchants", ["showMerchant"]),
    getMerchantDetail() {
      this.shop = null;
      this.showMerchant({ merchant_hash_id: this.$route.params.id })
        .then(({ data }) => {
          if (data.data) {
            this.shop = data.data;
            if (this.shop.package_dimensions) {
              this.shop.package_dimensions = JSON.parse(this.shop.package_dimensions);
            }
            this.owner = this.shop.owner;
            if (this.shop.origin_delivery_info) {
              let preOrderDuration = {
                min_days: 10,
                max_days: 15
              };
              if (this.shop.pre_order_duration) {
                preOrderDuration = {
                  min_days: this.shop.pre_order_duration.min_days || "",
                  max_days: this.shop.pre_order_duration.max_days || ""
                };
              }
              const preOrderDurationReplace = `${preOrderDuration.min_days} - ${preOrderDuration.max_days}`;
              let shippingDuration = {
                min_days: 7,
                max_days: 10
              };
              if (this.shop.shipping_duration) {
                shippingDuration = {
                  min_days: this.shop.shipping_duration.min_days || "",
                  max_days: this.shop.shipping_duration.max_days || ""
                };
              }
              const shippingDurationReplace = `${shippingDuration.min_days} - ${shippingDuration.max_days}`;
              let estimatedArrivalReplace = `${parseInt(preOrderDuration.min_days) + parseInt(shippingDuration.min_days)} - ${parseInt(preOrderDuration.max_days) + parseInt(shippingDuration.max_days)}`;
              this.shop.origin_delivery_info = this.replaceOriginDeliveryInfoTemplate(
                this.shop.origin_delivery_info,
                preOrderDurationReplace,
                shippingDurationReplace,
                estimatedArrivalReplace
              );

              if (this.shop.origin_delivery_info_locale) {
                let originDeliveryInfoInKhmer = this.shop.origin_delivery_info_locale.km;
                if (originDeliveryInfoInKhmer) {
                  originDeliveryInfoInKhmer = this.replaceOriginDeliveryInfoTemplate(
                    originDeliveryInfoInKhmer,
                    preOrderDurationReplace,
                    shippingDurationReplace,
                    estimatedArrivalReplace
                  );
                }

                let originDeliveryInfoInChina = this.shop.origin_delivery_info_locale.zh;
                if (originDeliveryInfoInChina) {
                  originDeliveryInfoInChina = this.replaceOriginDeliveryInfoTemplate(
                    originDeliveryInfoInChina,
                    preOrderDurationReplace,
                    shippingDurationReplace,
                    estimatedArrivalReplace
                  );
                }

                this.shop.origin_delivery_info_locale = {
                  km: originDeliveryInfoInKhmer ? originDeliveryInfoInKhmer : this.shop.origin_delivery_info,
                  zh: originDeliveryInfoInChina ? originDeliveryInfoInChina : this.shop.origin_delivery_info,
                };
              }
            }
          }
        })
        .catch(error => {
          this.onResponseError(error);
        });
    }
  },
};
</script>
```

## Common Building Blocks

| UI / utility | Example usage | Where it shows up |
| --- | --- | --- |
| `XHeader` | Section header with actions | `bank/Index.vue`, `bank/Create.vue`, `bank/Edit.vue` |
| `XThumb` + `XBoolean` | Table cells for images/booleans | `bank/Index.vue` |
| `UploadImage` | Image upload in forms | `country/Form.vue` |
| `mapFields` + `mapActions` | Vuex state and actions | `bank/Index.vue`, `country/Form.vue`, `merchants/Show.vue` |
| `multiselect` | Search/filter select inputs | `top_icon/components/Form.vue`, `popular_searched_keyword/components/Form.vue` |
