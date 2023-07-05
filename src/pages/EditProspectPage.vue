<template>
  <q-page>
    <div class="form container-form q-pb-md">
      <q-dialog v-model="notesDialog">
        <q-card style="width: 400px; max-width: 80vw">
          <q-card-section class="row items-center q-pb-none">
            <q-btn icon="close" flat round dense v-close-popup />
          </q-card-section>

          <q-card-section>
            <p class="text-subtitle1 q-mb-none text-grey">Motivo de rechazo</p>
            <q-input outlined type="textarea" v-model="notes" />
          </q-card-section>

          <q-card-section align="right">
            <q-btn
              flat
              unelevated
              label="Cancelar"
              class="bg-grey-3"
              v-close-popup
              no-caps
            />
            <q-btn
              color="red"
              unelevated
              label="Rechazar"
              class="q-ml-sm"
              @click="processProspect('Rechazado')"
              no-caps
              :loading="dialogLoading"
            />
          </q-card-section>
        </q-card>
      </q-dialog>

      <div class="q-pt-lg">
        <q-btn
          round
          icon="arrow_back"
          unelevated
          class="q-mr-md"
          @click="processProspect('Autorizado')"
        >
        </q-btn>
      </div>

      <q-form ref="formOrder">
        <div class="text-h5 text-weight-bold q-py-md">
          Prospecto
          <q-chip
            v-if="prospect.status === 'Enviado'"
            icon="arrow_forward"
            color="primary"
            text-color="white"
            >Enviado</q-chip
          >
          <q-chip
            v-if="prospect.status === 'Autorizado'"
            icon="done"
            color="green"
            text-color="white"
            >Autorizado</q-chip
          >
          <q-chip
            v-if="prospect.status === 'Rechazado'"
            icon="close"
            color="red"
            text-color="white"
            >Rechazado</q-chip
          >
        </div>
        <div v-if="prospect.status === 'Rechazado'">
          <p>{{ prospect.notes }}</p>
        </div>

        <div
          v-if="!isRejected"
          class="bg-grey-4 q-pa-md"
          style="border-radius: 8px"
        >
          <q-btn
            round
            icon="done"
            color="green"
            unelevated
            class="q-mr-md"
            @click="processProspect('Autorizado')"
          >
            <q-tooltip> Autorizar prospecto </q-tooltip>
          </q-btn>

          <q-btn
            round
            icon="close"
            color="red"
            unelevated
            @click="openReject()"
          >
            <q-tooltip> Rechazar prospecto </q-tooltip>
          </q-btn>
        </div>
        <div class="row">
          <div class="q-py-sm col">
            <q-input
              outlined
              label="Nombre"
              class="q-mr-md"
              v-model="prospect.firstName"
              :disable="isRejected"
            />
          </div>
          <div class="q-py-sm col">
            <q-input
              outlined
              label="Apellido"
              v-model="prospect.lastName"
              :disable="isRejected"
            />
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
                :disable="isRejected"
              />
            </div>
            <div class="q-py-sm col-4">
              <q-input
                outlined
                label="Número"
                v-model="prospect.number"
                :disable="isRejected"
              />
            </div>
            <div class="q-py-sm col-8">
              <q-input
                outlined
                label="Colonia"
                class="q-mr-md"
                v-model="prospect.neighborhood"
                :disable="isRejected"
              />
            </div>
            <div class="q-py-sm col-6">
              <q-input
                outlined
                label="Ciudad"
                class="q-mr-md"
                v-model="prospect.city"
                :disable="isRejected"
              />
            </div>
            <div class="q-py-sm col-6">
              <q-input
                outlined
                label="Estado"
                class="q-mr-md"
                v-model="prospect.state"
                :disable="isRejected"
              />
            </div>
            <div class="q-py-sm col-4">
              <q-input
                outlined
                label="Código postal"
                v-model="prospect.zipcode"
                :disable="isRejected"
              />
            </div>
          </div>
        </div>

        <div class="q-pb-lg">
          <div class="q-py-sm">
            <q-input
              outlined
              label="Teléfono"
              v-model="prospect.phone"
              :disable="isRejected"
            />
          </div>
          <div class="q-py-sm">
            <q-input
              outlined
              label="RFC"
              v-model="prospect.rfc"
              :disable="isRejected"
            />
          </div>
        </div>

        <div class="q-pb-md">
          <div class="q-py-sm" v-if="!docRef">
            <input
              style="display: none"
              type="file"
              @change="onFileSelected"
              ref="fileInput"
              :disable="isRejected"
            />
            <q-btn
              v-if="!this.selectedFile"
              style="width: 100%"
              no-caps
              unelevated
              color="primary"
              @click="$refs.fileInput.click()"
              :disable="isRejected"
              >Agregar documento</q-btn
            >
            <div v-else>
              <q-btn
                style="width: 100%"
                @click="onUpload"
                unelevated
                :loading="uploadLoading"
                no-caps
                color="primary"
                :disable="isRejected"
              >
                Subir documento
              </q-btn>
            </div>
          </div>
          <div v-else>
            <q-btn
              style="width: 100%"
              outline
              :loading="uploadLoading"
              no-caps
              color="primary"
              @click="download"
            >
              Descargar documento
            </q-btn>
          </div>
        </div>

        <div align="right">
          <q-btn
            flat
            unelevated
            label="Cancelar"
            class="bg-grey-4"
            v-close-popup
            no-caps
            @click="cancel"
          />
          <q-btn
            color="primary"
            unelevated
            label="Editar"
            class="q-ml-sm"
            no-caps
            @click="editProspect"
            :disable="isRejected"
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
      prospect: {},
      loading: false,
      isRejected: false,
      notesDialog: false,
      dialogLoading: false,
      notes: null,
      selectedFile: null,
      uploadLoading: false,
      docExt: null,
      docRef: null,
    };
  },
  mounted() {
    this.prospectId = this.$route.params.id;
    this.getProspect();
  },
  methods: {
    getProspect() {
      api
        .get(`/prospects/${this.prospectId}`)
        .then((response) => {
          this.prospect = response.data;
          if (this.prospect.status === "Rechazado") {
            this.isRejected = true;
          }

          if (this.prospect.documentRef) {
            this.docRef = this.prospect.documentRef;
          }
        })
        .catch();
    },
    editProspect() {
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
        .patch(`/prospects/${this.prospectId}`, data)
        .then(() => {
          this.$router.push(`/`);
        })
        .finally(() => {
          this.dialogLoading = false;
        });
    },
    openReject() {
      this.notesDialog = true;
    },
    processProspect(status) {
      let data = {};

      if (status === "Autorizado") {
        data = {
          status: status,
        };
      }

      if (status === "Rechazado") {
        console.log("Rechazado");
        data = {
          status: status,
          notes: this.notes,
        };
      }

      api
        .patch(`/prospects/${this.prospectId}`, data)
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
    onFileSelected(event) {
      this.selectedFile = event.target.files[0];
      console.log("File:", this.selectedFile);

      let ext = this.selectedFile.name.split(".");
      this.docExt = ext[ext.length - 1];
    },
    onUpload() {
      this.uploadLoading = true;

      const data = {
        ext: this.docExt,
      };

      api
        .post(`prospects/upload-file/${this.prospectId}`, data)
        .then((response) => {
          api
            .put(response.data.url, this.selectedFile)
            .then((response) => {
              console.log(response);
              this.getProspect();
            })
            .finally(() => {
              this.uploadLoading = false;
            });
        });
    },
    download() {
      api.get(`/prospects/get-file/${this.docRef}`).then((response) => {
        const link = document.createElement("a");
        link.href = response.data;
        link.click();
      });
    },
  },
};
</script>
