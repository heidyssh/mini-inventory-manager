<script setup>
defineProps({
  busqueda: { type: String, required: true },
  categoria: { type: String, required: true },
})

const emitir = defineEmits(['update:busqueda', 'update:categoria'])
const categorias = ['Tecnología', 'Hogar', 'Oficina', 'Otros']
</script>

<template>
  <div class="filters">
    <div class="field search-field">
      <label for="search">Buscar por nombre</label>
      <input
        id="search"
        :value="busqueda"
        type="search"
        placeholder="Escriba un nombre..."
        @input="emitir('update:busqueda', $event.target.value)"
      />
    </div>
    <div class="field category-filter">
      <label for="filter-category">Filtrar por categoría</label>
      <select id="filter-category" :value="categoria" @change="emitir('update:categoria', $event.target.value)">
        <option value="">Todas</option>
        <option v-for="elemento in categorias" :key="elemento" :value="elemento">{{ elemento }}</option>
      </select>
    </div>
  </div>
</template>
