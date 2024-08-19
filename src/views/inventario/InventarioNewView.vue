<template>
    <div>
      <h1>Registrar inventario</h1>
      <form @submit.prevent="submitForm">

        <div class="form-group">
          <label for="producto">Producto:</label>
          <select id="producto" v-model="form.productoId" :class="{ 'is-invalid': errors.productoId }">
            <option :value="producto.id" v-for="(producto, index) in productoList" :key="`producto-${index}`">{{ producto.nombre }}
            </option>
          </select>
          <div v-if="errors.productoId" class="invalid-feedback">{{ errors.productoId }}</div>
        </div>
  
        <div class="form-group">
          <label for="cantidad">Cantidad:</label>
          <input type="number" id="cantidad" v-model="form.cantidad" :class="{ 'is-invalid': errors.cantidad }"
            placeholder="Ingrese la cantidad" />
          <div v-if="errors.cantidad" class="invalid-feedback">{{ errors.cantidad }}</div>
        </div>
  
        <div class="form-group">
          <label for="observacion">Observaciones:</label>
          <textarea type="text" id="observacion" v-model="form.observacion" :class="{ 'is-invalid': errors.observacion }"
            placeholder="Ingrese las observaciones" ></textarea>
          <div v-if="errors.observacion" class="invalid-feedback">{{ errors.observacion }}</div>
        </div>

        <div class="form-group">
        <label for="fecha">Fecha:</label>
        <input type="date" id="fecha" v-model="form.fecha" :class="{ 'is-invalid': errors.fecha }"
          placeholder="Ingrese la fecha" />
        <div v-if="errors.fecha" class="invalid-feedback">{{ errors.fecha }}</div>
        </div>


  
        <button type="submit" class="btn btn-primary">Registrar</button>
      </form>
    </div>
  </template>
    
  <script>
  import { mapState, mapGetters, mapActions } from 'vuex'
  export default {
    name: 'RegisterInventario',
    data() {
      return {
        productoList: [],
        form: {
            productoId: null,
            cantidad: '',
            observacion: '',
            fecha:null
        },
        errors: {}
      };
    },
    methods: {
      ...mapActions(['increment']),
      validateForm() {
        this.errors = {};
        
        if (!this.form.productoId) {
          this.errors.productoId = 'El Producto es obligatoria.';
        }

        if (!this.form.cantidad) {
          this.errors.cantidad = 'La cantidad es obligatorio.';
        }
  
        if (!this.form.observacion) {
          this.errors.observacion = 'La observacion es obligatoria.';
        }  
        if (!this.form.fecha) {
        this.errors.fecha = 'La fecha es obligatorio.';
        }       
        return Object.keys(this.errors).length === 0;
      },
  
      submitForm() {
        if (this.validateForm()) {
          // Enviar los datos al servidor
          this.save();
          // Reiniciar el formulario
          this.form = {
            productoId: null,
            cantidad: '',
            observacion: ''
          };
        }
      },
      save() {
        const vm = this;
        this.axios.post(this.baseUrl + "/inventarios", this.form)
          .then(function (response) {
            if (response.status == '201') {
              vm.$emit('on-register', response.data);
            }
            vm.itemList = response.data;
          })
          .catch(function (error) {
            console.error(error);
          });
      },
      getProductoList() {
        const vm = this;
        this.axios.get(this.baseUrl + "/productos")
          .then(function (response) {
            vm.productoList = response.data;
          })
          .catch(function (error) {
            console.error(error);
          });
      },
    },
    computed: {
      // propiedades computadas que dependen de otras propiedades reactivas
      ...mapState(['count']),
      ...mapGetters(['doubleCount', 'getBaseUrl']),
      baseUrl() {
        return this.getBaseUrl
      }
    },
    mounted() {
      this.getProductoList();
    },
  }
  </script>
    
  <style scoped></style>
    