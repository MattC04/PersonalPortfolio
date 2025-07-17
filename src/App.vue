<script setup lang="ts">
import { ref, onMounted } from 'vue'
import LoadingScreen from './components/LoadingScreen.vue'

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

const terminalMessages = ref([
  { id: 1, text: 'Initializing terminal...', delay: 500 },
  { id: 2, text: 'Loading configuration files...', delay: 1200 },
  { id: 3, text: 'Establishing secure connection...', delay: 2000 },
  { id: 4, text: 'Fetching portfolio data...', delay: 2800 },
  { id: 5, text: 'Almost there...', delay: 3500 },
  { id: 6, text: 'Welcome to Matthew Chuang\'s Portfolio!', delay: 4200 }
])
const currentTerminalLine = ref(0)

onMounted(() => {
  startTerminalLoadingSequence()
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

const startTerminalLoadingSequence = () => {
  terminalMessages.value.forEach((msg, idx) => {
    setTimeout(() => {
      currentTerminalLine.value = idx + 1
      if (idx === terminalMessages.value.length - 1) {
        setTimeout(() => {
          isLoading.value = false
          setTimeout(() => {
            showProfile.value = true
          }, 500)
        }, 800)
      }
    }, msg.delay)
  })
}
</script>

<template>
  <!-- Terminal Loading Screen -->
  <Transition name="fade" mode="out-in">
    <LoadingScreen v-if="isLoading" :terminalMessages="terminalMessages" :currentTerminalLine="currentTerminalLine" />
  </Transition>

  <!-- Main Profile -->
  <Transition name="slide-up" mode="out-in">
    <div v-if="showProfile" class="min-h-screen bg-black font-montserrat">
      <!-- Navigation -->
      <nav class="fixed top-0 w-full bg-black/80 backdrop-blur-md z-50 transition-all duration-300 font-montserrat">
        <div class="container-custom px-4 py-4">
          <div class="flex justify-between items-center">
            <h1 class="text-2xl font-bold text-gray-800 dark:text-white font-montserrat">Matthew Chuang</h1>
            <div class="hidden md:flex space-x-8">
              <a href="#about" class="text-gray-600 hover:text-blue-600 dark:text-gray-300 dark:hover:text-blue-400 transition-colors font-montserrat">About</a>
              <a href="#projects" class="text-gray-600 hover:text-blue-600 dark:text-gray-300 dark:hover:text-blue-400 transition-colors font-montserrat">Projects</a>
              <a href="#experience" class="text-gray-600 hover:text-blue-600 dark:text-gray-300 dark:hover:text-blue-400 transition-colors font-montserrat">Experience</a>
              <a href="#contact" class="text-gray-600 hover:text-blue-600 dark:text-gray-300 dark:hover:text-blue-400 transition-colors font-montserrat">Contact</a>
            </div>
          </div>
        </div>
      </nav>
      <!-- Hero Section -->
      <section class="section-padding pt-32 font-montserrat bg-black">
        <div class="container-custom text-center">
          <div class="space-y-8">
            <h1 class="text-6xl md:text-8xl font-bold bg-gradient-to-r from-blue-600 via-purple-600 to-pink-600 bg-clip-text text-transparent animate-pulse font-montserrat">
              Hello World
            </h1>
            <p class="text-xl md:text-2xl text-gray-600 dark:text-gray-300 max-w-3xl mx-auto leading-relaxed font-montserrat">
              I'm a passionate developer creating amazing digital experiences with modern technologies.
            </p>
            <div class="flex flex-col sm:flex-row gap-4 justify-center items-center mt-8">
              <button class="btn-primary transform hover:scale-105 transition-transform duration-200 font-montserrat">
                View My Work
              </button>
              <button class="btn-secondary transform hover:scale-105 transition-transform duration-200 font-montserrat">
                Get In Touch
              </button>
            </div>
          </div>
        </div>
      </section>
      <!-- Skills Section -->
      <section class="section-padding bg-black font-montserrat">
        <div class="container-custom">
          <div class="text-center space-y-12">
            <h2 class="text-4xl font-bold text-gray-800 dark:text-white font-montserrat">Skills & Technologies</h2>
            <div class="grid grid-cols-2 md:grid-cols-4 gap-8">
              <div class="group p-6 bg-black rounded-xl transition-all duration-300 transform hover:scale-105">
                <div class="text-4xl mb-4">⚛️</div>
                <h3 class="font-semibold text-gray-800 dark:text-white font-montserrat">React</h3>
              </div>
              <div class="group p-6 bg-black rounded-xl transition-all duration-300 transform hover:scale-105">
                <div class="text-4xl mb-4">⚡</div>
                <h3 class="font-semibold text-gray-800 dark:text-white font-montserrat">Vue.js</h3>
              </div>
              <div class="group p-6 bg-black rounded-xl transition-all duration-300 transform hover:scale-105">
                <div class="text-4xl mb-4">🐍</div>
                <h3 class="font-semibold text-gray-800 dark:text-white font-montserrat">Python</h3>
              </div>
              <div class="group p-6 bg-black rounded-xl transition-all duration-300 transform hover:scale-105">
                <div class="text-4xl mb-4">🚀</div>
                <h3 class="font-semibold text-gray-800 dark:text-white font-montserrat">Node.js</h3>
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

.terminal-window {
  background: #18181b;
  border-radius: 0.75rem;
  box-shadow: 0 0 40px #0ea5e9aa;
}
.terminal-bar {
  height: 1.5rem;
}
.terminal-content {
  color: #38bdf8;
  text-shadow: 0 0 8px #38bdf8, 0 0 16px #0ea5e9;
}
.neon-text {
  color: #38bdf8;
  text-shadow: 0 0 8px #38bdf8, 0 0 16px #0ea5e9;
}

.font-mono {
  font-family: 'Fira Mono', 'Consolas', 'Menlo', 'Monaco', monospace;
}

.font-montserrat {
  font-family: 'Montserrat', Arial, Helvetica, sans-serif;
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
