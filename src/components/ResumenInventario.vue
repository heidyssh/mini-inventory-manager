<script setup>
defineProps({
  totalProductos: { type: Number, required: true },
  valorInventario: { type: Number, required: true },
  productoMasCaro: { type: Object, default: null },
})

const formatoMoneda = new Intl.NumberFormat('es-HN', {
  style: 'currency',
  currency: 'HNL',
  minimumFractionDigits: 2,
})
</script>

<template>
  <section class="summary-panel" aria-label="Resumen del inventario">
    <h2>Resumen del inventario</h2>
    <div class="summary-grid">
      <div class="summary-item">
        <span>Productos registrados</span>
        <strong>{{ totalProductos }}</strong>
      </div>
      <div class="summary-item">
        <span>Valor total</span>
        <strong>{{ formatoMoneda.format(valorInventario) }}</strong>
      </div>
      <div class="summary-item">
        <span>Producto con mayor precio</span>
        <strong>{{ productoMasCaro ? productoMasCaro.nombre : 'Sin productos' }}</strong>
        <small v-if="productoMasCaro">{{ formatoMoneda.format(productoMasCaro.precio) }}</small>
      </div>
    </div>
  </section>
</template>
