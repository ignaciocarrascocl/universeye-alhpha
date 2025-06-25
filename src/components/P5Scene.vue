<template>
    <div ref="p5Container" class="p5-container"></div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import p5 from 'p5'

const p5Container = ref(null)
let p5Instance = null

onMounted(() => {
    // Create p5 sketch
    const sketch = (p) => {
        p.setup = () => {
            // Create canvas that fills the window
            p.createCanvas(p.windowWidth, p.windowHeight)
        }

        p.draw = () => {
            // Simple gradient background
            for (let i = 0; i <= p.height; i++) {
                let inter = p.map(i, 0, p.height, 0, 1)
                let c = p.lerpColor(p.color(20, 20, 40), p.color(120, 60, 180), inter)
                p.stroke(c)
                p.line(0, i, p.width, i)
            }
        }

        p.windowResized = () => {
            p.resizeCanvas(p.windowWidth, p.windowHeight)
        }
    }

    // Initialize p5 instance
    p5Instance = new p5(sketch, p5Container.value)
})

onUnmounted(() => {
    if (p5Instance) {
        p5Instance.remove()
    }
})
</script>

<style scoped>
.p5-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 1;
}
</style>
