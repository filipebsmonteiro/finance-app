<script>
import Portfolio from "src/repositories/Finance/Portfolio.js";

export default {
  props: {
    stock: Object,
  },
  data() {
    return {
      columns: [
        {
          name: "date",
          label: this.$q.screen.gt.sm ? "Data da Compra" : "Data",
          field: "date",
          align: "left",
          format: this.$formaters.dateExtended,
          sortable: true,
        },
        {
          name: "value",
          label: this.$q.screen.gt.sm ? "Valor da Compra" : "Valor",
          field: "value",
          format: this.$formaters.money,
          sortable: true,
        },
        {
          name: "quantity",
          label: this.$q.screen.gt.sm ? "Quantidade" : "Qtd",
          field: "quantity",
          sortable: true,
        },
        {
          name: "result",
          label: "Resultado",
          field: "gain",
          // field: (row) => row.value - this.quote,
          format: this.$formaters.money,
          sortable: true,
        },
        {
          name: "delete",
          label: "",
          field: "delete",
          sortable: true,
        },
      ],
    };
  },
  methods: {
    remove(id) {
      Portfolio.delete(id);
    },
  },
};
</script>

<template>
  <q-table
    :rows="stock.contributions"
    :columns="columns"
    :rows-per-page-options="[0]"
    hide-pagination
    flat
    :dense="$q.screen.lt.md"
  >
    <template v-slot:body-cell-result="props">
      <q-td :props="props">
        <span
          :class="{
            'text-caption': true,
            'text-positive': props.row.result > 0,
            'text-negative': props.row.result < 0,
            'text-grey': props.row.result === 0,
          }"
        >
          {{ $formaters.money(props.row.result) }}
        </span>
      </q-td>
    </template>
    <template v-slot:body-cell-delete="props">
      <q-td :props="props">
        <q-icon
          name="la la-trash"
          size="sm"
          color="grey"
          class="cursor-pointer"
          @click="remove(props.row.id)"
        />
      </q-td>
    </template>
  </q-table>
  <!-- <div class="row justify-center q-mt-md">
    <q-pagination
      v-model="pagination.page"
      color="grey-8"
      :max="pagesNumber"
      size="sm"
    />
  </div> -->
</template>
