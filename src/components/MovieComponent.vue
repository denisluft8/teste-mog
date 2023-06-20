<script setup>
import { computed } from "vue";

const isSoldOut = computed(() => {
  return props.movie.sessao === "S" ? true : false;
});

const props = defineProps({
  movie: Object,
});
</script>

<template>
  <div :class="{ 'sold-out': isSoldOut, 'horario-container': !isSoldOut }">
    <p class="horario">{{ movie.horario }}</p>
    <p v-if="isSoldOut">ESGOTADA</p>
    <p v-else class="horario">{{ movie.legenda }}</p>
  </div>
</template>

<style scoped>
.horario {
  margin: 0;
}
.horario-container {
  border: 1px solid grey;
  height: 64px;
  width: 120px;
}

.sold-out {
  border: 1px solid grey;
  background: red;
  height: 64px;
  width: 120px;
}
</style>
