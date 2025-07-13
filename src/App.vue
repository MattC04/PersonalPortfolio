<script setup lang="ts">
import { ref, onMounted } from 'vue'

const isLoading = ref(true)
const showProfile = ref(false)
const loadingProgress = ref(0)
const interactiveElements = ref([
  { id: 1, text: 'Loading creativity...', delay: 500 },
  { id: 2, text: 'Brewing coffee...', delay: 1500 },
  { id: 3, text: 'Connecting neurons...', delay: 2500 },
  { id: 4, text: 'Almost there...', delay: 3500 }
])
const currentElement = ref(0)

onMounted(() => {
  // Start the loading animation sequence
  startLoadingSequence()
})

const startLoadingSequence = () => {
  const totalDuration = 5000 // 5 seconds total
  const interval = 100
  
  const timer = setInterval(() => {
    loadingProgress.value += (100 / (totalDuration / interval))
    
    if (loadingProgress.value >= 100) {
      clearInterval(timer)
      setTimeout(() => {
        isLoading.value = false
        setTimeout(() => {
          showProfile.value = true
        }, 500) // Small delay for transition
      }, 500)
    }
  }, interval)
  
  // Show interactive elements with delays
  interactiveElements.value.forEach((element, index) => {
    setTimeout(() => {
      currentElement.value = index
    }, element.delay)
  })
}
</script>

<template>
  <!-- Loading Screen -->
  <Transition name="fade" mode="out-in">
    <div v-if="isLoading" class="min-h-screen bg-gradient-to-br from-indigo-900 via-purple-900 to-pink-900 flex items-center justify-center">
      <div class="text-center space-y-8">
        <!-- Animated Logo/Icon -->
        <div class="relative">
          <div class="w-32 h-32 mx-auto bg-gradient-to-r from-blue-400 to-purple-500 rounded-full flex items-center justify-center animate-pulse">
            <span class="text-4xl">👨‍💻</span>
          </div>
          <!-- Orbiting dots -->
          <div class="absolute inset-0 animate-spin">
            <div class="absolute top-0 left-1/2 transform -translate-x-1/2 w-3 h-3 bg-yellow-400 rounded-full"></div>
            <div class="absolute bottom-0 left-1/2 transform -translate-x-1/2 w-3 h-3 bg-pink-400 rounded-full"></div>
            <div class="absolute left-0 top-1/2 transform -translate-y-1/2 w-3 h-3 bg-green-400 rounded-full"></div>
            <div class="absolute right-0 top-1/2 transform -translate-y-1/2 w-3 h-3 bg-blue-400 rounded-full"></div>
          </div>
        </div>
        
        <!-- Title -->
        <h1 class="text-6xl font-bold text-white animate-bounce">
          Welcome
        </h1>
        
        <!-- Interactive Loading Messages -->
        <div class="h-8">
          <Transition name="slide-up" mode="out-in">
            <p v-if="currentElement < interactiveElements.length" 
               :key="currentElement"
               class="text-xl text-gray-300 animate-pulse">
              {{ interactiveElements[currentElement].text }}
            </p>
          </Transition>
        </div>
        
        <!-- Progress Bar -->
        <div class="w-80 mx-auto">
          <div class="bg-gray-700 rounded-full h-2">
            <div class="bg-gradient-to-r from-blue-500 to-purple-500 h-2 rounded-full transition-all duration-300"
                 :style="{ width: loadingProgress + '%' }"></div>
          </div>
          <p class="text-sm text-gray-400 mt-2">{{ Math.round(loadingProgress) }}%</p>
        </div>
        
        <!-- Click to skip hint -->
        <p class="text-sm text-gray-500 animate-pulse">
          Click anywhere to skip
        </p>
      </div>
    </div>
  </Transition>

  <!-- Main Profile -->
  <Transition name="slide-up" mode="out-in">
    <div v-if="showProfile" class="min-h-screen bg-gradient-to-br from-blue-50 via-white to-purple-50 dark:from-gray-900 dark:via-gray-800 dark:to-gray-900">
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
          <div class="space-y-8">
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
        </div>
      </section>

      <!-- Skills Section -->
      <section class="section-padding bg-white dark:bg-gray-800">
        <div class="container-custom">
          <div class="text-center space-y-12">
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
        </div>
      </section>
    </div>
  </Transition>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.slide-up-enter-active {
  transition: all 0.8s ease-out;
}

.slide-up-enter-from {
  opacity: 0;
  transform: translateY(30px);
}

.slide-up-enter-to {
  opacity: 1;
  transform: translateY(0);
}

/* Custom animations */
@keyframes float {
  0%, 100% { transform: translateY(0px); }
  50% { transform: translateY(-10px); }
}

.animate-float {
  animation: float 3s ease-in-out infinite;
}
</style>
