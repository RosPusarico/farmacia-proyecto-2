<template>
    <div>
        <Modal v-model:modelValue="showModalNuevo">
            <InventarioNewView @on-register="onRegister($event)"/>
        </Modal>
        <Modal v-model:modelValue="showModalEdit">
            <InventarioEditView @on-update="onUpdate($event)" :item="itemToEdit"/>
        </Modal>
        <h1>Lista de Inventario</h1>
        <button @click="showModalNuevo = true" class="btn btn-primary">Nuevo</button>
        <button @click="buscar()" class="btn btn-lith" style="float:right">Buscar</button>
        <input type="search" style="float:right" v-model="textToSearch" @search="buscar()">

        <div style="margin: 20px 0;">
            <h3>Filtros:</h3>
            <form @submit.prevent="filtrar()">

                <label for="fecha"> Fecha: </label>
                <input type="date" id="fecha" v-model="filter.fecha" placeholder="Ingrese la fecha" />

                <label for="producto"> producto: </label>
                <select id="producto" v-model="filter.productoId">
                    <option value="">Todos</option>
                    <option :value="producto.id" v-for="(producto, index) in productoList" :key="`producto-${index}`">{{ producto.nombre }}
                    </option>
                  </select>
                <button type="submit" class="btn btn-lith">Fitrar</button>
            </form>
        </div>



        <table>
            <thead>
                <tr>
                    <th>No.</th>
                    <th>Producto</th>
                    <th>Cantidad</th>    
                    <th>Observaciones</th> 
                    <th>Fecha de registro</th>            
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in itemList" :key="index">
                    <td>{{ 1 + index }}</td>
                    <td>{{ item.producto.nombre}}</td>
                    <td>{{ item.cantidad }}</td>
                    <td>{{ item.observacion }}</td>
                    <td>{{ item.fecha }}</td>
                    <td>
                        <button @click="edit(item)" class="btn btn-dark" style="margin-right: 15px;">Editar</button>
                        <button @click="Eliminar(item.id)" class="btn btn-danger">Eliminar</button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>
  
<script>
import { mapState, mapGetters, mapActions } from 'vuex'
import Modal from '../../components/Modal.vue'
import InventarioNewView from './InventarioNewView'
import InventarioEditView from './InventarioEditView'

export default {
    name: 'Inventario',
    data() {
        return {
            currentPage: 1,
            totalPages: 100, // Este valor debe ser calculado según tus datos
            showModalNuevo: false,
            showModalEdit: false,
            itemToEdit: null,
            textToSearch: '',
            itemList: [],
            productoList: [],
            path: '',
            filter: {
                fecha: null,
                productoId:''
            }
        }
    },
    components: {
        // Registro de componentes que se utilizaran.
        Modal,
        InventarioNewView,
        InventarioEditView
    },
    methods: {
        // métodos que se pueden llamar desde la plantilla o desde otras partes del componente.
        ...mapActions(['increment']),
        getList() {
            const vm = this;
            this.path = this.baseUrl + "/inventarios?_sort=fecha&_order=desc,asc&_expand=producto&" + this.textToFilter + "&q=" + this.textToSearch;
            this.axios.get(this.baseUrl + "/inventarios?_sort=fecha&_order=desc,asc&_expand=producto&" + this.textToFilter + "&q=" + this.textToSearch)
                .then(function (response) {
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
        edit(item) {
            this.itemToEdit = Object.assign({}, item);
            this.showModalEdit = true;
        },
        Eliminar(id) {
            if (confirm("¿Esta Seguro de eliminar el registro?")) {
                const vm = this;
                this.axios.delete(this.baseUrl + "/inventarios/" + id)
                    .then(function (response) {
                        vm.getList();
                        vm.$toast.show("Registro eliminado.", "danger");
                    })
                    .catch(function (error) {
                        console.error(error);
                    });
            }

        },
        buscar() {
            this.getList();
        },
        filtrar() {
            this.textToFilter = '';
            if (this.filter.fecha != null && this.filter.fecha != '') {
                this.textToFilter += "&fecha=" + this.filter.fecha;
            }
            if (this.filter.productoId != null && this.filter.productoId != '') {
                this.textToFilter += "&productoId=" + this.filter.productoId;
            }
            this.getList();
        },
        onRegister(event) {
            this.getList();
            this.showModalNuevo = false;
            this.$toast.show('Registro exitoso', 'success');
        },
        onUpdate(event) {
            this.getList();
            this.showModalEdit = false;
            this.itemToEdit = null;
            this.$toast.show('Edicion exitosa', 'info');
        },
        showToast(message, type) {
            this.$toast.show(message, type);
        }
    },
    computed: {
        // propiedades computadas que dependen de otras propiedades reactivas
        ...mapState(['count']),
        ...mapGetters(['doubleCount', 'getBaseUrl']),
        baseUrl() {
            return this.getBaseUrl
        }
    },
    props: {
        // propiedades que el componente puede recibir.
    },
    mounted() {
        this.getList();
        this.getProductoList();
    },
    emits: [] // los eventos personalizados que el componente puede emitir.
}
</script>
  
<style></style>