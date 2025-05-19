<template>
  <div class="container-fluid py-4">
    <div class="card">
      <div class="card-header pb-0">
        <h6>Listado de Almacenes</h6>
      </div>
      <div class="card-body px-4 pt-0 pb-4">
        <!-- DataTable integrado con estilo -->
        <DataTable
          class="table table-striped"
          :data="almacenes"
          :columns="columns"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'
import Swal from 'sweetalert2'
import { useRouter } from 'vue-router'
import DataTable from 'datatables.net-vue3';
//import DataTablesCore from 'datatables.net-dt';
import DataTablesCore from 'datatables.net-bs5';
DataTable.use(DataTablesCore)

const router = useRouter()
const almacenes = ref([])

const columns = [
  { title: 'Código', data: 'codigo' },
  { title: 'Nombre', data: 'nombre' },
  { title: 'Dirección', data: 'direccion' },
  { title: 'Ubigeo', data: 'ubigeo' },
  {
    title: 'Estado',
    data: 'status',
     responsive: true,
  select: true,
    render: data => data ? '<span class="badge bg-gradient-success">Activo</span>' : '<span class="badge bg-gradient-secondary">Inactivo</span>'
  }
]

onMounted(async () => {
  try {
    const token = localStorage.getItem('token')
    const response = await axios.get('http://localhost:3000/api/almacenes', {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })

    almacenes.value = response.data
  } catch (err) {
    const msg = err.response?.data?.mensaje || err.message

    if (msg.toLowerCase().includes('token expirado')) {
      await Swal.fire({
        icon: 'warning',
        title: 'Sesión expirada',
        text: 'Vuelve a iniciar sesión'
      })
      localStorage.removeItem('token')
      router.push('/signin')
    } else {
      Swal.fire({
        icon: 'error',
        title: 'Error al cargar almacenes',
        text: msg
      })
    }
  }
})

</script>
<style>
    @import 'bootstrap';
    @import 'datatables.net-bs5';
</style>