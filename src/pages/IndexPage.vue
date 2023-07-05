<template>
  <q-page class="q-pa-md container">
    <div class="q-pa-md">
      <div
        class="fit row wrap justify-between items-center content-center container q-pb-md"
      >
        <div class="text-h5 text-weight-bold">Prospectos</div>
        <div>
          <q-btn
            color="primary"
            icon="add"
            unelevated
            no-caps
            label="Agregar prospecto"
            @click="AddProspect"
          />
        </div>
      </div>
      <q-table
        flat
        bordered
        ref="tableReq"
        :rows="prospects"
        :columns="columns"
        :loading="loading"
        row-key="id"
        color="primary"
        v-model:pagination="pagination"
        @request="getProspects"
        @row-click="toProspect"
        :rows-per-page-options="[25, 50, 100]"
      >
        <template v-slot:body-cell-status="props">
          <q-td>
            <q-chip
              square
              v-if="props.row.status === 'Enviado'"
              color="primary"
              text-color="white"
              >Enviado</q-chip
            >
            <q-chip
              square
              v-if="props.row.status === 'Autorizado'"
              color="green"
              text-color="white"
              >Autorizado</q-chip
            >
            <q-chip
              square
              v-if="props.row.status === 'Rechazado'"
              color="red"
              text-color="white"
              >Rechazado</q-chip
            >
          </q-td>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";
import { api } from "boot/axios";

export default {
  data() {
    return {
      prospects: [],
      columns: [
        {
          name: "id",
          align: "left",
          label: "Prospecto #",
          field: (it) => it.id,
          sortable: true,
        },
        {
          name: "firstName",
          align: "left",
          label: "Nombre #",
          field: (it) => `${it.firstName} ${it.lastName}`,
          sortable: true,
        },
        {
          name: "status",
          align: "left",
          label: "Status",
          field: (it) => it.status,
          sortable: true,
        },
        {
          name: "zipcode",
          align: "left",
          label: "Código postal",
          field: (it) => it.zipcode,
          sortable: true,
        },
        {
          name: "phone",
          align: "left",
          label: "Teléfono",
          field: (it) => it.phone,
          sortable: false,
        },
        {
          name: "rfc",
          align: "left",
          label: "RFC",
          field: (it) => it.rfc,
          sortable: true,
        },
      ],
      loading: false,
      pagination: {
        page: 1,
        rowsPerPage: 25,
        rowsNumber: 10,
        sortBy: "id",
        descending: true,
      },
    };
  },
  mounted() {
    this.$refs.tableReq.requestServerInteraction();
  },
  methods: {
    getProspects(data) {
      this.pagination = data.pagination;

      this.loading = true;

      const pagination = {
        limit: this.pagination.rowsPerPage,
        offset: (this.pagination.page - 1) * this.pagination.rowsPerPage,
        sortBy: this.pagination.sortBy,
        sortType: this.pagination.descending ? "DESC" : "ASC",
      };

      const params = {
        ...pagination,
      };

      api
        .get("/prospects", { params })
        .then((response) => {
          this.prospects = response.data.data;
          this.pagination.rowsNumber = response.data.total;
        })
        .finally(() => {
          this.loading = false;
        });
    },
    AddProspect() {
      this.$router.push(`/nuevo-prospecto`);
    },
    toProspect(_, row) {
      this.$router.push(`/editar-prospecto/${row.id}`);
    },
  },
};
</script>
