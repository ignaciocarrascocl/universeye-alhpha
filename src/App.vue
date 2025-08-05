<script setup>
import HomeSection from './components/HomeSection.vue'
import BandUI from './components/BandUI.vue'
import VideoSection from './components/VideoSection.vue'
import BandmateSection from './components/BandmateSection.vue'
import FloatingIcons from './components/FloatingIcons.vue'
</script>

<template>
  <HomeSection />
  <VideoSection />
  <BandmateSection />
  <BandUI />
  <FloatingIcons />
  <div class="copyright-bar">
    creado por 
    <a href="https://estudiodospuntos.cl/" target="_blank" rel="noopener" class="copyright-link">
      estudiodospuntos.cl
    </a>
  </div>
</template>

<style scoped>
/* No additional styles needed - just the particle background */
.copyright-bar {
  position: fixed;
  left: 50%;
  bottom: 10px;
  transform: translateX(-50%);
  background: rgba(0,0,0,0.85);
  color: #fff;
  font-size: 0.9rem;
  text-align: center;
  padding: 0.5rem 1.2rem;
  z-index: 2000;
  letter-spacing: 0.02em;
  font-family: inherit;
  border-radius: 1.2rem;
  box-shadow: 0 2px 12px rgba(0,0,0,0.18);
  width: auto;
  max-width: 90vw;
  display: inline-block;
}

.copyright-link {
  color: #ffd700;
  text-decoration: underline;
  margin-left: 0.3em;
  transition: color 0.2s;
}
.copyright-link:hover {
  color: #fff;
}
</style>
