<template>
  <Particles id="tsparticles" :options="particlesOptions" />
</template>

<script setup lang="ts">
import { Particles } from '@tsparticles/vue3';
import { ref } from 'vue';

const particlesOptions = ref({
  background: {
    color: {
      value: 'transparent',
    },
  },
  fpsLimit: 60,
  interactivity: {
    events: {
      onHover: {
        enable: true,
        mode: 'grab',
      },
      onClick: {
        enable: true,
        mode: 'push',
      },
      resize: true,
    },
    modes: {
      grab: {
        distance: 180,
        links: {
          opacity: 0.6,
        },
      },
      push: {
        quantity: 4,
      },
    },
  },
  particles: {
    color: {
      value: '#38bdf8',
    },
    links: {
      color: '#38bdf8',
      distance: 150,
      enable: true,
      opacity: 0.4,
      width: 1.5,
    },
    collisions: {
      enable: false,
    },
    move: {
      direction: 'none',
      enable: true,
      outModes: {
        default: 'bounce',
      },
      random: false,
      speed: 1.2,
      straight: false,
    },
    number: {
      density: {
        enable: true,
        area: 800,
      },
      value: 40,
    },
    opacity: {
      value: 0.7,
    },
    shape: {
      type: 'circle',
    },
    size: {
      value: { min: 2, max: 4 },
    },
  },
  detectRetina: true,
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
</style> 