<template>
  <div class="container-fluid py-4">
    <div class="card">
      <div class="card-header pb-0">
        <h6>Listado de Almacenes</h6>
      </div>
      <div class="card-body px-0 pt-0 pb-2">
        <div class="table-responsive p-0">
          <table class="table align-items-center justify-content-center mb-0">
            <thead>
              <tr>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Código</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2">Nombre</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2">Dirección</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2">Ubigeo</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2">Estado</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="almacen in almacenes" :key="almacen.id">
                <td>
                  <h6 class="mb-0 text-sm">{{ almacen.codigo }}</h6>
                </td>
                <td>
                  <p class="text-sm font-weight-bold mb-0">{{ almacen.nombre }}</p>
                </td>
                <td>
                  <span class="text-xs font-weight-bold">{{ almacen.direccion }}</span>
                </td>
                <td>
                  <span class="text-xs font-weight-bold">{{ almacen.ubigeo }}</span>
                </td>
                <td>
                  <span class="badge bg-gradient-success" v-if="almacen.status">Activo</span>
                  <span class="badge bg-gradient-secondary" v-else>Inactivo</span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'
import Swal from 'sweetalert2'
import { useRouter } from 'vue-router'

const router = useRouter()
const almacenes = ref([])

onMounted(async () => {
  try {
    const token = localStorage.getItem('token');
    const response = await axios.get('http://localhost:3000/api/almacenes', {
      headers: {
        Authorization: `Bearer ${token}`
      }
    });
    almacenes.value = response.data;
  } catch (err) {
    const msg = err.response?.data?.mensaje || err.message;

    if (msg.toLowerCase().includes('token expirado')) {
      await Swal.fire({
        icon: 'warning',
        title: 'Sesión expirada',
        text: 'Por favor inicia sesión nuevamente.',
        confirmButtonText: 'Aceptar'
      });
      router.push('/signin');
    } else {
      Swal.fire({
        icon: 'error',
        title: 'Error',
        text: 'No se pudo cargar almacenes: ' + msg
      });
    }
  }
});

</script>
