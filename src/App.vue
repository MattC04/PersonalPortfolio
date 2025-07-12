<script setup lang="ts">
import { ref, onMounted } from 'vue'

const isLoaded = ref(false)

onMounted(() => {
  setTimeout(() => {
    isLoaded.value = true
  }, 100)
})
</script>

<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 via-white to-purple-50 dark:from-gray-900 dark:via-gray-800 dark:to-gray-900">
    <!-- Navigation -->
    <nav class="fixed top-0 w-full bg-white/80 backdrop-blur-md dark:bg-gray-900/80 z-50 transition-all duration-300">
      <div class="container-custom px-4 py-4">
        <div class="flex justify-between items-center">
          <h1 class="text-2xl font-bold text-gray-800 dark:text-white">Your Name</h1>
          <div class="hidden md:flex space-x-8">
            <a href="#about" class="text-gray-600 hover:text-blue-600 dark:text-gray-300 dark:hover:text-blue-400 transition-colors">About</a>
            <a href="#projects" class="text-gray-600 hover:text-blue-600 dark:text-gray-300 dark:hover:text-blue-400 transition-colors">Projects</a>
            <a href="#experience" class="text-gray-600 hover:text-blue-600 dark:text-gray-300 dark:hover:text-blue-400 transition-colors">Experience</a>
            <a href="#contact" class="text-gray-600 hover:text-blue-600 dark:text-gray-300 dark:hover:text-blue-400 transition-colors">Contact</a>
          </div>
        </div>
      </div>
    </nav>

    <!-- Hero Section -->
    <section class="section-padding pt-32">
      <div class="container-custom text-center">
        <Transition name="fade-up" appear>
          <div v-if="isLoaded" class="space-y-8">
            <h1 class="text-6xl md:text-8xl font-bold bg-gradient-to-r from-blue-600 via-purple-600 to-pink-600 bg-clip-text text-transparent animate-pulse">
              Hello World
            </h1>
            <p class="text-xl md:text-2xl text-gray-600 dark:text-gray-300 max-w-3xl mx-auto leading-relaxed">
              I'm a passionate developer creating amazing digital experiences with modern technologies.
            </p>
            <div class="flex flex-col sm:flex-row gap-4 justify-center items-center mt-8">
              <button class="btn-primary transform hover:scale-105 transition-transform duration-200">
                View My Work
              </button>
              <button class="btn-secondary transform hover:scale-105 transition-transform duration-200">
                Get In Touch
              </button>
            </div>
          </div>
        </Transition>
      </div>
    </section>

    <!-- Skills Section -->
    <section class="section-padding bg-white dark:bg-gray-800">
      <div class="container-custom">
        <Transition name="fade-up" appear>
          <div v-if="isLoaded" class="text-center space-y-12">
            <h2 class="text-4xl font-bold text-gray-800 dark:text-white">Skills & Technologies</h2>
            <div class="grid grid-cols-2 md:grid-cols-4 gap-8">
              <div class="group p-6 bg-gray-50 dark:bg-gray-700 rounded-xl hover:bg-blue-50 dark:hover:bg-gray-600 transition-all duration-300 transform hover:scale-105">
                <div class="text-4xl mb-4">⚛️</div>
                <h3 class="font-semibold text-gray-800 dark:text-white">React</h3>
              </div>
              <div class="group p-6 bg-gray-50 dark:bg-gray-700 rounded-xl hover:bg-blue-50 dark:hover:bg-gray-600 transition-all duration-300 transform hover:scale-105">
                <div class="text-4xl mb-4">⚡</div>
                <h3 class="font-semibold text-gray-800 dark:text-white">Vue.js</h3>
              </div>
              <div class="group p-6 bg-gray-50 dark:bg-gray-700 rounded-xl hover:bg-blue-50 dark:hover:bg-gray-600 transition-all duration-300 transform hover:scale-105">
                <div class="text-4xl mb-4">🐍</div>
                <h3 class="font-semibold text-gray-800 dark:text-white">Python</h3>
              </div>
              <div class="group p-6 bg-gray-50 dark:bg-gray-700 rounded-xl hover:bg-blue-50 dark:hover:bg-gray-600 transition-all duration-300 transform hover:scale-105">
                <div class="text-4xl mb-4">🚀</div>
                <h3 class="font-semibold text-gray-800 dark:text-white">Node.js</h3>
              </div>
            </div>
          </div>
        </Transition>
      </div>
    </section>
  </div>
</template>

<style scoped>
.fade-up-enter-active {
  transition: all 0.8s ease-out;
}

.fade-up-enter-from {
  opacity: 0;
  transform: translateY(30px);
}

.fade-up-enter-to {
  opacity: 1;
  transform: translateY(0);
}
</style>
