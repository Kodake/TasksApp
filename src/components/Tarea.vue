<template>
  <div>
    <h1 class="display-4 text-center">Listado de Tareas</h1>
    <hr />
    <div class="row">
      <div class="col-lg-8 offset-lg-2">
        <div class="card mt-4">
          <div class="card-body">
            <div class="input-group">
              <input
                type="text"
                v-model="tarea"
                placeholder="Agregar Tarea"
                class="form-control form-control-lg"
              />
              <div class="input-group-append">
                <button @click="agregarTarea()" class="btn btn-success btn-lg">Agregar</button>
              </div>
            </div>

            <br />

            <div class="text-center p-3" v-if="loading">
              <div class="spinner-border text-primary" role="status">
                <span class="sr-only">Loading...</span>
              </div>
              <div class="spinner-border text-success" role="status">
                <span class="sr-only">Loading...</span>
              </div>
              <div class="spinner-border text-danger" role="status">
                <span class="sr-only">Loading...</span>
              </div>
            </div>

            <h5 v-if="listTareas.length == 0">No hay tareas para realizar</h5>

            <ul v-if="!loading" class="list-group">
              <li v-for="(tarea, index) of listTareas" :key="index" class="list-group-item d-flex justify-content-between">
                <span class="cursor" v-bind:class="{'text-success' : tarea.estado}" v-on:click="editarTarea(tarea, tarea.id)">
                  <i v-bind:class="[tarea.estado ? 'fas fa-check-circle' : 'far fa-circle']"></i>
                </span>
                {{tarea.nombre}}
                <span class="text-danger cursor" @click="eliminarTarea(tarea.id)">
                  <i class="fas fa-trash-alt"></i>
                </span>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "Tarea",
  data() {
    return {
      tarea: "",
      listTareas: [],
      loading: false
    };
  },
  methods: {
      agregarTarea() {
          const tarea = {
              nombre: this.tarea,
              estado: false
          }
          this.loading = true;
          axios.post("https://localhost:44326/api/Tareas/", tarea).then(resp => {
            this.loading = false;
            this.obtenerTareas();
            console.log(resp);
          }).catch(err => {
            this.loading = false;
            console.error(err);
          });
          this.tarea = '';
      },
      editarTarea(tarea, id) {
        /*  this.listTareas[index].estado = !tarea.estado; */
        this.loading = true;
        axios.put("https://localhost:44326/api/Tareas/"+ id, tarea).then(()=> {
          this.obtenerTareas();
          this.loading = false
        }).catch(() => this.loading = false);
      },
      eliminarTarea(id) {
        this.loading = true;
          axios.delete("https://localhost:44326/api/Tareas/" + id).then(resp => {
            this.loading = false;
            this.obtenerTareas();
            console.log(resp);
        }).catch(err => {
          this.loading = false;
          console.error(err);
        });
      },
      obtenerTareas() {
        this.loading = true;
        axios.get("https://localhost:44326/api/Tareas").then(resp => {
          this.listTareas = resp.data;
          this.loading = false;
        }).catch(() => this.loading = false);
      }
  },
  created: function() {
    this.obtenerTareas();
  }
};
</script>

<style scoped>
.cursor {
  cursor: pointer;
}
</style>