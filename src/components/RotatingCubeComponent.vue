<script setup>
import { defineProps } from "vue";

const props = defineProps({
  movieType: String,
});
</script>

<template>
  <div class="cube">
    <div class="face front">
      <p>XD</p>
    </div>
    <div class="face back">
      <p v-if="movieType === 'X'">2D</p>
      <p v-else>3D</p>
    </div>
  </div>
</template>

<style scoped>
.cube {
  width: 60px;
  height: 60px;
  position: relative;
  transform-style: preserve-3d;
  animation: rotateCube 4s infinite linear;
}

.face {
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  color: #fff;
  background-image: linear-gradient(to bottom right, #4d4a47, #676363);
  backface-visibility: hidden;
  font-weight: 600;
  font-size: 32px;
  color: #e6e4e5;
  text-shadow: 1px 1px 10px #aaa;
}

.front {
  transform: translateZ(0px);
}

.back {
  transform: rotateY(180deg) translateZ(0px);
}

@keyframes rotateCube {
  0% {
    transform: rotateY(0);
  }
  25% {
    transform: rotateY(0);
  }
  50% {
    transform: rotateY(180deg);
  }
  75% {
    transform: rotateY(180deg);
  }
  100% {
    transform: rotateY(360deg);
  }
}
</style>
