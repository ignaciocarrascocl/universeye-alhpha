<template>
  <section class="bandmate-section">
    <video autoplay muted loop class="background-video">
      <source src="/Looping_Animation_Generation_Complete.mp4" type="video/mp4">
    </video>
    
    <div class="content-wrapper">
      <!-- Buttons above the content box -->
      <div class="buttons-container">
        <a href="#" class="nav-button">
          <img src="/boton_merch.webp" alt="Merch" loading="eager" />
        </a>
        <a href="#" class="nav-button">
          <img src="/boton_musica.webp" alt="Música" loading="eager" />
        </a>
        <a href="#" class="nav-button">
          <img src="/boton_tour.webp" alt="Tour" loading="eager" />
        </a>
        <a href="#" class="nav-button">
          <img src="/boton_videos.webp" alt="Videos" loading="eager" />
        </a>
      </div>
      
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

.content-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  z-index: 2;
  position: relative;
  margin: 0 2rem;
}

.buttons-container {
  display: flex;
  justify-content: center;
  gap: 2rem;
  margin-bottom: 2rem;
}

.nav-button {
  display: block;
  transition: transform 0.3s ease, filter 0.3s ease;
  text-decoration: none;
}

.nav-button:hover {
  transform: scale(1.1);
  filter: brightness(1.2);
}

.nav-button img {
  width: 120px;
  height: 60px;
  object-fit: cover;
  filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.3));
}

.content-box {
  background: white;
  color: #333;
  padding: 3rem;
  border-radius: 1rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
  text-align: center;
  max-width: 1200px;
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

@media (max-width: 768px) {
  .bandmate-section {
    height: auto;
    min-height: 100vh;
    padding: 2rem 0;
  }

  .buttons-container {
    gap: 1rem;
  }
  
  .nav-button img {
    width: 80px;
    height: 40px;
    object-fit: cover;
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

@media (max-width: 480px) {
  .buttons-container {
    flex-wrap: wrap;
    gap: 0.8rem;
  }
  
  .nav-button img {
    width: 70px;
    height: 35px;
    object-fit: cover;
  }
}
</style>