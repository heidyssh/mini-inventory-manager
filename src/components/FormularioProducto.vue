<script setup>
import { reactive, ref, watch } from 'vue'

const propiedades = defineProps({
  productoAEditar: {
    type: Object,
    default: null,
  },
})

const emitir = defineEmits(['guardar-producto', 'cancelar-edicion', 'error-validacion'])

const categorias = ['Tecnología', 'Hogar', 'Oficina', 'Otros']
const mensajeError = ref('')

const formulario = reactive({
  nombre: '',
  categoria: '',
  precio: '',
  cantidad: '',
})

watch(
  () => propiedades.productoAEditar,
  (producto) => {
    if (producto) {
      formulario.nombre = producto.nombre
      formulario.categoria = producto.categoria
      formulario.precio = producto.precio
      formulario.cantidad = producto.cantidad
    } else {
      reiniciarFormulario()
    }
    mensajeError.value = ''
  },
  { immediate: true }
)

function reiniciarFormulario() {
  formulario.nombre = ''
  formulario.categoria = ''
  formulario.precio = ''
  formulario.cantidad = ''
}

function limpiarFormulario() {
  reiniciarFormulario()
  mensajeError.value = ''
}

function limpiarNombre(evento) {
  const valorLimpio = evento.target.value.replace(/[^a-zA-ZáéíóúÁÉÍÓÚüÜñÑ\s-]/g, '')
  evento.target.value = valorLimpio
  formulario.nombre = valorLimpio
}

function impedirTeclaNumericaInvalida(evento, permitirDecimal) {
  const teclasInvalidas = ['-', '+', 'e', 'E']
  if (!permitirDecimal) teclasInvalidas.push('.', ',')
  if (teclasInvalidas.includes(evento.key)) evento.preventDefault()
}

function impedirValorNegativo(evento, campo) {
  if (Number(evento.target.value) < 0) {
    evento.target.value = ''
    formulario[campo] = ''
  }
}

function enviarFormulario() {
  const nombre = formulario.nombre.trim()
  const precio = Number(formulario.precio)
  const cantidad = Number(formulario.cantidad)

  if (!nombre || !formulario.categoria || formulario.precio === '' || formulario.cantidad === '') {
    mensajeError.value = 'Todos los campos son obligatorios.'
    emitir('error-validacion', mensajeError.value)
    return
  }

  if (precio <= 0 || cantidad <= 0) {
    mensajeError.value = 'El precio y la cantidad deben ser mayores a 0.'
    emitir('error-validacion', mensajeError.value)
    return
  }

  if (!Number.isInteger(cantidad)) {
    mensajeError.value = 'La cantidad debe ser un número entero.'
    emitir('error-validacion', mensajeError.value)
    return
  }

  if (cantidad > 500) {
    mensajeError.value = 'La cantidad máxima permitida es 500.'
    emitir('error-validacion', mensajeError.value)
    return
  }

  emitir('guardar-producto', {
    nombre,
    categoria: formulario.categoria,
    precio,
    cantidad,
  })

  mensajeError.value = ''
  if (!propiedades.productoAEditar) reiniciarFormulario()
}

function cancelarEdicion() {
  emitir('cancelar-edicion')
}
</script>

<template>
  <section class="card form-card">
    <h2>{{ productoAEditar ? 'Editar producto' : 'Registrar producto' }}</h2>

    <form @submit.prevent="enviarFormulario" novalidate>
      <div class="field full-field">
        <label for="name">Nombre del producto</label>
        <input
          id="name"
          v-model="formulario.nombre"
          type="text"
          placeholder="Ej. Teclado"
          autocomplete="off"
          @input="limpiarNombre"
        />
      </div>

      <div class="field full-field">
        <label for="category">Categoría</label>
        <select id="category" v-model="formulario.categoria">
          <option value="" disabled>Seleccione una categoría</option>
          <option v-for="categoria in categorias" :key="categoria" :value="categoria">
            {{ categoria }}
          </option>
        </select>
      </div>

      <div class="field">
        <label for="price">Precio</label>
        <input
          id="price"
          v-model.number="formulario.precio"
          type="number"
          min="0.01"
          step="0.01"
          placeholder="0.00"
          @keydown="impedirTeclaNumericaInvalida($event, true)"
          @input="impedirValorNegativo($event, 'precio')"
        />
      </div>

      <div class="field">
        <label for="quantity">Cantidad</label>
        <input
          id="quantity"
          v-model.number="formulario.cantidad"
          type="number"
          min="1"
          max="500"
          step="1"
          placeholder="0"
          @keydown="impedirTeclaNumericaInvalida($event, false)"
          @input="impedirValorNegativo($event, 'cantidad')"
        />
      </div>

      <p v-if="mensajeError" class="error-message" role="alert">{{ mensajeError }}</p>

      <div class="form-actions">
        <button class="primary-button" type="submit">
          {{ productoAEditar ? 'Guardar cambios' : 'Agregar producto' }}
        </button>
        <button class="secondary-button" type="button" @click="limpiarFormulario">
          Limpiar
        </button>
        <button v-if="productoAEditar" class="secondary-button" type="button" @click="cancelarEdicion">
          Cancelar
        </button>
      </div>
    </form>
  </section>
</template>
