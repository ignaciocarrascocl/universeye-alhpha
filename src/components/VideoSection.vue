<template>
  <section class="video-section">
    <div class="video-container" @click="openModal">
      <!-- Placeholder video local -->
      <video 
        ref="placeholderVideo"
        class="placeholder-video" 
        muted
        loop
        playsinline
   >
        <source src="/placeholder.mp4" type="video/mp4">
      </video>
      
      <!-- Overlay de play -->
      <div class="play-overlay">
        <svg class="play-icon" viewBox="0 0 24 24" fill="white">
          <path d="M8 5v14l11-7z"/>
        </svg>
      </div>
    </div>
    
    <!-- Modal de YouTube -->
    <div class="video-modal" v-if="showModal" @click="closeModal">
      <div class="modal-content" @click.stop>
        <button class="close-button" @click="closeModal">&times;</button>
        <div class="youtube-container">
          <iframe 
            width="560" 
            height="315" 
            :src="youtubeUrl" 
            title="YouTube video player" 
            frameborder="0" 
            allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
            allowfullscreen>
          </iframe>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const placeholderVideo = ref(null);
const showModal = ref(false);
const youtubeUrl = ref('');

// URL de YouTube con autoplay activado cuando se abre el modal
const youtubeEmbedUrl = 'https://www.youtube.com/embed/NuJ5FoNeBBE?autoplay=1';

onMounted(() => {
  // Iniciar reproducción del video placeholder
  if (placeholderVideo.value) {
    placeholderVideo.value.play().catch(err => {
      console.log('Reproducción automática no permitida:', err);
    });
  }
});

// Función para abrir el modal
const openModal = () => {
  youtubeUrl.value = youtubeEmbedUrl;
  showModal.value = true;
  // Prevenir scroll del body
  document.body.style.overflow = 'hidden';
};

// Función para cerrar el modal
const closeModal = () => {
  showModal.value = false;
  youtubeUrl.value = '';
  // Restaurar scroll del body
  document.body.style.overflow = '';
};
</script>

<style scoped>
.video-section {
  display: flex;
  align-items: center;
  justify-content: center;
  background: black;
  position: relative;
  padding: 2rem;
}

.video-container {
  position: relative;
  width: 90%;
  max-width: 1200px;
  aspect-ratio: 16/9;
  border-radius: 0.5rem;
  overflow: hidden;
  box-shadow: 0 8px 32px rgba(255, 255, 255, 0.1);
  cursor: pointer;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.video-container:hover {
  transform: scale(1.02);
  box-shadow: 0 12px 48px rgba(255, 255, 255, 0.2);
}

.placeholder-video {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.play-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(0, 0, 0, 0.3);
  transition: background-color 0.3s ease;
}

.play-overlay:hover {
  background-color: rgba(0, 0, 0, 0.5);
}

.play-icon {
  width: 80px;
  height: 80px;
  filter: drop-shadow(0 0 8px rgba(0, 0, 0, 0.5));
  transition: transform 0.3s ease, filter 0.3s ease;
}

.play-overlay:hover .play-icon {
  transform: scale(1.1);
  filter: drop-shadow(0 0 15px rgba(255, 255, 255, 0.8));
}

/* Estilos para el modal */
.video-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  position: relative;
  width: 90%;
  max-width: 1200px;
  aspect-ratio: 16/9;
  background-color: black;
  border-radius: 8px;
  overflow: hidden;
}

.close-button {
  position: absolute;
  top: -40px;
  right: 0;
  width: 40px;
  height: 40px;
  background: none;
  border: none;
  color: white;
  font-size: 28px;
  cursor: pointer;
  z-index: 10;
}

.youtube-container {
  width: 100%;
  height: 100%;
}

.youtube-container iframe {
  width: 100%;
  height: 100%;
  border: none;
}

@media (max-width: 768px) {
  .video-section {
    height: auto;
    padding: 1rem;
  }
  
  .video-container {
    width: 95%;
    max-width: 100%;
  }
  
  .play-icon {
    width: 60px;
    height: 60px;
  }
  
  .close-button {
    top: -30px;
    width: 30px;
    height: 30px;
    font-size: 24px;
  }
}
</style>