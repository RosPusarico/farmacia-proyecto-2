<template>
    <div>
        <h1>Editar Producto</h1>
        <form @submit.prevent="submitForm">

            <div class="form-group">
                <label for="name">Nombre:</label>
                <input type="text" id="name" v-model="form.nombre" :class="{ 'is-invalid': errors.nombre }"
                    placeholder="Ingrese el nombre" />
                <div v-if="errors.nombre" class="invalid-feedback">{{ errors.nombre }}</div>
            </div>

            <div class="form-group">
                <label for="name">Descripcion:</label>
                <input type="text" id="name" v-model="form.descripcion" :class="{ 'is-invalid': errors.descripcion }"
                    placeholder="Ingrese la descripcion" />
                <div v-if="errors.descripcion" class="invalid-feedback">{{ errors.descripcion }}</div>
            </div>

            <div class="form-group">
                <label for="precio">Precio:</label>
                <input type="number" id="precio" v-model="form.precio" :class="{ 'is-invalid': errors.precio }"
                    placeholder="Ingrese el precio" />
                <div v-if="errors.precio" class="invalid-feedback">{{ errors.precio }}</div>
            </div>
            <div class="form-group">
                <label for="cantidad">Cantidad:</label>
                <input type="number" id="cantidad" v-model="form.cantidad" :class="{ 'is-invalid': errors.cantidad }"
                    placeholder="Ingrese la cantidad" />
                <div v-if="errors.cantidad" class="invalid-feedback">{{ errors.cantidad }}</div>
            </div>
            <div class="form-group">
                <label for="proveedor">Proveedor:</label>
                <input type="text" id="proveedor" v-model="form.proveedor" :class="{ 'is-invalid': errors.proveedor }"
                    placeholder="Ingrese el proveedor" />
                <div v-if="errors.proveedor" class="invalid-feedback">{{ errors.proveedor }}</div>
            </div>


            <button type="submit" class="btn btn-primary">Registrar</button>
        </form>
    </div>
</template>
    
  <script>
  import { mapState, mapGetters, mapActions } from 'vuex'
  export default {
    name: 'EditProducto',
    data() {
      return {
  
        errors: {}
      };
    },
    props: ['item'],
    methods: {
      ...mapActions(['increment']),
      validateForm() {
        this.errors = {};
  
        if (!this.item.nombre) {
          this.errors.nombre = 'El nombre es obligatorio.';
        }
        if (!this.form.descripcion) {
          this.errors.descripcion = 'La descripcion es obligatorio.';
        }
        if (!this.form.precio) {
          this.errors.precio = 'El precio es obligatorio.';
        }
        if (!this.form.cantidad) {
          this.errors.cantidad = 'La cantidad es obligatorio.';
        }
        if (!this.form.proveedor) {
          this.errors.proveedor = 'El proveedor es obligatorio.';
        }
  
        return Object.keys(this.errors).length === 0;
      },
  
      submitForm() {
        if (this.validateForm()) {
          // Enviar los datos al servidor
          this.save();
          // Reiniciar el formulario
        }
      },
      save() {
        const vm = this;
        this.axios.patch(this.baseUrl + "/productos/" + this.item.id, this.form)
          .then(function (response) {
            if (response.status == '200') {
              vm.$emit('on-update', response.data);
            }
            console.log(response); vm.itemList = response.data;
          })
          .catch(function (error) {
            console.error(error);
          });
      }
    },
    computed: {
      // propiedades computadas que dependen de otras propiedades reactivas
      ...mapState(['count']),
      ...mapGetters(['doubleCount', 'getBaseUrl']),
      baseUrl() {
        return this.getBaseUrl
      },
      form() {
        return Object.assign({}, this.item);
      }
    },
  }
  </script>
    
  <style scoped></style>
    