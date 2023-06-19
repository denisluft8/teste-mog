<template>
  <div id="horario-container">
    <p id="horario">{{ movie.horario }}</p>
    <p id="horario">{{ movie.legenda }}</p>
  </div>
</template>

<script>
export default {
  props: ["movie"],
};
</script>

<style scoped>
#horario {
  margin: 0;
}
#horario-container {
  border: 1px solid grey;
  height: 64px;
  width: 120px;
}
</style>
