<script setup>
defineProps({
  mensaje: { type: String, required: true },
  tipo: { type: String, default: 'exito' },
})

const emitir = defineEmits(['cerrar'])
</script>

<template>
  <div v-if="mensaje" class="notification" :class="`notification-${tipo}`" role="status">
    <span>{{ mensaje }}</span>
    <button type="button" aria-label="Cerrar mensaje" @click="emitir('cerrar')">×</button>
  </div>
</template>
