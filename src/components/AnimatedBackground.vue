<template>
  <div class="animated-background">
    <!-- Floating geometric shapes -->
    <div class="floating-shapes">
      <div 
        v-for="(shape, index) in shapes" 
        :key="index"
        :class="['floating-shape', shape.type]"
        :style="{
          left: shape.x + '%',
          top: shape.y + '%',
          animationDelay: shape.delay + 's',
          animationDuration: shape.duration + 's'
        }"
      ></div>
    </div>
    
    <!-- Parallax layers -->
    <div class="parallax-layer layer-1" :style="{ transform: `translateY(${parallaxOffset * 0.1}px)` }"></div>
    <div class="parallax-layer layer-2" :style="{ transform: `translateY(${parallaxOffset * 0.3}px)` }"></div>
    <div class="parallax-layer layer-3" :style="{ transform: `translateY(${parallaxOffset * 0.5}px)` }"></div>
    
    <!-- Gradient orbs -->
    <div class="gradient-orbs">
      <div class="orb orb-1" :style="{ transform: `translate(${orbOffset * 0.2}px, ${orbOffset * 0.1}px)` }"></div>
      <div class="orb orb-2" :style="{ transform: `translate(${orbOffset * -0.3}px, ${orbOffset * 0.2}px)` }"></div>
      <div class="orb orb-3" :style="{ transform: `translate(${orbOffset * 0.1}px, ${orbOffset * -0.2}px)` }"></div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const parallaxOffset = ref(0)
const orbOffset = ref(0)
let animationFrame: number

// Generate random floating shapes
const shapes = ref(Array.from({ length: 20 }, (_, i) => ({
  type: ['circle', 'square', 'triangle'][Math.floor(Math.random() * 3)],
  x: Math.random() * 100,
  y: Math.random() * 100,
  delay: Math.random() * 5,
  duration: 8 + Math.random() * 4
})))

const handleScroll = () => {
  parallaxOffset.value = window.scrollY
  orbOffset.value = window.scrollY * 0.1
}

const animateOrbs = () => {
  orbOffset.value += 0.5
  animationFrame = requestAnimationFrame(animateOrbs)
}

onMounted(() => {
  console.log('AnimatedBackground mounted, starting animations...')
  console.log('Shapes generated:', shapes.value.length)
  
  // Start animations immediately for testing
  animateOrbs()
  
  // Add scroll listener after a short delay
  setTimeout(() => {
    window.addEventListener('scroll', handleScroll)
    console.log('Scroll listener added')
  }, 100)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
  if (animationFrame) {
    cancelAnimationFrame(animationFrame)
  }
})
</script>

<style scoped>
.animated-background {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: -1;
  overflow: hidden;
}

.floating-shapes {
  position: absolute;
  width: 100%;
  height: 100%;
}

.floating-shape {
  position: absolute;
  opacity: 0.3;
  animation: float linear infinite;
}

.floating-shape.circle {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: linear-gradient(45deg, #38bdf8, #0ea5e9);
  box-shadow: 0 0 30px rgba(56, 189, 248, 0.5);
}

.floating-shape.square {
  width: 60px;
  height: 60px;
  background: linear-gradient(45deg, #8b5cf6, #7c3aed);
  box-shadow: 0 0 30px rgba(139, 92, 246, 0.5);
  transform: rotate(45deg);
}

.floating-shape.triangle {
  width: 0;
  height: 0;
  border-left: 35px solid transparent;
  border-right: 35px solid transparent;
  border-bottom: 60px solid #ec4899;
  box-shadow: 0 0 30px rgba(236, 72, 153, 0.5);
}

@keyframes float {
  0% {
    transform: translateY(0px) rotate(0deg);
    opacity: 0.1;
  }
  50% {
    opacity: 0.3;
  }
  100% {
    transform: translateY(-100px) rotate(360deg);
    opacity: 0.1;
  }
}

.parallax-layer {
  position: absolute;
  width: 100%;
  height: 200%;
  background: radial-gradient(circle at 20% 80%, rgba(56, 189, 248, 0.05) 0%, transparent 50%),
              radial-gradient(circle at 80% 20%, rgba(139, 92, 246, 0.05) 0%, transparent 50%),
              radial-gradient(circle at 40% 40%, rgba(236, 72, 153, 0.05) 0%, transparent 50%);
}

.layer-1 {
  top: -50%;
  background-size: 400px 400px;
}

.layer-2 {
  top: -100%;
  background-size: 600px 600px;
}

.layer-3 {
  top: -150%;
  background-size: 800px 800px;
}

.gradient-orbs {
  position: absolute;
  width: 100%;
  height: 100%;
}

.orb {
  position: absolute;
  border-radius: 50%;
  filter: blur(40px);
  opacity: 0.3;
}

.orb-1 {
  width: 400px;
  height: 400px;
  background: radial-gradient(circle, rgba(56, 189, 248, 0.6) 0%, transparent 70%);
  top: 20%;
  left: 10%;
}

.orb-2 {
  width: 500px;
  height: 500px;
  background: radial-gradient(circle, rgba(139, 92, 246, 0.5) 0%, transparent 70%);
  top: 60%;
  right: 20%;
}

.orb-3 {
  width: 350px;
  height: 350px;
  background: radial-gradient(circle, rgba(236, 72, 153, 0.5) 0%, transparent 70%);
  top: 40%;
  left: 60%;
}
</style> 