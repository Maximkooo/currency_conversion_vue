<template>
  <b-table :fields="fields" :items="items">
    <template v-slot:cell(from_currency)="{ item }">
      <span class="text-truncate">{{ item.from_currency.name }}</span>
    </template>
    <template v-slot:cell(to_currency)="{ item }">
      <span class="text-truncate">{{ item.to_currency.name }}</span>
    </template>
    <template v-slot:cell(delete)="{ item }">
      <b-button size="sm" variant="danger" @click="removeItem(item)"
        >Delete
      </b-button>
    </template>
  </b-table>
</template>
<script>
export default {
  props: {
    items: {
      type: Array,
      required: true,
      default: () => [],
    },
  },
  data() {
    return {
      fields: [
        {
          key: "amount",
          label: "Amount",
          class: "align-middle",
        },
        {
          key: "from_currency",
          label: "From currency",
          class: "align-middle",
        },
        {
          key: "to_currency",
          label: "To currency",
          class: "align-middle",
        },
        {
          key: "actions",
          label: "Actions",
          class: "align-middle",
        },
        {
          key: "delete",
          label: "Effect",
        },
      ],
    };
  },
  methods: {
    removeItem(item) {
      this.$emit("deleteItem", item);
    },
  },
};
</script>
