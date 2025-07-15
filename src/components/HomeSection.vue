<template>
  <section class="home-section">
    <ThreeScene />

  </section>
</template>

<script setup>
import ThreeScene from './ThreeScene.vue'
</script>

<style scoped>
.home-section {
  width: 100vw;
  height: 100vh;
  position: relative;
  overflow: hidden;
}

@media (max-width: 768px) {
  .home-section {
    height: auto;
    min-height: 100vh;
  }
}

.home-content {
  position: absolute;
  bottom: 10%;
  left: 50%;
  transform: translateX(-50%);
  text-align: center;
  color: white;
  z-index: 50;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.7);
}

.home-content h1 {
  font-size: 3rem;
  margin-bottom: 1rem;
  font-weight: bold;
  opacity: 0.9;
}

.home-content p {
  font-size: 1.5rem;
  opacity: 0.8;
}
</style>