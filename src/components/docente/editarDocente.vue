<template>
  <v-row justify="center">
    <v-dialog v-model="showEditarDocente" persistent max-width="800">
      <v-card>
        <v-card-title class="headline"> Editar administrador </v-card-title>
        <v-card-text class="mt-3">
          <v-form ref="form" v-model="valid" lazy-validation>
            <v-row>
              <v-col cols="12" class="ma-0 pt-0 pb-0"
                ><v-text-field
                  label="Nombre"
                  placeholder="Ingresa el nombre"
                  v-model="data.nombre"
                  :rules="campoRequerido"
                  outlined
                  dense
                ></v-text-field>
              </v-col>
              <v-col cols="12" class="ma-0 pt-0 pb-0"
                ><v-text-field
                  label="Apellido paterno"
                  placeholder="Ingresa el apellido paterno"
                  v-model="data.apellidoPaterno"
                  :rules="campoRequerido"
                  outlined
                  dense
                ></v-text-field>
              </v-col>
              <v-col cols="12" class="ma-0 pt-0 pb-0"
                ><v-text-field
                  label="Apellido materno"
                  placeholder="Ingresa el apellido materno"
                  v-model="data.apellidoMaterno"
                  :rules="campoRequerido"
                  outlined
                  dense
                ></v-text-field>
              </v-col>
              <v-col cols="12" class="ma-0 pt-0 pb-0"
                ><v-select
                  :items="genero"
                  label="Género"
                  placeholder="Selecciona el género"
                  v-model="data.genero"
                  :rules="campoRequerido"
                  outlined
                  dense
                ></v-select>
              </v-col>
              <v-col cols="12" class="ma-0 pt-0 pb-0"
                ><v-select
                  :items="abscripcion"
                  label="Carrera de abscripción"
                  placeholder="Selecciona la carrera de abscripción"
                  v-model="data.carreraAbscripcion"
                  :rules="campoRequerido"
                  outlined
                  dense
                ></v-select>
              </v-col>
              <v-col cols="12" class="ma-0 pt-0 pb-0"
                ><v-select
                  :items="gradoAcademico"
                  label="Grado academico"
                  placeholder="Selecciona el grado academico"
                  v-model="data.gradoAcademico"
                  :rules="campoRequerido"
                  outlined
                  dense
                ></v-select>
              </v-col>
              <v-col cols="12" class="ma-0 pt-0 pb-0"
                ><v-select
                  :items="puesto"
                  label="Tipo de contrato"
                  placeholder="Selecciona el tipo de contrato"
                  v-model="data.puesto"
                  :rules="campoRequerido"
                  outlined
                  dense
                ></v-select>
              </v-col>
              <v-col cols="12" class="ma-0 pt-0 pb-0"
                ><v-text-field
                  label="Correo"
                  placeholder="Ingresa el correo"
                  v-model="data.correo"
                  :rules="emailRules"
                  outlined
                  dense
                ></v-text-field>
              </v-col>
              <v-col cols="12" class="ma-0 pt-0 pb-0"
                ><v-text-field
                  label="Contraseña"
                  placeholder="Ingresa la contraseña"
                  v-model="data.password"
                  type="password"
                  :rules="passwordRules"
                  outlined
                  dense
                ></v-text-field>
              </v-col>
            </v-row>
          </v-form>
          <v-row justify="center" v-if="authError">
            <v-alert outlined color="red">
              <div>{{ messageError }}</div>
            </v-alert>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" text @click="cerrar"> Cancelar </v-btn>
          <v-btn color="green darken-1" text @click="actualizarUsuario">
            Registrar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
import { validacionesMixin } from "../../mixins/Validaciones";
import { firebaseMixin } from "../../mixins/RegistrosFirebase";
import { mapActions } from "vuex";

export default {
  name: "editarDocente",
  mixins: [validacionesMixin, firebaseMixin],
  props: {
    showEditarDocente: {
      type: Boolean,
      required: true,
    },
    data: {
      type: Object,
      required: true,
    },
  },
  mounted() {
    this.userOld = Object.assign({}, this.data);
  },
  data: () => ({
    authError: false,
    messageError: "",
    userOld: {},
    uid: "",
    genero: ["Mujer", "Hombre"],
    abscripcion: ["Tecnologías de la información y comunicación", "Contaduría"],
    gradoAcademico: ["Licenciatura", "Ingenieria", "Maestría", "Doctorado"],
    puesto: ["Profesor de Tiempo Completo", "No PTC"],
  }),
  methods: {
    ...mapActions(["showLoading"]),
    cerrar() {
      this.$refs.form.reset();
      this.$emit("cerrar");
    },
    async actualizarUsuario() {
      if (!this.$refs.form.validate()) return;
      this.showLoading(true);
      try {
        await this.updateUserWhithEmailPassword(this.userOld, this.data);
        this.$refs.form.reset();
        this.$emit("usuarioActualizado");
      } catch (error) {
        console.log(error.message);
      } finally {
        this.showLoading(false);
      }
    },
  },
  watch: {
    data: function () {
      console.log("ENTREE");
      this.userOld = Object.assign({}, this.data);
    },
  },
};
</script>

<style lang="scss" scoped>
</style>