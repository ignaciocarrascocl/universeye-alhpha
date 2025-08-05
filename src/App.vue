<script setup>
import HomeSection from './components/HomeSection.vue'
import BandUI from './components/BandUI.vue'
import VideoSection from './components/VideoSection.vue'
import BandmateSection from './components/BandmateSection.vue'
import FloatingIcons from './components/FloatingIcons.vue'
</script>

<template>
  <!-- Hidden SEO content about the band -->
  <section class="seo-content" aria-hidden="true">
    <h1>Dinastía Moon - Banda Chilena de Música Psicodélica</h1>
    <p>Dinastía Moon es una innovadora banda chilena especializada en música psicodélica y experimental. Fusionando sonidos únicos con melodías envolventes, la banda ha creado un estilo distintivo que transporta a los oyentes a paisajes sonoros únicos.</p>
    <p>Con canciones como "Spaghetti Western", "Isla Friendship", "Kepler", "Cosmonauta" y "Mycelium", Dinastía Moon explora territorios musicales inexplorados, combinando elementos del rock psicodélico con experimentación sonora contemporánea.</p>
    <p>La banda está disponible en plataformas como Bandcamp, YouTube, Instagram y Facebook, donde comparten su música y videos únicos con una creciente base de fanáticos.</p>
  </section>
  
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
/* Hidden SEO content for search engines */
.seo-content {
  position: absolute;
  left: -10000px;
  top: 0;
  width: 1px;
  height: 1px;
  overflow: hidden;
}

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
  display: inline-block;
  white-space: nowrap;
}


.copyright-link {
  color: #ffd700;
  text-decoration: underline;
  margin-left: 0.3em;
  transition: color 0.2s;
  display: inline;
}
.copyright-link:hover {
  color: #fff;
}
</style>
