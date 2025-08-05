<template>
  <main class="home-section" role="main">
    <ThreeScene />
    <!-- SEO Content for the band -->
    <header class="visually-hidden">
      <h1>Dinastía Moon - Banda Chilena de Música Psicodélica</h1>
      <p>Descubre la música experimental y psicodélica de Dinastía Moon, una banda chilena que fusiona sonidos únicos con melodías envolventes.</p>
    </header>
    <div class="home-bottom-edge" aria-hidden="true"></div>
  </main>
</template>

<script setup>
import ThreeScene from './ThreeScene.vue'
</script>

<style scoped>
.home-section {
  min-width: 100vw;
  min-height: 100vh;
  position: relative;
  overflow: hidden;
}

/* SEO content hidden visually but accessible to screen readers and search engines */
.visually-hidden {
  position: absolute;
  left: -10000px;
  top: auto;
  width: 1px;
  height: 1px;
  overflow: hidden;
}

.home-bottom-edge {
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 60px; /* ajusta la altura si lo necesitas */
  background-image: url('/separador.webp');
  background-repeat: repeat-x;
  background-size: auto 100%;
  z-index: 100;
  pointer-events: none;
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