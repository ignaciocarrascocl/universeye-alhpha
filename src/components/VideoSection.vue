<template>
  <section class="video-section">
    <div class="video-container">
      <iframe 
        width="560" 
        height="315" 
        src="https://www.youtube.com/embed/NuJ5FoNeBBE" 
        title="YouTube video player" 
        frameborder="0" 
        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
        allowfullscreen>
      </iframe>
    </div>
  </section>
</template>

<script setup>
// Video section component
</script>

<style scoped>
.video-section {
  width: 100vw;
  height: 100vh;
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
}

.video-container iframe {
  width: 100%;
  height: 100%;
  border: none;
}

@media (max-width: 768px) {
  .video-section {
    height: auto;
    min-height: 100vh;
    padding: 1rem;
  }
  
  .video-container {
    width: 95%;
    max-width: 100%;
  }
}
</style>