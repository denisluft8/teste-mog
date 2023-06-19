<template>
  <div>
    <h2>{{ movie.nome }}</h2>
    <p> {{ movie.horario }}</p>
    <!-- Outras informações do filme... -->
  </div>
</template>

<script>
export default {
  props: ["movie"],
};
</script>

<style scoped></style>
