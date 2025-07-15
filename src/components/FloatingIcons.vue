<template>
  <div class="floating-icons">
    <div 
      v-for="(icon, index) in icons" 
      :key="index"
      class="floating-icon"
      :class="[icon.position, { active: isActivePreset(icon.preset) }]"
      :style="{ transform: `translate(${mouseOffset.x * icon.factor}px, ${mouseOffset.y * icon.factor}px)` }"
      @click="handleIconClick(index)"
      :title="icon.presetName"
    >
      <img :src="icon.src" :alt="icon.alt" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const mouseOffset = ref({ x: 0, y: 0 })

const icons = [
  { 
    src: '/icono_1.webp', 
    alt: 'Icon 1', 
    position: 'top-left',
    factor: 0.1,
    preset: 'cosmic',
    presetName: 'Cosmic'
  },
  { 
    src: '/icono_2.webp', 
    alt: 'Icon 2', 
    position: 'top-right',
    factor: -0.1,
    preset: 'minimal',
    presetName: 'Minimal'
  },
  { 
    src: '/icono_3.webp', 
    alt: 'Icon 3', 
    position: 'bottom-left',
    factor: -0.1,
    preset: 'psychedelic',
    presetName: 'Psychedelic'
  },
  { 
    src: '/icono_4.webp', 
    alt: 'Icon 4', 
    position: 'bottom-right',
    factor: 0.1,
    preset: 'wave',
    presetName: 'Wave'
  }
]

const handleMouseMove = (event) => {
  const centerX = window.innerWidth / 2
  const centerY = window.innerHeight / 2
  
  mouseOffset.value = {
    x: (event.clientX - centerX) * 0.1,
    y: (event.clientY - centerY) * 0.1
  }
}

const handleIconClick = (index) => {
  const icon = icons[index]
  console.log(`Icon ${index + 1} clicked - Applying preset: ${icon.preset}`)
  
  // Apply the preset using the global ThreeScene function
  if (window.ThreeScenePresets && window.ThreeScenePresets.applyPreset) {
    window.ThreeScenePresets.applyPreset(icon.preset)
  }
}

const isActivePreset = (preset) => {
  if (window.ThreeScenePresets && window.ThreeScenePresets.currentPreset) {
    return window.ThreeScenePresets.currentPreset() === preset
  }
  return false
}

onMounted(() => {
  window.addEventListener('mousemove', handleMouseMove)
})

onUnmounted(() => {
  window.removeEventListener('mousemove', handleMouseMove)
})
</script>

<style scoped>
.floating-icons {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  pointer-events: none;
  z-index: 1000;
}

.floating-icon {
  position: absolute;
  width: 60px;
  height: 60px;
  pointer-events: all;
  transition: transform 0.1s ease-out, opacity 0.3s ease, filter 0.3s ease;
  cursor: pointer;
  opacity: 0.8;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.floating-icon:hover {
  opacity: 1;
  transform: scale(1.2);
}

.floating-icon.active {
  opacity: 1;
  filter: drop-shadow(0 0 15px rgba(255, 255, 255, 0.8));
}

.floating-icon img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.3));
}

.floating-icon.active img {
  filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.3)) drop-shadow(0 0 10px rgba(255, 255, 255, 0.6));
}

.top-left {
  top: 20px;
  left: 20px;
}

.top-right {
  top: 20px;
  right: 20px;
}

.bottom-left {
  bottom: 20px;
  left: 20px;
}

.bottom-right {
  bottom: 20px;
  right: 20px;
}
</style>