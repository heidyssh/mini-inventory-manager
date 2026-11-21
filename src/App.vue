<script setup>
import { computed, ref, watch } from 'vue'
import FiltrosProductos from './components/FiltrosProductos.vue'
import FormularioProducto from './components/FormularioProducto.vue'
import MensajeNotificacion from './components/MensajeNotificacion.vue'
import ModalConfirmacion from './components/ModalConfirmacion.vue'
import ResumenInventario from './components/ResumenInventario.vue'
import TablaProductos from './components/TablaProductos.vue'

const productosGuardados = localStorage.getItem('productos-inventario')
const productos = ref(productosGuardados ? JSON.parse(productosGuardados) : [])
const busqueda = ref('')
const categoriaSeleccionada = ref('')
const productoEnEdicion = ref(null)
const productoAEliminar = ref(null)
const notificacion = ref({ mensaje: '', tipo: 'exito' })
let temporizadorNotificacion

const productosFiltrados = computed(() => {
  const termino = busqueda.value.trim().toLowerCase()

  return productos.value.filter((producto) => {
    const coincideNombre = producto.nombre.toLowerCase().includes(termino)
    const coincideCategoria = !categoriaSeleccionada.value || producto.categoria === categoriaSeleccionada.value
    return coincideNombre && coincideCategoria
  })
})

const textoResultados = computed(() => {
  if (!productos.value.length) return 'Sin productos registrados aún'
  if (!productosFiltrados.value.length) return 'No se encontraron productos'
  if (productosFiltrados.value.length === 1) return '1 producto'
  return `${productosFiltrados.value.length} productos`
})

const valorInventario = computed(() =>
  productos.value.reduce((acumulado, producto) => acumulado + producto.precio * producto.cantidad, 0)
)

const productoMasCaro = computed(() => {
  if (!productos.value.length) return null
  return productos.value.reduce((masCaro, producto) =>
    producto.precio > masCaro.precio ? producto : masCaro
  )
})

watch(
  productos,
  (nuevosProductos) => localStorage.setItem('productos-inventario', JSON.stringify(nuevosProductos)),
  { deep: true }
)

function guardarProducto(datosProducto) {
  if (productoEnEdicion.value) {
    const indice = productos.value.findIndex(
      (producto) => producto.identificador === productoEnEdicion.value.identificador
    )
    productos.value[indice] = { ...productoEnEdicion.value, ...datosProducto }
    productoEnEdicion.value = null
    mostrarNotificacion('Producto actualizado correctamente.')
    return
  }

  productos.value.push({
    identificador: Date.now(),
    ...datosProducto,
  })
  mostrarNotificacion('Producto agregado correctamente.')
}

function editarProducto(producto) {
  productoEnEdicion.value = { ...producto }
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function solicitarEliminacion(identificador) {
  productoAEliminar.value =
    productos.value.find((producto) => producto.identificador === identificador) ?? null
}

function confirmarEliminacion() {
  if (!productoAEliminar.value) return

  const identificador = productoAEliminar.value.identificador
  productos.value = productos.value.filter(
    (producto) => producto.identificador !== identificador
  )
  if (productoEnEdicion.value?.identificador === identificador) productoEnEdicion.value = null
  productoAEliminar.value = null
  mostrarNotificacion('Producto eliminado correctamente.')
}

function mostrarNotificacion(mensaje, tipo = 'exito') {
  notificacion.value = { mensaje, tipo }
  clearTimeout(temporizadorNotificacion)
  temporizadorNotificacion = setTimeout(() => {
    notificacion.value.mensaje = ''
  }, 3500)
}
</script>

<template>
  <header class="page-header">
    <div class="container">
      <h1>Mini Inventory Manager</h1>
    </div>
  </header>

  <main class="container main-content">
    <ResumenInventario
      :total-productos="productos.length"
      :valor-inventario="valorInventario"
      :producto-mas-caro="productoMasCaro"
    />

    <div class="content-grid">
      <FormularioProducto
        :producto-a-editar="productoEnEdicion"
        @guardar-producto="guardarProducto"
        @cancelar-edicion="productoEnEdicion = null"
        @error-validacion="mostrarNotificacion($event, 'error')"
      />

      <section class="card list-card">
        <div class="section-heading">
          <div>
            <h2>Listado de productos</h2>
            <p>{{ textoResultados }}</p>
          </div>
        </div>

        <FiltrosProductos
          v-model:busqueda="busqueda"
          v-model:categoria="categoriaSeleccionada"
        />

        <TablaProductos
          :productos="productosFiltrados"
          @editar-producto="editarProducto"
          @eliminar-producto="solicitarEliminacion"
        />
      </section>
    </div>
  </main>

  <MensajeNotificacion
    :mensaje="notificacion.mensaje"
    :tipo="notificacion.tipo"
    @cerrar="notificacion.mensaje = ''"
  />

  <ModalConfirmacion
    :abierto="Boolean(productoAEliminar)"
    :nombre-producto="productoAEliminar?.nombre"
    @confirmar="confirmarEliminacion"
    @cancelar="productoAEliminar = null"
  />
</template>
