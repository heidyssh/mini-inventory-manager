<script setup>
defineProps({
  abierto: { type: Boolean, required: true },
  nombreProducto: { type: String, default: '' },
})

const emitir = defineEmits(['confirmar', 'cancelar'])
</script>

<template>
  <div v-if="abierto" class="modal-backdrop" @click.self="emitir('cancelar')">
    <section class="modal" role="dialog" aria-modal="true" aria-labelledby="modal-title">
      <h2 id="modal-title">Eliminar producto</h2>
      <p>
        ¿Deseas eliminar <strong>{{ nombreProducto }}</strong>? Esta acción no se puede deshacer.
      </p>
      <div class="modal-actions">
        <button class="secondary-button" type="button" @click="emitir('cancelar')">Cancelar</button>
        <button class="danger-button" type="button" @click="emitir('confirmar')">Sí, eliminar</button>
      </div>
    </section>
  </div>
</template>
