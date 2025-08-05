<template>
  <aside class="band-ui" role="complementary" aria-label="Redes sociales de Dinastía Moon">
    <nav class="social-icons" role="navigation" aria-label="Enlaces a redes sociales">
      <a href="https://instagram.com/dinastiamoon" target="_blank" rel="noopener noreferrer" class="social-icon" aria-label="Seguir a Dinastía Moon en Instagram">
        <vue-feather type="instagram" size="24" aria-hidden="true" />
        <span class="visually-hidden">Instagram</span>
      </a>
      <a href="https://dinastiamoon.bandcamp.com" target="_blank" rel="noopener noreferrer" class="social-icon" aria-label="Escuchar música de Dinastía Moon en Bandcamp">
        <vue-feather type="music" size="24" aria-hidden="true" />
        <span class="visually-hidden">Bandcamp</span>
      </a>
      <a href="https://facebook.com/dinastiamoon" target="_blank" rel="noopener noreferrer" class="social-icon" aria-label="Seguir a Dinastía Moon en Facebook">
        <vue-feather type="facebook" size="24" aria-hidden="true" />
        <span class="visually-hidden">Facebook</span>
      </a>
      <a href="https://youtube.com/@dinastiamoon" target="_blank" rel="noopener noreferrer" class="social-icon" aria-label="Ver videos de Dinastía Moon en YouTube">
        <vue-feather type="youtube" size="24" aria-hidden="true" />
        <span class="visually-hidden">YouTube</span>
      </a>
    </nav>
  </aside>
</template>

<script setup>
import VueFeather from 'vue-feather'
</script>

<style scoped>
/* SEO content hidden visually but accessible to screen readers and search engines */
.visually-hidden {
  position: absolute;
  left: -10000px;
  top: auto;
  width: 1px;
  height: 1px;
  overflow: hidden;
}

.band-ui {
  position: fixed;
  top: 50%;
  left: 0;
  transform: translateY(-50%);
  z-index: 1000;
}

.social-icons {
  display: flex;
  flex-direction: column;
  gap: 20px;
  align-items: center;
}

.social-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 50px;
  height: 50px;
  background: rgba(0, 0, 0, 0.7);
  color: white;
  text-decoration: none;
  transition: all 0.6s ease;
  animation: float 3s ease-in-out infinite;
  backdrop-filter: blur(8px);
  border-radius: 50px;
}

.social-icon:nth-child(1) {
  animation-delay: 0s;
}

.social-icon:nth-child(2) {
  animation-delay: 0.7s;
}

.social-icon:nth-child(3) {
  animation-delay: 1.4s;
}

.social-icon:nth-child(4) {
  animation-delay: 2.1s;
}

.social-icon:hover {
  background: rgba(0, 0, 0, 0.9);
  transform: translateY(-8px) scale(1.1);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.4);
}

@keyframes float {
  0%, 100% {
    transform: translateY(0px);
  }
  50% {
    transform: translateY(-10px);
  }
}
</style>
