# Vue Component Templates

**Location**: `young_sia_admin/resources/js/views/{Resource}/`

## Index.vue (List View)

```vue
<template>
  <div class="resource-index">
    <b-card>
      <b-card-header>
        <h3>{{ $t('resource.title') }}</h3>
        <b-button
          variant="primary"
          :to="{ name: 'resource.create' }"
          v-if="canCreate"
        >
          <i class="fa fa-plus"></i> {{ $t('resource.create') }}
        </b-button>
      </b-card-header>

      <b-card-body>
        <!-- Filters Component -->
        <resource-filters @filter="handleFilter" />

        <!-- Data Table -->
        <b-table
          :items="items"
          :fields="fields"
          :busy="loading"
          striped
          hover
          responsive
        >
          <template #cell(actions)="data">
            <b-button-group size="sm">
              <b-button
                variant="info"
                :to="{ name: 'resource.show', params: { id: data.item.id } }"
              >
                <i class="fa fa-eye"></i>
              </b-button>
              <b-button
                variant="warning"
                :to="{ name: 'resource.edit', params: { id: data.item.id } }"
                v-if="canEdit"
              >
                <i class="fa fa-edit"></i>
              </b-button>
              <b-button
                variant="danger"
                @click="handleDelete(data.item.id)"
                v-if="canDelete"
              >
                <i class="fa fa-trash"></i>
              </b-button>
            </b-button-group>
          </template>
        </b-table>

        <!-- Pagination -->
        <b-pagination
          v-model="currentPage"
          :total-rows="totalRows"
          :per-page="perPage"
          @change="handlePageChange"
        />
      </b-card-body>
    </b-card>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import ResourceFilters from './components/ResourceFilters.vue'

export default {
  name: 'ResourceIndex',
  components: {
    ResourceFilters
  },
  data() {
    return {
      fields: [
        { key: 'id', label: 'ID', sortable: true },
        { key: 'field1', label: this.$t('resource.fields.field1'), sortable: true },
        { key: 'field2', label: this.$t('resource.fields.field2'), sortable: true },
        { key: 'actions', label: this.$t('common.actions') }
      ],
      currentPage: 1,
      perPage: 15
    }
  },
  computed: {
    ...mapGetters('resource', ['items', 'loading', 'pagination']),
    totalRows() {
      return this.pagination.total
    },
    canCreate() {
      return this.$store.getters['auth/hasPermission']('resource.create')
    },
    canEdit() {
      return this.$store.getters['auth/hasPermission']('resource.edit')
    },
    canDelete() {
      return this.$store.getters['auth/hasPermission']('resource.delete')
    }
  },
  methods: {
    ...mapActions('resource', ['fetchItems', 'deleteItem']),
    async loadData() {
      try {
        await this.fetchItems({
          page: this.currentPage,
          per_page: this.perPage
        })
      } catch (error) {
        this.$bvToast.toast(error.message, {
          title: 'Error',
          variant: 'danger'
        })
      }
    },
    handlePageChange(page) {
      this.currentPage = page
      this.loadData()
    },
    handleFilter(filters) {
      this.currentPage = 1
      this.loadData()
    },
    async handleDelete(id) {
      const confirmed = await this.$bvModal.msgBoxConfirm(
        this.$t('resource.messages.deleteConfirm'),
        {
          title: this.$t('common.confirm'),
          okVariant: 'danger',
          okTitle: this.$t('common.delete'),
          cancelTitle: this.$t('common.cancel')
        }
      )
      
      if (confirmed) {
        try {
          await this.deleteItem(id)
          this.$bvToast.toast(this.$t('resource.messages.deleteSuccess'), {
            title: 'Success',
            variant: 'success'
          })
          this.loadData()
        } catch (error) {
          this.$bvToast.toast(error.message, {
            title: 'Error',
            variant: 'danger'
          })
        }
      }
    }
  },
  mounted() {
    this.loadData()
  }
}
</script>
```

## Create.vue (Create Form)

```vue
<template>
  <div class="resource-create">
    <b-card>
      <b-card-header>
        <h3>{{ $t('resource.create') }}</h3>
      </b-card-header>

      <b-card-body>
        <resource-form
          :initial-data="{}"
          @submit="handleSubmit"
          @cancel="handleCancel"
        />
      </b-card-body>
    </b-card>
  </div>
</template>

<script>
import { mapActions } from 'vuex'
import ResourceForm from './Form.vue'

export default {
  name: 'ResourceCreate',
  components: {
    ResourceForm
  },
  methods: {
    ...mapActions('resource', ['createItem']),
    async handleSubmit(data) {
      try {
        await this.createItem(data)
        this.$bvToast.toast(this.$t('resource.messages.createSuccess'), {
          title: 'Success',
          variant: 'success'
        })
        this.$router.push({ name: 'resource.index' })
      } catch (error) {
        this.$bvToast.toast(error.message, {
          title: 'Error',
          variant: 'danger'
        })
      }
    },
    handleCancel() {
      this.$router.push({ name: 'resource.index' })
    }
  }
}
</script>
```

## Edit.vue (Edit Form)

```vue
<template>
  <div class="resource-edit">
    <b-card>
      <b-card-header>
        <h3>{{ $t('resource.edit') }}</h3>
      </b-card-header>

      <b-card-body>
        <resource-form
          v-if="currentItem"
          :initial-data="currentItem"
          :is-edit="true"
          @submit="handleSubmit"
          @cancel="handleCancel"
        />
        <b-spinner v-else variant="primary" />
      </b-card-body>
    </b-card>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import ResourceForm from './Form.vue'

export default {
  name: 'ResourceEdit',
  components: {
    ResourceForm
  },
  computed: {
    ...mapGetters('resource', ['currentItem'])
  },
  methods: {
    ...mapActions('resource', ['fetchItem', 'updateItem']),
    async handleSubmit(data) {
      try {
        await this.updateItem({
          id: this.$route.params.id,
          data
        })
        this.$bvToast.toast(this.$t('resource.messages.updateSuccess'), {
          title: 'Success',
          variant: 'success'
        })
        this.$router.push({ name: 'resource.index' })
      } catch (error) {
        this.$bvToast.toast(error.message, {
          title: 'Error',
          variant: 'danger'
        })
      }
    },
    handleCancel() {
      this.$router.push({ name: 'resource.index' })
    }
  },
  async mounted() {
    try {
      await this.fetchItem(this.$route.params.id)
    } catch (error) {
      this.$bvToast.toast(error.message, {
        title: 'Error',
        variant: 'danger'
      })
      this.$router.push({ name: 'resource.index' })
    }
  }
}
</script>
```

## Form.vue (Reusable Form Component)

```vue
<template>
  <b-form @submit.prevent="handleSubmit">
    <b-form-group
      :label="$t('resource.fields.field1')"
      label-for="field1"
    >
      <b-form-input
        id="field1"
        v-model="form.field1"
        :state="validateState('field1')"
        required
      />
      <b-form-invalid-feedback>
        {{ errors.field1 }}
      </b-form-invalid-feedback>
    </b-form-group>

    <b-form-group
      :label="$t('resource.fields.field2')"
      label-for="field2"
    >
      <b-form-input
        id="field2"
        v-model="form.field2"
        :state="validateState('field2')"
        type="number"
      />
      <b-form-invalid-feedback>
        {{ errors.field2 }}
      </b-form-invalid-feedback>
    </b-form-group>

    <!-- Add more form fields as needed -->

    <b-button-group>
      <b-button type="submit" variant="primary">
        {{ $t('resource.actions.save') }}
      </b-button>
      <b-button type="button" variant="secondary" @click="$emit('cancel')">
        {{ $t('resource.actions.cancel') }}
      </b-button>
    </b-button-group>
  </b-form>
</template>

<script>
export default {
  name: 'ResourceForm',
  props: {
    initialData: {
      type: Object,
      required: true
    },
    isEdit: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      form: {
        field1: '',
        field2: null,
        ...this.initialData
      },
      errors: {}
    }
  },
  methods: {
    handleSubmit() {
      if (this.validate()) {
        this.$emit('submit', this.form)
      }
    },
    validate() {
      this.errors = {}
      
      if (!this.form.field1) {
        this.errors.field1 = this.$t('resource.validation.field1_required')
      }
      
      // Add more validation as needed
      
      return Object.keys(this.errors).length === 0
    },
    validateState(field) {
      return this.errors[field] ? false : null
    }
  },
  watch: {
    initialData: {
      handler(newVal) {
        this.form = { ...this.form, ...newVal }
      },
      deep: true
    }
  }
}
</script>
```

## Show.vue (Detail View)

```vue
<template>
  <div class="resource-show">
    <b-card>
      <b-card-header>
        <h3>{{ $t('resource.view') }}</h3>
        <b-button-group>
          <b-button
            variant="warning"
            :to="{ name: 'resource.edit', params: { id: $route.params.id } }"
            v-if="canEdit"
          >
            <i class="fa fa-edit"></i> {{ $t('resource.actions.edit') }}
          </b-button>
          <b-button variant="secondary" :to="{ name: 'resource.index' }">
            <i class="fa fa-arrow-left"></i> {{ $t('common.back') }}
          </b-button>
        </b-button-group>
      </b-card-header>

      <b-card-body>
        <b-spinner v-if="loading" variant="primary" />
        <div v-else-if="currentItem">
          <b-row>
            <b-col md="6">
              <dl class="row">
                <dt class="col-sm-4">{{ $t('resource.fields.field1') }}</dt>
                <dd class="col-sm-8">{{ currentItem.field1 }}</dd>
                
                <dt class="col-sm-4">{{ $t('resource.fields.field2') }}</dt>
                <dd class="col-sm-8">{{ currentItem.field2 }}</dd>
                
                <!-- Add more fields as needed -->
              </dl>
            </b-col>
          </b-row>
        </div>
      </b-card-body>
    </b-card>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'

export default {
  name: 'ResourceShow',
  computed: {
    ...mapGetters('resource', ['currentItem', 'loading']),
    canEdit() {
      return this.$store.getters['auth/hasPermission']('resource.edit')
    }
  },
  methods: {
    ...mapActions('resource', ['fetchItem'])
  },
  async mounted() {
    try {
      await this.fetchItem(this.$route.params.id)
    } catch (error) {
      this.$bvToast.toast(error.message, {
        title: 'Error',
        variant: 'danger'
      })
      this.$router.push({ name: 'resource.index' })
    }
  }
}
</script>
```

## components/ Subfolder

Create reusable components in `young_sia_admin/resources/js/views/{Resource}/components/`:
- **ResourceFilters.vue**: Filter component for the list view
- **ResourceModal.vue**: Modal dialogs for quick actions
- **ResourceCard.vue**: Card component for displaying resource details
- Any other feature-specific reusable components
