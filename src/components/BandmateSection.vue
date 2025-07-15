<template>
  <section class="bandmate-section">
    <video autoplay muted loop class="background-video">
      <source src="/Looping_Animation_Generation_Complete.mp4" type="video/mp4">
    </video>
    <div class="content-box">
      <div class="iframe-grid">
        <iframe 
          style="border: 0; width: 350px; height: 350px;" 
          src="https://bandcamp.com/EmbeddedPlayer/track=979445113/size=large/bgcol=333333/linkcol=ffffff/minimal=true/transparent=true/" 
          seamless>
          <a href="https://dinastiamoon.bandcamp.com/track/spaghetti-western-3">SPAGHETTI WESTERN de Dinastía Moon</a>
        </iframe>
        
        <iframe 
          style="border: 0; width: 350px; height: 350px;" 
          src="https://bandcamp.com/EmbeddedPlayer/track=2103735496/size=large/bgcol=333333/linkcol=ffffff/minimal=true/transparent=true/" 
          seamless>
          <a href="https://dinastiamoon.bandcamp.com/track/isla-friendship">ISLA FRIENDSHIP de Dinastía Moon</a>
        </iframe>
        
        <iframe 
          style="border: 0; width: 350px; height: 350px;" 
          src="https://bandcamp.com/EmbeddedPlayer/track=4275410357/size=large/bgcol=333333/linkcol=ffffff/minimal=true/transparent=true/" 
          seamless>
          <a href="https://dinastiamoon.bandcamp.com/track/kepler">KEPLER de Dinastía Moon</a>
        </iframe>
        
        <iframe 
          style="border: 0; width: 350px; height: 350px;" 
          src="https://bandcamp.com/EmbeddedPlayer/track=1110235795/size=large/bgcol=333333/linkcol=ffffff/minimal=true/transparent=true/" 
          seamless>
          <a href="https://dinastiamoon.bandcamp.com/track/cosmonauta">COSMONAUTA de Dinastía Moon</a>
        </iframe>
        
        <iframe 
          style="border: 0; width: 350px; height: 350px;" 
          src="https://bandcamp.com/EmbeddedPlayer/album=2788105991/size=large/bgcol=333333/linkcol=ffffff/minimal=true/transparent=true/" 
          seamless>
          <a href="https://dinastiamoon.bandcamp.com/album/coscoroba-ep">COSCOROBA EP de Dinastía Moon</a>
        </iframe>
        
        <iframe 
          style="border: 0; width: 350px; height: 350px;" 
          src="https://bandcamp.com/EmbeddedPlayer/track=1940190945/size=large/bgcol=333333/linkcol=ffffff/minimal=true/transparent=true/" 
          seamless>
          <a href="https://dinastiamoon.bandcamp.com/track/desierto-paralelo-33-2">DESIERTO PARALELO 33 de Dinastía Moon</a>
        </iframe>
        
        <iframe 
          style="border: 0; width: 350px; height: 350px;" 
          src="https://bandcamp.com/EmbeddedPlayer/track=1627307893/size=large/bgcol=333333/linkcol=ffffff/minimal=true/transparent=true/" 
          seamless>
          <a href="https://dinastiamoon.bandcamp.com/track/mycelium-3">MYCELIUM de Dinastía Moon</a>
        </iframe>
      </div>
    </div>
  </section>
</template>

<script setup>
// Bandmate section component
</script>

<style scoped>
.bandmate-section {
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
}

.background-video {
  position: absolute;
  top: 50%;
  left: 50%;
  min-width: 110%;
  min-height: 110%;
  width: auto;
  height: auto;
  transform: translate(-50%, -50%) scale(1.1);
  object-fit: cover;
  z-index: 1;
  filter: blur(2px);
}

.content-box {
  background: white;
  color: #333;
  padding: 3rem;
  border-radius: 1rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
  text-align: center;
  z-index: 2;
  position: relative;
  max-width: 1200px;
  margin: 0 2rem;
}

.content-box h1 {
  font-size: 3rem;
  margin-bottom: 1rem;
  font-weight: bold;
  display: none;
}

.content-box p {
  font-size: 1.5rem;
  opacity: 0.8;
  margin-bottom: 2rem;
  display: none;
}

.iframe-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.5rem;
  justify-items: center;
  max-width: 1100px;
  margin: 0 auto;
}

.iframe-grid iframe {
  width: 300px !important;
  height: 300px !important;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

@media (max-width: 1024px) {
  .iframe-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 640px) {
  .iframe-grid {
    grid-template-columns: 1fr;
  }
  
  .iframe-grid iframe {
    width: 280px !important;
    height: 280px !important;
  }
}
</style>