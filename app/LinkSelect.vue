<template>
  <k-grid>
    <k-column v-if="showSelect" width="1/4">
      <k-select-field
        type="select"
        v-model="data.type"
        :options="types"
        :empty="false"
        @input="inputType"
      />
    </k-column>

    <k-column :width="showSelect ? '3/4' : null">
      <k-url-field v-if="data.type === 'url'" v-model="data.value" placeholder="https://example.com/" />

      <k-pages-field
        v-else-if="data.type === 'page'"
        v-model="data.value"
        :endpoints="{
          field: this.endpoints.field + '/pages'
        }"
      ></k-pages-field>

      <k-files-field
        v-else-if="data.type === 'file'"
        v-model="data.value"
        :endpoints="{
          field: this.endpoints.field + '/files'
        }"
      ></k-files-field>

      <k-email-field v-else-if="data.type === 'email'" v-model="data.value" />

      <k-tel-field v-else-if="data.type === 'tel'" v-model="data.value" />

      <k-box
        v-else
        theme="negative"
        :text="$t('error.type', { type: data.type })"
      />
    </k-column>
  </k-grid>
</template>

<script>
export default {
  props: {
    value: Object,
    options: Array,
    endpoints: Object
  },
  data: function () {
    return {
      data: this.value,
      updating: false
    }
  },
  computed: {
    showSelect: function () {
      return this.types.length > 1
    },
    types: function () {
      return this.options.map(function (type) {
        return {
          value: type,
          text: this.$t(type)
        }
      }.bind(this))
    }
  },
  methods: {
    inputType: function () {
      this.data.value = undefined
    }
  },
  watch: {
    data: {
      deep: true,
      handler: function (value) {
        if (!this.updating) {
          this.$emit('input', value)
        }
      }
    },
    value: function (value) {
      this.updating = true

      Object.assign(this.data, value)

      this.$nextTick(function () {
        this.updating = false
      }.bind(this))
    }
  }
}
</script>

<style lang="scss" scoped>
/deep/ .k-field-header {
  display: none; // hides the Select buttons
}
</style>