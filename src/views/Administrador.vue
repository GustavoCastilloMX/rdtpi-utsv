<template>
  <v-container>
    <h1>Administrador <v-icon right color="green dark-1">mdi-ninja</v-icon></h1>
    <v-row>
      <v-col cols="8">
        <v-text-field
          outlined
          dense
          color="green"
          v-model="search"
          append-icon="mdi-magnify"
          label="Buscar"
          single-line
          hide-details
        ></v-text-field>
      </v-col>
      <v-col cols="4" class="pb-0 mt-1">
        <v-btn
          depressed
          color="green"
          dark
          block
          @click="showNuevoAdmin = true"
        >
          Agregar administrador</v-btn
        >
      </v-col>
      <v-col cols="12">
        <v-data-table
          :headers="headers"
          :items="users"
          :search="search"
          class="elevation-1"
        >
          <template v-slot:item.actions="{ item }">
            <v-btn @click="editarUsuario(item)" icon color="blue">
              <v-icon> mdi-account-edit </v-icon>
            </v-btn>
            <v-btn @click="eliminarUsuario(item)" icon color="red">
              <v-icon> mdi-trash-can</v-icon>
            </v-btn>
          </template>
        </v-data-table>
      </v-col>
    </v-row>

    <nuevoAdmin
      v-if="showNuevoAdmin"
      :showNuevoAdmin="showNuevoAdmin"
      @cerrar="showNuevoAdmin = false"
      @usuarioCreado="usuarioCreado"
    />

    <editarAdmin
      v-if="showEditarAdmin"
      :showEditarAdmin="showEditarAdmin"
      :dataAdmin="dataAdmin"
      @cerrar="showEditarAdmin = false"
      @usuarioActualizado="usuarioCreado"
    />
  </v-container>
</template>

<script>
import { firebaseMixin } from "../mixins/RegistrosFirebase";
export default {
  name: "Administrador",
  mixins: [firebaseMixin],
  mounted() {
    this.getUsersByRol("admin");
  },
  components: {
    nuevoAdmin: () => import("../components/administrador/nuevoAdmin"),
    editarAdmin: () => import("../components/administrador/editarAdmin"),
  },
  data: () => ({
    showEditarAdmin: false,
    showNuevoAdmin: false,
    search: "",
    users: [],
    headers: [
      {
        text: "Nombre",
        align: "start",
        sortable: false,
        value: "nombre",
      },
      { text: "Apellido paterno", value: "apellidoPaterno" },
      { text: "Apellido materno", value: "apellidoMaterno" },
      { text: "Correo", value: "correo" },
      { text: "Género", value: "genero" },
      { text: "Acciones", value: "actions" },
    ],
    dataAdmin: {},
  }),
  methods: {
    usuarioCreado() {
      this.users = [];
      this.getUsersByRol("admin");
      this.showNuevoAdmin = false;
      this.showEditarAdmin = false;
    },
    editarUsuario(usuario) {
      this.dataAdmin = Object.assign({}, usuario);
      this.showEditarAdmin = true;
      console.log(usuario);
    },
    async eliminarUsuario(usuario) {
      const response = confirm(
        `¿Seguro que desea eliminar a ${usuario.nombre}? Esta acción no se podrá deshacer.`
      );

      try {
        if (response) {
          this.users = await [];
          await this.deleteUserWhitEmailPassword(usuario);
          this.getUsersByRol("admin");
        }
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
</style>