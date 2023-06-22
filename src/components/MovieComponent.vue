<script setup>
import { computed, defineProps, ref, watch } from "vue";

const props = defineProps({
  movie: Object,
});

const currentTime = ref(new Date("2023-06-22T09:00:00"));

const isTimeNear = computed(() => {
  const currentHour = currentTime.value.getHours();
  const currentMinute = currentTime.value.getMinutes();
  const [movieHour, movieMinute] = props.movie.horario.split(":").map(Number);
  const currentTimeInMinutes = currentHour * 60 + currentMinute;
  const movieTimeInMinutes = movieHour * 60 + movieMinute;

  return movieTimeInMinutes - currentTimeInMinutes <= 15;
});

const isSoldOut = computed(() => {
  return props.movie.sessao === "S";
});

const isPastTime = computed(() => {
  const currentHour = currentTime.value.getHours();
  const currentMinute = currentTime.value.getMinutes();
  const [movieHour, movieMinute] = props.movie.horario.split(":").map(Number);
  return (
    currentHour > movieHour ||
    (currentHour === movieHour && currentMinute > movieMinute)
  );
});

const isVip = computed(() => {
  return props.movie.vip === "S" ? true : false;
});

watch(currentTime, () => {
  if (!isPastTime.value) {
    // Atualizar o componente apenas se o horário do filme não tiver passado
    forceUpdate();
  }
});

function forceUpdate() {
  // Atualizar o componente forçadamente chamando o hook `render`
  const render = vm.render;
  vm.render = () => render();
}
</script>

<template>
  <div
    v-if="!isPastTime && !isSoldOut"
    :class="{
      'sold-out': isSoldOut,
      'not-sold-out': !isSoldOut,
      'green-background': isTimeNear,
    }"
  >
    <p class="horario">{{ movie.horario }}</p>
    <p class="legenda">{{ movie.legenda === "L" ? "LEG" : "DUB" }}</p>
  </div>
</template>

<style scoped>
.horario {
  margin: 0;
  font-size: 24px;
  font-weight: 800;
  color: #333;
}

.not-sold-out,
.sold-out {
  border: 1px solid #8f8e92;
  height: 64px;
  width: 120px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: #a0a1a4;
}

.green-background {
  background-color: green;
}

.sold-out {
  background: red;
}

.legenda {
  background-color: #aeadb0;
  font-size: 12px;
  margin: 0;
  color: #000;
  font-weight: 700;
  width: 48px;
  border-radius: 20px;
  box-shadow: inset 1px 1px 3px #000;
}
</style>
