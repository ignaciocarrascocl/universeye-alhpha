<template>
  <div class="band-ui">
    <div class="social-icons">
      <a href="https://instagram.com/dinastiamoon" target="_blank" class="social-icon">
        <vue-feather type="instagram" size="24" />
      </a>
      <a href="https://dinastiamoon.bandcamp.com" target="_blank" class="social-icon">
        <vue-feather type="music" size="24" />
      </a>
      <a href="https://facebook.com/dinastiamoon" target="_blank" class="social-icon">
        <vue-feather type="facebook" size="24" />
      </a>
      <a href="https://youtube.com/@dinastiamoon" target="_blank" class="social-icon">
        <vue-feather type="youtube" size="24" />
      </a>
    </div>
  </div>
</template>

<script setup>
import VueFeather from 'vue-feather'
</script>

<style scoped>
.band-ui {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1000;
}

.social-icons {
  display: flex;
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
