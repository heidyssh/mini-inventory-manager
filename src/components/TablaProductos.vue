<script setup>
defineProps({
  productos: { type: Array, required: true },
})

const emitir = defineEmits(['editar-producto', 'eliminar-producto'])

const formatoMoneda = new Intl.NumberFormat('es-HN', {
  style: 'currency',
  currency: 'HNL',
  minimumFractionDigits: 2,
})
</script>

<template>
  <div class="table-wrapper">
    <table v-if="productos.length">
      <thead>
        <tr>
          <th>Nombre</th>
          <th>Categoría</th>
          <th>Precio</th>
          <th>Cantidad</th>
          <th>Subtotal</th>
          <th class="actions-heading">Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="producto in productos" :key="producto.identificador">
          <td data-label="Nombre">{{ producto.nombre }}</td>
          <td data-label="Categoría">{{ producto.categoria }}</td>
          <td data-label="Precio">{{ formatoMoneda.format(producto.precio) }}</td>
          <td data-label="Cantidad">{{ producto.cantidad }}</td>
          <td data-label="Subtotal">{{ formatoMoneda.format(producto.precio * producto.cantidad) }}</td>
          <td data-label="Acciones" class="table-actions">
            <button class="edit-button" type="button" @click="emitir('editar-producto', producto)">Editar</button>
            <button class="delete-button" type="button" @click="emitir('eliminar-producto', producto.identificador)">Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>

    <p v-else class="empty-state">No hay productos que mostrar.</p>
  </div>
</template>
