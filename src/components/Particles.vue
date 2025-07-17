<template>
  <Particles id="tsparticles" :options="particlesOptions" />
  <div v-if="mouseNode" :style="mouseNodeStyle as import('vue').CSSProperties" class="cursor-node"></div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, computed } from 'vue';
import Particles from '@tsparticles/vue3';

const particlesOptions = ref({
  background: {
    color: { value: 'transparent' }
  },
  fpsLimit: 60,
  interactivity: {
    events: {
      onHover: { enable: true, mode: 'grab' },
      onClick: { enable: false },
      resize: true
    },
    modes: {
      grab: {
        distance: 180,
        links: { opacity: 0.3 }
      }
    }
  },
  particles: {
    color: { value: '#38bdf8' },
    links: {
      color: '#38bdf8',
      distance: 120,
      enable: true,
      opacity: 0.2,
      width: 1
    },
    move: {
      enable: true,
      speed: 0.6,
      direction: 'none',
      random: false,
      straight: false,
      outModes: { default: 'out' }
    },
    number: {
      value: 80,
      density: { enable: true, area: 800 }
    },
    opacity: { value: 0.5 },
    shape: { type: 'circle' },
    size: { value: { min: 1, max: 3 } }
  },
  detectRetina: true
});

const mouseNode = ref(false);
const mouseX = ref(0);
const mouseY = ref(0);

const mouseNodeStyle = computed(() => ({
  position: 'fixed',
  left: mouseX.value + 'px',
  top: mouseY.value + 'px',
  width: '10px',
  height: '10px',
  borderRadius: '50%',
  background: '#38bdf8',
  border: '1.5px solid #fff',
  zIndex: '10',
  pointerEvents: 'none',
  transform: 'translate(-50%, -50%)',
  transition: 'background 0.1s, border 0.1s'
}));

function handleMouseMove(e: MouseEvent) {
  mouseNode.value = true;
  mouseX.value = e.clientX;
  mouseY.value = e.clientY;
}

onMounted(() => {
  window.addEventListener('mousemove', handleMouseMove);
});
onUnmounted(() => {
  window.removeEventListener('mousemove', handleMouseMove);
});
</script>

<style scoped>
#tsparticles {
  position: fixed;
  inset: 0;
  width: 100vw;
  height: 100vh;
  pointer-events: none;
  z-index: 10;
}
.cursor-node {
  position: fixed;
  pointer-events: none;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: #38bdf8;
  border: 1.5px solid #fff;
  z-index: 10;
  transform: translate(-50%, -50%);
  transition: background 0.1s, border 0.1s;
}
</style> 