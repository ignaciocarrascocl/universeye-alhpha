<template>
    <div ref="sceneContainer" class="scene-container">
        <!-- WebGL Background Canvas -->
        <canvas ref="glCanvas" id="glcanvas" class="webgl-background"></canvas>

        <!-- Hidden base eye template -->
        <svg id="base-eye" class="eye" viewBox="0 0 1000 1000" xmlns="http://www.w3.org/2000/svg" fill="black"
            style="display: none;">
            <clipPath id="lids">
                <path id="lids-path" stroke-linejoin="round" stroke-linecap="round"
                    d="M 50 500 Q 500 0 950 500 Q 500 850 50 500" />
            </clipPath>
            <g clip-path="url(#lids)">
                <rect class="whites" width="1000" height="1000" fill="#fff" />
                <g class="pupil-group">
                    <circle class="pupil" cx="500" cy="500" r="150" fill="#000" />
                    <circle class="glint" cx="450" cy="450" r="20" fill="#fff" />
                </g>
            </g>
            <use href="#lids-path" class="lids" stroke="#000" stroke-width="20" />
        </svg>

        <!-- Single eye in center -->
        <div class="eye-container">
            <svg ref="mainEye" class="eye main-eye" viewBox="0 0 1000 1000" xmlns="http://www.w3.org/2000/svg"
                fill="black">
                <clipPath id="main-lids">
                    <path id="main-lids-path" stroke-linejoin="round" stroke-linecap="round"
                        d="M 50 500 Q 500 0 950 500 Q 500 850 50 500" />
                </clipPath>
                <g clip-path="url(#main-lids)">
                    <rect class="whites" width="1000" height="1000" fill="#fff" />
                    <g class="pupil-group">
                        <circle class="pupil" cx="500" cy="500" r="150" fill="#000" />
                        <circle class="glint" cx="450" cy="450" r="20" fill="#fff" />
                    </g>
                </g>
                <use href="#main-lids-path" class="lids" stroke="#000" stroke-width="20" />
            </svg>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { GUI } from 'lil-gui'

const sceneContainer = ref(null)
const mainEye = ref(null)
const glCanvas = ref(null)

let eyeCenter
const mousePos = { x: 0, y: 0 }
const maxEyeTravelX = 275
const maxEyeTravelY = 100
let maxDist

// WebGL variables
let gl, program, start
let timeLoc, resLoc, posLoc, quadVBO, interactionPointsLoc, numInteractionsLoc
let numWavesLoc, waveAmplitudeLoc, waveSpeedLoc, radialFreqLoc, mixFactorLoc, combinedMultLoc, grainIntensityLoc

// Interaction tracking
const MAX_INTERACTIONS = 10
const interactionPoints = []
let interactionData = new Float32Array(MAX_INTERACTIONS * 4) // x, y, intensity, time

// GUI controls object
const guiControls = {
    numWaves: 30.0,
    waveAmplitude: 0.1,
    waveSpeed: 6.0,
    radialFreq: 20.0,
    mixFactor: 0.3,
    combinedMult: 4.0,
    grainIntensity: 0.25
}

let gui = null

// WebGL shader sources
const vertexSrc = `
    attribute vec2 a_position;
    varying vec2 v_uv;
    void main() {
      v_uv = a_position * 0.5 + 0.5;
      gl_Position = vec4(a_position, 0.0, 1.0);
    }`

// Fragment shader with colorful background and black line overlay
const fragmentSrc = `
    precision highp float;
    uniform float u_time;
    uniform vec2  u_resolution;
    uniform vec4  u_interactions[${MAX_INTERACTIONS}]; // x, y, intensity, time
    uniform int   u_numInteractions;
    uniform float u_numWaves;
    uniform float u_waveAmplitude;
    uniform float u_waveSpeed;
    uniform float u_radialFreq;
    uniform float u_mixFactor;
    uniform float u_combinedMult;
    uniform float u_grainIntensity;
    varying vec2  v_uv;
    #define PI 3.141592653589793

    // Hash & noise
    float hash(vec2 p) {
      p = fract(p * vec2(123.34, 456.21));
      p += dot(p, p + 78.233);
      return fract(p.x * p.y);
    }
    float noise(vec2 p) {
      vec2 i = floor(p), f = fract(p);
      float a = hash(i), b = hash(i + vec2(1.0, 0.0));
      float c = hash(i + vec2(0.0, 1.0)), d = hash(i + vec2(1.0, 1.0));
      vec2 u = f * f * (3.0 - 2.0 * f);
      return mix(a, b, u.x) +
             (c - a) * u.y * (1.0 - u.x) +
             (d - b) * u.x * u.y;
    }
    // FBM with 8 octaves
    float fbm(vec2 p) {
      float v = 0.0, amp = 0.5;
      for(int i = 0; i < 8; i++){
        v += amp * noise(p);
        p *= 2.0;
        amp *= 0.5;
      }
      return v;
    }

    // Inigo-style palette for colors
    vec3 palette(float t) {
      vec3 a = vec3(0.5);
      vec3 b = vec3(0.5);
      vec3 c = vec3(1.0);
      vec3 d = vec3(0.00, 0.33, 0.67);
      return a + b * cos(2.0 * PI * (c * t + d));
    }

    // Convert RGB to HSV
    vec3 rgb2hsv(vec3 c) {
      vec4 K = vec4(0.0, -1.0 / 3.0, 2.0 / 3.0, -1.0);
      vec4 p = mix(vec4(c.bg, K.wz), vec4(c.gb, K.xy), step(c.b, c.g));
      vec4 q = mix(vec4(p.xyw, c.r), vec4(c.r, p.yzx), step(p.x, c.r));
      float d = q.x - min(q.w, q.y);
      float e = 1.0e-10;
      return vec3(abs(q.z + (q.w - q.y) / (6.0 * d + e)), d / (q.x + e), q.x);
    }

    // Convert HSV to RGB
    vec3 hsv2rgb(vec3 c) {
      vec4 K = vec4(1.0, 2.0 / 3.0, 1.0 / 3.0, 3.0);
      vec3 p = abs(fract(c.xxx + K.xyz) * 6.0 - K.www);
      return c.z * mix(K.xxx, clamp(p - K.xxx, 0.0, 1.0), c.y);
    }

    // Smooth falloff function
    float smoothFalloff(float dist, float radius) {
      return 1.0 - smoothstep(0.0, radius, dist);
    }

    void main(){
      // Normalized UV & correct aspect
      vec2 uv = v_uv;
      vec2 p = uv - 0.5;
      p.x *= u_resolution.x / u_resolution.y;

      // Dynamic kaleidoscope
      float segs = mix(3.0, 12.0, sin(u_time * 0.12) * 0.5 + 0.5);
      float ang = atan(p.y, p.x);
      float rad = length(p);
      ang = mod(ang, 2.0 * PI / segs) - (PI / segs);
      p = rad * vec2(cos(ang), sin(ang));

      // Multi-layered swirl
      vec2 q = p;
      for(int i = 0; i < 3; i++){
        float a = fbm(q * (3.0 + float(i)) + u_time * (0.15 * float(i+1))) * 6.2831;
        q = mat2(cos(a), -sin(a), sin(a), cos(a)) * q;
      }

      // Radial FBM distortion
      float distortion = fbm(q * 5.0 + u_time * 0.3);
      vec2 finalUv = uv + q * distortion * 0.45;

      // Base pattern + time warp
      float T = u_time * 0.2;
      float n = fbm(finalUv * 4.0 + T);

      // Create colorful background using palette
      vec3 col = palette(n + T);

      // Apply interaction effects using original UV coordinates (before distortion)
      if (u_numInteractions > 0) {
        vec3 hsv = rgb2hsv(col);
        float totalHueShift = 0.0;
        float totalSatBoost = 0.0;
        float totalWeight = 0.0;

        // Process each interaction point
        for (int i = 0; i < ${MAX_INTERACTIONS}; i++) {
          if (i >= u_numInteractions) break;
          
          vec4 interaction = u_interactions[i];
          vec2 interactionPos = interaction.xy;
          float intensity = interaction.z;
          float time = interaction.w;
          
          // Convert interaction position to UV coordinates
          vec2 interactionUV = interactionPos / u_resolution;
          // Flip Y coordinate to match screen coordinates
          interactionUV.y = 1.0 - interactionUV.y;
          
          // Calculate distance from current pixel to interaction point using original UV
          vec2 screenUV = v_uv;
          vec2 diff = screenUV - interactionUV;
          diff.x *= u_resolution.x / u_resolution.y; // Correct aspect ratio
          float dist = length(diff);
          
          // Define interaction radius (normalized)
          float radius = 0.2;
          
          // Calculate falloff based on distance and time
          float falloff = smoothFalloff(dist, radius);
          float timeFade = exp(-time * 2.0); // Exponential fade over time
          
          float weight = falloff * intensity * timeFade;
          
          if (weight > 0.01) {
            // Create unique hue shift based on interaction position
            float hueShift = sin(interactionPos.x * 0.01 + interactionPos.y * 0.01 + time * 3.0) * 0.3;
            float saturationBoost = 0.4;
            
            totalHueShift += hueShift * weight;
            totalSatBoost += saturationBoost * weight;
            totalWeight += weight;
          }
        }
        
        // Apply accumulated effects
        if (totalWeight > 0.0) {
          hsv.x = fract(hsv.x + totalHueShift);
          hsv.y = clamp(hsv.y + totalSatBoost, 0.0, 1.0);
          col = hsv2rgb(hsv);
        }
      }

      // Optical illusion swirling concentric lines (for black overlay)
      vec2 center = vec2(0.5);
      vec2 spiralUv = v_uv - center;
      
      // Adjust aspect ratio to prevent distortion
      spiralUv.x *= u_resolution.x / u_resolution.y;
      
      // Calculate polar coordinates: angle and radius
      float angle = atan(spiralUv.y, spiralUv.x); // Angle from the positive x-axis
      float radius = length(spiralUv);            // Distance from the center

      // Define parameters for the wavy pattern (now controlled by GUI)
      float num_waves = u_numWaves;           // GUI controlled number of waves
      float wave_amplitude = u_waveAmplitude; // GUI controlled wave amplitude
      float wave_speed = u_waveSpeed;         // GUI controlled animation speed

      // Create the wavy pattern based on angle and radius
      float wave_pattern = sin(angle * num_waves + radius * 60.0 + u_time * wave_speed);

      // Add a radial component to enhance the tunnel effect (GUI controlled frequency)
      float radial_pattern = sin(radius * u_radialFreq + u_time * wave_speed * 0.5);

      // Combine the wave and radial patterns (GUI controlled mix factor)
      float combined_pattern = mix(wave_pattern, radial_pattern, u_mixFactor);

      // Map the combined pattern to black lines (GUI controlled multiplier)
      float allSpirals = step(0.0, sin(combined_pattern * u_combinedMult));
      
      // Apply spiral lines as fully black overlay on top of colorful background
      col = mix(col, vec3(0.0), allSpirals);

      // Heavy grain (GUI controlled intensity)
      float g = (noise(finalUv * u_resolution * 0.5 + u_time) - 0.5) * u_grainIntensity;
      col += g;

      gl_FragColor = vec4(col, 1.0);
    }`

// WebGL helper functions
function compileShader(src, type) {
    const shader = gl.createShader(type)
    gl.shaderSource(shader, src)
    gl.compileShader(shader)
    if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
        console.error("Shader compile error:", gl.getShaderInfoLog(shader))
        gl.deleteShader(shader)
        return null
    }
    return shader
}

function createProgram(vsSrc, fsSrc) {
    const vs = compileShader(vsSrc, gl.VERTEX_SHADER)
    const fs = compileShader(fsSrc, gl.FRAGMENT_SHADER)
    const prog = gl.createProgram()
    gl.attachShader(prog, vs)
    gl.attachShader(prog, fs)
    gl.linkProgram(prog)
    if (!gl.getProgramParameter(prog, gl.LINK_STATUS)) {
        console.error("Program link error:", gl.getProgramInfoLog(prog))
        return null
    }
    return prog
}

function initWebGL() {
    const canvas = glCanvas.value
    gl = canvas.getContext("webgl")
    if (!gl) {
        console.error("WebGL not supported")
        return false
    }

    function resize() {
        canvas.width = window.innerWidth
        canvas.height = window.innerHeight
        gl.viewport(0, 0, canvas.width, canvas.height)
    }
    window.addEventListener("resize", resize)
    resize()

    // Full-screen quad setup
    const quadVerts = new Float32Array([-1, -1, 1, -1, -1, 1, -1, 1, 1, -1, 1, 1])
    quadVBO = gl.createBuffer()
    gl.bindBuffer(gl.ARRAY_BUFFER, quadVBO)
    gl.bufferData(gl.ARRAY_BUFFER, quadVerts, gl.STATIC_DRAW)

    // Compile & Link Program
    program = createProgram(vertexSrc, fragmentSrc)
    if (!program) return false

    gl.useProgram(program)

    // Bind Quad & Attributes
    posLoc = gl.getAttribLocation(program, "a_position")
    gl.enableVertexAttribArray(posLoc)
    gl.bindBuffer(gl.ARRAY_BUFFER, quadVBO)
    gl.vertexAttribPointer(posLoc, 2, gl.FLOAT, false, 0, 0)

    // Uniform Locations
    timeLoc = gl.getUniformLocation(program, "u_time")
    resLoc = gl.getUniformLocation(program, "u_resolution")
    interactionPointsLoc = gl.getUniformLocation(program, "u_interactions")
    numInteractionsLoc = gl.getUniformLocation(program, "u_numInteractions")
    
    // GUI-controlled uniform locations
    numWavesLoc = gl.getUniformLocation(program, "u_numWaves")
    waveAmplitudeLoc = gl.getUniformLocation(program, "u_waveAmplitude")
    waveSpeedLoc = gl.getUniformLocation(program, "u_waveSpeed")
    radialFreqLoc = gl.getUniformLocation(program, "u_radialFreq")
    mixFactorLoc = gl.getUniformLocation(program, "u_mixFactor")
    combinedMultLoc = gl.getUniformLocation(program, "u_combinedMult")
    grainIntensityLoc = gl.getUniformLocation(program, "u_grainIntensity")

    return true
}

function renderWebGL(now) {
    if (!start) start = now
    const t = (now - start) * 0.001 // convert to seconds

    // Update interaction times and remove expired ones
    updateInteractions(t)

    gl.uniform1f(timeLoc, t)
    gl.uniform2f(resLoc, glCanvas.value.width, glCanvas.value.height)
    gl.uniform1i(numInteractionsLoc, interactionPoints.length)
    
    // Update GUI-controlled uniforms
    gl.uniform1f(numWavesLoc, guiControls.numWaves)
    gl.uniform1f(waveAmplitudeLoc, guiControls.waveAmplitude)
    gl.uniform1f(waveSpeedLoc, guiControls.waveSpeed)
    gl.uniform1f(radialFreqLoc, guiControls.radialFreq)
    gl.uniform1f(mixFactorLoc, guiControls.mixFactor)
    gl.uniform1f(combinedMultLoc, guiControls.combinedMult)
    gl.uniform1f(grainIntensityLoc, guiControls.grainIntensity)
    
    // Update interaction data array
    for (let i = 0; i < MAX_INTERACTIONS; i++) {
        if (i < interactionPoints.length) {
            const interaction = interactionPoints[i]
            interactionData[i * 4] = interaction.x
            interactionData[i * 4 + 1] = interaction.y
            interactionData[i * 4 + 2] = interaction.intensity
            interactionData[i * 4 + 3] = t - interaction.startTime
        } else {
            interactionData[i * 4] = 0
            interactionData[i * 4 + 1] = 0
            interactionData[i * 4 + 2] = 0
            interactionData[i * 4 + 3] = 0
        }
    }
    
    gl.uniform4fv(interactionPointsLoc, interactionData)
    gl.drawArrays(gl.TRIANGLES, 0, 6)

    requestAnimationFrame(renderWebGL)
}

function initGUI() {
    gui = new GUI()
    gui.title('Controles de Espiral Visual')
    
    // Spiral Lines folder
    const spiralFolder = gui.addFolder('Líneas Espirales')
    spiralFolder.add(guiControls, 'numWaves', 5, 100, 1).name('Número de Ondas')
    spiralFolder.add(guiControls, 'waveAmplitude', 0.0, 1.0, 0.01).name('Amplitud de Ondas')
    spiralFolder.add(guiControls, 'waveSpeed', 0.1, 20.0, 0.1).name('Velocidad de Animación')
    spiralFolder.add(guiControls, 'radialFreq', 5.0, 50.0, 1.0).name('Frecuencia Radial')
    spiralFolder.add(guiControls, 'mixFactor', 0.0, 1.0, 0.01).name('Factor de Mezcla')
    spiralFolder.add(guiControls, 'combinedMult', 1.0, 10.0, 0.1).name('Multiplicador de Patrón')
    spiralFolder.open()
    
    // Effects folder
    const effectsFolder = gui.addFolder('Efectos Visuales')
    effectsFolder.add(guiControls, 'grainIntensity', 0.0, 1.0, 0.01).name('Intensidad de Grano')
    effectsFolder.open()
    
    // Position GUI in top right corner
    gui.domElement.style.position = 'fixed'
    gui.domElement.style.top = '20px'
    gui.domElement.style.right = '20px'
    gui.domElement.style.zIndex = '10000'
}

function addInteraction(x, y, intensity = 1.0) {
    const currentTime = performance.now() * 0.001
    
    // Check if there's already an interaction very close to this position
    const threshold = 50 // pixels
    for (let i = 0; i < interactionPoints.length; i++) {
        const existing = interactionPoints[i]
        const dist = Math.sqrt((existing.x - x) ** 2 + (existing.y - y) ** 2)
        if (dist < threshold) {
            // Update existing interaction
            existing.intensity = Math.min(existing.intensity + intensity * 0.3, 2.0)
            existing.startTime = currentTime
            return
        }
    }
    
    // Add new interaction
    const interaction = {
        x: x,
        y: y,
        intensity: intensity,
        startTime: currentTime
    }
    
    interactionPoints.push(interaction)
    
    // Remove oldest interactions if we exceed the limit
    if (interactionPoints.length > MAX_INTERACTIONS) {
        interactionPoints.shift()
    }
}

function updateInteractions(currentTime) {
    // Remove interactions that have faded out (after 3 seconds)
    for (let i = interactionPoints.length - 1; i >= 0; i--) {
        const interaction = interactionPoints[i]
        const age = currentTime - interaction.startTime
        if (age > 3.0) {
            interactionPoints.splice(i, 1)
        }
    }
}

function handleMouseMove(event) {
    mousePos.x = event.clientX
    mousePos.y = event.clientY

    // Add interaction point for mouse movement
    addInteraction(event.clientX, event.clientY, 0.5)

    if (!eyeCenter || !mainEye.value) return

    const vecToMouse = {
        x: mousePos.x - eyeCenter.x,
        y: mousePos.y - eyeCenter.y
    }

    const dist = Math.sqrt(vecToMouse.x * vecToMouse.x + vecToMouse.y * vecToMouse.y)

    const clampedMouseX = clamp(vecToMouse.x, -maxEyeTravelX, maxEyeTravelX)
    const clampedMouseY = clamp(vecToMouse.y, -maxEyeTravelY, maxEyeTravelY)

    const pupilX = map(clampedMouseX, -maxEyeTravelX, maxEyeTravelX, -maxEyeTravelX, maxEyeTravelX)
    const pupilY = map(clampedMouseY, -maxEyeTravelY, maxEyeTravelY, -maxEyeTravelY, maxEyeTravelY)
    const scale = map(dist, 0, maxDist, 1.2, 0.8)

    mainEye.value.style.setProperty("--pupil-x", pupilX)
    mainEye.value.style.setProperty("--pupil-y", pupilY)
    mainEye.value.style.setProperty("--scale", scale)
}

function handleMouseEnter(event) {
    addInteraction(event.clientX, event.clientY, 1.0)
}

function handleMouseClick(event) {
    addInteraction(event.clientX, event.clientY, 1.5)
}

function handleTouchStart(event) {
    event.preventDefault()
    for (let i = 0; i < event.touches.length; i++) {
        const touch = event.touches[i]
        addInteraction(touch.clientX, touch.clientY, 1.2)
    }
}

function handleTouchMove(event) {
    event.preventDefault()
    for (let i = 0; i < event.touches.length; i++) {
        const touch = event.touches[i]
        addInteraction(touch.clientX, touch.clientY, 0.8)
    }
}

function handleTouchEnd(event) {
    event.preventDefault()
    // Touch end doesn't add new interactions, just lets existing ones fade
}

function handleResize() {
    updateEyeCenter()
}

function updateEyeCenter() {
    if (!mainEye.value) return

    const eyeRect = mainEye.value.getBoundingClientRect()
    eyeCenter = {
        x: eyeRect.left + eyeRect.width * 0.5,
        y: eyeRect.top + eyeRect.height * 0.5
    }

    maxDist = Math.sqrt(window.innerWidth * window.innerWidth + window.innerHeight * window.innerHeight) * 0.5

    // Trigger initial positioning
    handleMouseMove({ clientX: eyeCenter.x, clientY: eyeCenter.y })
}

function throttled(fn) {
    let didRequest = false
    return (param) => {
        if (!didRequest) {
            requestAnimationFrame(() => {
                fn(param)
                didRequest = false
            })
            didRequest = true
        }
    }
}

function map(value, min1, max1, min2, max2) {
    return (value - min1) * (max2 - min2) / (max1 - min1) + min2
}

function clamp(value, min = 0, max = 1) {
    return value <= min ? min : value >= max ? max : value
}

onMounted(() => {
    const throttledMouseMove = throttled(handleMouseMove)
    const throttledResize = throttled(handleResize)

    // Mouse events
    window.addEventListener('mousemove', throttledMouseMove)
    window.addEventListener('mouseenter', handleMouseEnter)
    window.addEventListener('click', handleMouseClick)
    
    // Touch events
    window.addEventListener('touchstart', handleTouchStart, { passive: false })
    window.addEventListener('touchmove', handleTouchMove, { passive: false })
    window.addEventListener('touchend', handleTouchEnd, { passive: false })
    
    // Resize event
    window.addEventListener('resize', throttledResize)

    // Initialize WebGL background
    if (initWebGL()) {
        requestAnimationFrame(renderWebGL)
    }
    
    // Initialize GUI
    initGUI()

    // Initial setup
    setTimeout(() => {
        updateEyeCenter()
    }, 100)
})

onUnmounted(() => {
    // Mouse events
    window.removeEventListener('mousemove', handleMouseMove)
    window.removeEventListener('mouseenter', handleMouseEnter)
    window.removeEventListener('click', handleMouseClick)
    
    // Touch events
    window.removeEventListener('touchstart', handleTouchStart)
    window.removeEventListener('touchmove', handleTouchMove)
    window.removeEventListener('touchend', handleTouchEnd)
    
    // Resize event
    window.removeEventListener('resize', handleResize)
    
    // Clean up GUI
    if (gui) {
        gui.destroy()
    }
})
</script>

<style scoped>
.scene-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 1;
    background: #111;
    overflow: hidden;
}

.webgl-background {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
}

.eye-container {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 300px;
    height: 300px;
    z-index: 10;
}

.eye {
    --pupil-x: 0;
    --pupil-y: 0;
    --color-whites: #fff;
    --color-lid: #000;
    --color-pupil: #000;
    --color-glint: #fff;
    --scale: 1;
    max-width: 100%;
    max-height: 100%;
    fill: none;
    transform: scale(var(--scale));
    transition: transform 0.1s ease-out;
}

.main-eye {
    width: 100%;
    height: 100%;
}

.lids {
    stroke: var(--color-lid);
    stroke-width: 5%;
}

.whites {
    fill: var(--color-whites);
}

.pupil {
    fill: var(--color-pupil);
    cx: 500px;
    cy: 500px;
}

.glint {
    fill: var(--color-glint);
}

.pupil-group {
    transform: translate(calc(var(--pupil-x) * 1px), calc(var(--pupil-y) * 1px));
    transition: transform 0.1s ease-out;
}
</style>
