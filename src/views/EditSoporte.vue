
<template>
  <div>
    <br />
    <section v-if="errored">
      <p>Lo sentimos, no es posible Actualizar el registro en este momento</p>
    </section>
    <section v-else>
      <div v-if="loading">
        Cargando..
        <div class="spinner-border text-success" role="status">
          <span class="sr-only">Loading...</span>
        </div>
      </div>
      <div v-else></div>
      <section class="content">
        <div style="width: 80%; margin: 0 auto;">
          <div class="card card-success">
            <nav class="nav grey lighten-4 py-4">
              <a @click="back" class="nav-item nav-link">Atrás</a>
            </nav>
            <h3 class="text-center">Editar soporte</h3>
            <form @submit.prevent="handleSubmit">
              <div class="card-body">
                <div class="form-group">
                  <label for="soporte">Nombre</label>
                  <input
                    type="text"
                    pattern="[A-Za-z0-9 ]+"
                    title=" Solo Letras y números. Tamaño máximo: 255"
                    v-model.trim="soporte.soporte"
                    id="soporte"
                    name="soporte"
                    placeholder="Nombre"
                    class="form-control"
                    :class="{ 'is-invalid': submitted && $v.soporte.soporte.$error }"
                  />
                  <div v-if="submitted && $v.soporte.soporte.$error" class="invalid-feedback">
                    <span v-if="!$v.soporte.soporte.required">El campo es requerido</span>
                    <span
                      v-if="!$v.soporte.soporte.maxLength"
                    >El campo no debe superar los 255 caracteres</span>
                  </div>
                </div>
                <div class="form-group">
                  <label for="vinculo">Vinculo</label>
                  <input
                    type="text"
                    pattern="[A-Za-z0-9 ./ ? = @  :]+"
                    title=" Solo Letras y números. Tamaño máximo: 255"
                    v-model.trim="soporte.vinculo"
                    id="vinculo"
                    name="vinculo"
                    placeholder="Nombre"
                    class="form-control"
                    :class="{ 'is-invalid': submitted && $v.soporte.vinculo.$error }"
                  />
                  <div v-if="submitted && $v.soporte.vinculo.$error" class="invalid-feedback">
                    <span v-if="!$v.soporte.vinculo.required">El campo es requerido</span>
                    <span
                      v-if="!$v.soporte.vinculo.maxLength"
                    >El campo no debe superar los 255 caracteres</span>
                  </div>
                </div>

                <button type="submit" class="btn btn-outline-success">Actualizar</button>
              </div>
            </form>
          </div>
        </div>
      </section>
    </section>
  </div>
</template>
<script>
import ApiService from "../services/api.service";
import Swal from "sweetalert2/dist/sweetalert2.all.min.js";
import { required, maxLength } from "vuelidate/lib/validators";
export default {
  data() {
    return {
      submitted: false,
      loading: true,
      errored: false,
      soporte: {
        soporte: "",
        vinculo: ""
      }
    };
  },

  created() {
    ApiService.get(`/soporte/${this.$route.params.id}/edit`)
      .then(response => {
        if (response.status === 204) {
          this.$swal({
            type: "info",
            text: "fallo al cargar datos del grupo",
            timer: 2000,
            showCancelButton: false,
            showConfirmButton: false
          });
        } else if (response.status === 200) {
          this.soporte = response.data[0];
        }
      })
      .catch(error => {
        console.log(error);
        this.errored = true;
      })
      .finally(() => (this.loading = false));
  },

  //Reglas de validacion para VueValidate
  validations: {
    soporte: {
      soporte: {
        required,
        maxLength: maxLength(255)
      },
      vinculo: { required, maxLength: maxLength(255) }
    }
  },

  methods: {
    back() {
      this.$router.go(-1);
    },
    /* Despliega mensaje de exito al guardar un registro */
    showAlert() {
      this.$swal({
        type: "success",
        text: "Registro Actualizado con exito",
        timer: 2000,
        showCancelButton: false,
        showConfirmButton: false
      });
    },
    updateSoporte() {
      ApiService.put(`/soporte/${this.$route.params.id}`, {
        soporte: this.soporte.soporte,
        vinculo: this.soporte.vinculo
      })
        .then(response => {
          if (response.status === 200) {
            this.showAlert();
            this.back();
          } else if (response.status === 204) {
            this.$swal({
              type: "warning",
              text: "No hay soportes",
              timer: 2000,
              showCancelButton: false,
              showConfirmButton: false
            });
          }
        })
        .catch(error => {
          console.log(error);
          this.errored = true;
        });
    },

    onChange(e) {},

    handleSubmit(e) {
      this.submitted = true;
      // Se detiene aqui si es invalido, de lo contrario ejecuta el submit()
      this.$v.$touch();
      if (this.$v.$invalid) {
        return;
      }
      this.updateSoporte();
    }
  }
};
</script>