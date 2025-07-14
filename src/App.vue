<script setup lang="ts">
import { ref, onMounted, watch } from 'vue'

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
  { id: 1, text: 'Initializing terminal...', delay: 800 },
  { id: 2, text: 'Loading configuration files...', delay: 1800 },
  { id: 3, text: 'Establishing secure connection...', delay: 3000 },
  { id: 4, text: 'Fetching portfolio data...', delay: 4200 },
  { id: 5, text: 'Almost there...', delay: 5400 },
  { id: 6, text: 'Welcome to Matthew Chuang\'s Portfolio!', delay: 7000 }
])
const currentTerminalLine = ref(0)

const profileLines = ref([
  { id: 1, type: 'name', text: 'Matthew Chuang' },
  { id: 2, type: 'nav', text: 'About | Projects | Experience | Contact' },
  { id: 3, type: 'hero', text: 'Hello World' },
  { id: 4, type: 'desc', text: "I'm a passionate developer creating amazing digital experiences with modern technologies." },
  { id: 5, type: 'buttons', text: '' },
  { id: 6, type: 'skills', text: 'Skills & Technologies: React, Vue.js, Python, Node.js' }
])
const currentProfileLine = ref(0)

const showProfileLine = (idx: number) => currentProfileLine.value > idx

const startProfileReveal = () => {
  profileLines.value.forEach((line, idx) => {
    setTimeout(() => {
      currentProfileLine.value = idx + 1
    }, 400 * (idx + 1))
  })
}

watch(showProfile, (val) => {
  if (val) {
    setTimeout(() => {
      startProfileReveal()
    }, 300)
  }
})

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
        }, 1000)
      }
    }, msg.delay)
  })
}
</script>

<template>
  <!-- Terminal Loading Screen -->
  <Transition name="fade" mode="out-in">
    <div v-if="isLoading" class="fixed inset-0 w-screen h-screen bg-black z-50 flex flex-col items-start justify-start">
      <div class="terminal-window p-4 w-full max-w-xl mt-8 ml-8 bg-transparent shadow-none">
        <div class="terminal-bar flex items-center mb-4">
          <span class="w-3 h-3 rounded-full bg-red-500 mr-2"></span>
          <span class="w-3 h-3 rounded-full bg-yellow-400 mr-2"></span>
          <span class="w-3 h-3 rounded-full bg-green-500"></span>
        </div>
        <div class="terminal-content font-mono text-lg">
          <div v-for="(msg, idx) in terminalMessages.slice(0, currentTerminalLine)" :key="msg.id" class="neon-text">
            <span>&gt; </span>{{ msg.text }}
          </div>
        </div>
        <div v-if="currentTerminalLine < terminalMessages.length" class="font-mono text-blue-400 animate-pulse mt-4 neon-text">_</div>
      </div>
    </div>
  </Transition>

  <!-- Main Profile as Terminal Output -->
  <Transition name="slide-up" mode="out-in">
    <div v-if="showProfile" class="min-h-screen bg-gradient-to-br from-blue-50 via-white to-purple-50 dark:from-gray-900 dark:via-gray-800 dark:to-gray-900">
      <!-- Navigation -->
      <nav class="fixed top-0 w-full bg-white/80 backdrop-blur-md dark:bg-gray-900/80 z-50 transition-all duration-300">
        <div class="container-custom px-4 py-4">
          <div class="flex justify-between items-center">
            <h1 class="text-2xl font-bold text-gray-800 dark:text-white">Matthew Chuang</h1>
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

.terminal-window {
  background: transparent;
  border-radius: 0.75rem;
  box-shadow: none;
}
.terminal-bar {
  height: 1.5rem;
}

.terminal-content {
  color: #38bdf8;
}
.neon-text {
  color: #38bdf8;
}

.font-mono {
  font-family: 'Fira Mono', 'Consolas', 'Menlo', 'Monaco', monospace;
}

</style>
