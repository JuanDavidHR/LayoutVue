<template>
  <div class="card p-4">
    <h5 class="mb-3">Listado de Almacenes</h5>
    <EasyDataTable
       v-if="almacenes && almacenes.length"
        :headers="columnas"
        :items="almacenes"
      table-class-name="table table-striped"
      rows-per-page="5"
    >
      <!-- Personalizamos la columna de estado -->
      <template #item-status="{ status }">
        <span class="badge bg-gradient-success" v-if="status">Activo</span>
        <span class="badge bg-gradient-secondary" v-else>Inactivo</span>
      </template>
    </EasyDataTable>
  </div>
</template>


<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'
import Swal from 'sweetalert2'
import EasyDataTable from 'vue3-easy-data-table'
import 'vue3-easy-data-table/dist/style.css'

const router = useRouter()
const almacenes = ref([]); // ✅ inicia como arreglo vacío

onMounted(async () => {
  try {
    const token = localStorage.getItem('token');
    const response = await axios.get('http://localhost:3000/api/almacenes', {
      headers: { Authorization: `Bearer ${token}` }
    });
    almacenes.value = response.data;
  } catch (err) {
    const msg = err.response?.data?.mensaje || err.message
    if (msg.toLowerCase().includes('token expirado')) {
      await Swal.fire({ icon: 'warning', title: 'Sesión expirada', text: 'Por favor inicia sesión nuevamente.' })
      localStorage.removeItem('token')
      router.push('/signin')
    } else {
      Swal.fire({ icon: 'error', title: 'Error', text: 'No se pudo cargar almacenes: ' + msg })
    }
  }
})

const columnas = [
  { label: 'Código', field: 'codigo' },
  { label: 'Nombre', field: 'nombre' },
  { label: 'Dirección', field: 'direccion' },
  { label: 'Ubigeo', field: 'ubigeo' },
  {
    label: 'Estado',
    field: 'status',
    sortable: false,
    width: '120px'
  }
]

</script>
