<template>
  <q-page>
    <div class="form container-form q-pb-md">
      <q-form ref="formOrder">
        <div class="text-h5 text-weight-bold q-py-md">Nuevo prospecto</div>
        <div class="row">
          <div class="q-py-sm col">
            <q-input
              outlined
              label="Nombre"
              class="q-mr-md"
              v-model="prospect.firstName"
            />
          </div>
          <div class="q-py-sm col">
            <q-input outlined label="Apellido" v-model="prospect.lastName" />
          </div>
        </div>

        <div class="q-py-lg">
          <div>
            <span class="text-subtitle1 text-grey-7">Domicilio</span>
          </div>
          <div class="row">
            <div class="q-py-sm col-8">
              <q-input
                outlined
                label="Calle"
                class="q-mr-md"
                v-model="prospect.street"
              />
            </div>
            <div class="q-py-sm col-4">
              <q-input outlined label="Número" v-model="prospect.number" />
            </div>
            <div class="q-py-sm col-8">
              <q-input
                outlined
                label="Colonia"
                class="q-mr-md"
                v-model="prospect.neighborhood"
              />
            </div>
            <div class="q-py-sm col-6">
              <q-input
                outlined
                label="Ciudad"
                class="q-mr-md"
                v-model="prospect.city"
              />
            </div>
            <div class="q-py-sm col-6">
              <q-input
                outlined
                label="Estado"
                class="q-mr-md"
                v-model="prospect.state"
              />
            </div>
            <div class="q-py-sm col-4">
              <q-input
                outlined
                label="Código postal"
                v-model="prospect.zipCode"
              />
            </div>
          </div>
        </div>

        <div class="q-pb-lg">
          <div class="q-py-sm">
            <q-input outlined label="Teléfono" v-model="prospect.phone" />
          </div>
          <div class="q-py-sm">
            <q-input outlined label="RFC" v-model="prospect.rfc" />
          </div>
        </div>
        <div align="right">
          <q-btn
            flat
            unelevated
            label="Cancelar"
            class="bg-grey-3"
            v-close-popup
            no-caps
            @click="cancel"
          />
          <q-btn
            color="primary"
            unelevated
            label="Agregar prospecto"
            class="q-ml-sm"
            no-caps
            @click="addProspect"
          />
        </div>
      </q-form>
    </div>
  </q-page>
</template>

<script>
import { api } from "boot/axios";

export default {
  data() {
    return {
      prospect: {
        firstName: null,
        lastName: null,
        street: null,
        number: null,
        neighborhood: null,
        city: null,
        state: null,
        zipCode: null,
        phone: null,
        rfc: null,
      },
      loading: true,
    };
  },
  mounted() {},
  methods: {
    addProspect() {
      this.loading = true;

      const data = {
        firstName: this.prospect.firstName,
        lastName: this.prospect.lastName,
        street: this.prospect.street,
        number: this.prospect.number,
        neighborhood: this.prospect.neighborhood,
        city: this.prospect.city,
        state: this.prospect.state,
        zipcode: this.prospect.zipCode,
        phone: this.prospect.phone,
        rfc: this.prospect.rfc,
        status: "Enviado",
      };

      api
        .post("/prospects", data)
        .then(() => {
          this.$router.push(`/`);
        })
        .finally(() => {
          this.dialogLoading = false;
        });
    },
    cancel() {
      this.$router.push(`/`);
    },
  },
};
</script>
