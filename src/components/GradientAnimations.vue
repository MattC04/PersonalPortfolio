<template>
  <div class="gradient-animations">
    <!-- Animated gradient borders -->
    <div class="gradient-border" :class="{ 'animate': isVisible }"></div>
    
    <!-- Floating gradient particles -->
    <div class="gradient-particles" :class="{ 'animate': isVisible }">
      <div 
        v-for="(particle, index) in particles" 
        :key="index"
        class="particle"
        :style="{
          '--delay': particle.delay + 's',
          '--duration': particle.duration + 's',
          '--x': particle.x + '%',
          '--y': particle.y + '%'
        }"
      ></div>
    </div>
    
    <!-- Gradient text glow effect -->
    <div class="gradient-glow" :class="{ 'animate': isVisible }"></div>
    
    <!-- Animated gradient background -->
    <div class="gradient-bg" :class="{ 'animate': isVisible }"></div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

interface Props {
  triggerOnScroll?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  triggerOnScroll: true
})

const isVisible = ref(false)
let observer: IntersectionObserver | null = null

// Generate random particles
const particles = ref(Array.from({ length: 8 }, (_, i) => ({
  x: Math.random() * 100,
  y: Math.random() * 100,
  delay: Math.random() * 2,
  duration: 3 + Math.random() * 2
})))

const handleIntersection = (entries: IntersectionObserverEntry[]) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      isVisible.value = true
    } else if (props.triggerOnScroll) {
      isVisible.value = false
    }
  })
}

onMounted(() => {
  console.log('GradientAnimations mounted, triggerOnScroll:', props.triggerOnScroll)
  
  if (props.triggerOnScroll) {
    observer = new IntersectionObserver(handleIntersection, {
      threshold: 0.3,
      rootMargin: '0px 0px -100px 0px'
    })
    
    const element = document.querySelector('.gradient-animations')
    if (element) {
      observer.observe(element)
      console.log('Observer set up for element:', element)
    } else {
      console.log('Could not find .gradient-animations element')
    }
  } else {
    // Auto-trigger after a delay
    console.log('Auto-triggering animations in 500ms')
    setTimeout(() => {
      isVisible.value = true
      console.log('Animations triggered automatically')
    }, 500)
  }
})

onUnmounted(() => {
  if (observer) {
    observer.disconnect()
  }
})
</script>

<style scoped>
.gradient-animations {
  position: relative;
  width: 100%;
  height: 100%;
}

.gradient-border {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  border: 3px solid transparent;
  border-radius: 12px;
  background: linear-gradient(45deg, #38bdf8, #8b5cf6, #ec4899, #38bdf8);
  background-size: 400% 400%;
  opacity: 0;
  transition: opacity 0.5s ease;
}

.gradient-border.animate {
  opacity: 1;
  animation: gradientShift 3s ease infinite;
}

.gradient-particles {
  position: absolute;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.particle {
  position: absolute;
  width: 4px;
  height: 4px;
  background: linear-gradient(45deg, #38bdf8, #8b5cf6);
  border-radius: 50%;
  opacity: 0;
  left: var(--x);
  top: var(--y);
}

.gradient-particles.animate .particle {
  animation: particleFloat var(--duration) ease-in-out infinite;
  animation-delay: var(--delay);
}

.gradient-glow {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 300px;
  height: 300px;
  background: radial-gradient(circle, rgba(56, 189, 248, 0.4) 0%, transparent 70%);
  border-radius: 50%;
  opacity: 0;
  transition: opacity 0.5s ease;
}

.gradient-glow.animate {
  opacity: 1;
  animation: glowPulse 2s ease-in-out infinite;
}

.gradient-bg {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, 
    rgba(56, 189, 248, 0.05) 0%, 
    rgba(139, 92, 246, 0.05) 25%, 
    rgba(236, 72, 153, 0.05) 50%, 
    rgba(56, 189, 248, 0.05) 75%, 
    rgba(139, 92, 246, 0.05) 100%);
  background-size: 400% 400%;
  opacity: 0;
  transition: opacity 0.5s ease;
}

.gradient-bg.animate {
  opacity: 1;
  animation: gradientMove 4s ease infinite;
}

@keyframes gradientShift {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

@keyframes particleFloat {
  0% {
    opacity: 0;
    transform: translateY(0px) scale(0);
  }
  50% {
    opacity: 1;
    transform: translateY(-20px) scale(1);
  }
  100% {
    opacity: 0;
    transform: translateY(-40px) scale(0);
  }
}

@keyframes glowPulse {
  0%, 100% {
    transform: translate(-50%, -50%) scale(1);
    opacity: 0.2;
  }
  50% {
    transform: translate(-50%, -50%) scale(1.2);
    opacity: 0.4;
  }
}

@keyframes gradientMove {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}
</style> 