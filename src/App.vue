<script setup lang="ts">
import { ref, onMounted } from 'vue'
import LoadingScreen from './components/LoadingScreen.vue'
import AnimatedBackground from './components/AnimatedBackground.vue'
import GradientAnimations from './components/GradientAnimations.vue'
import AIChatInterface from './components/AIChatInterface.vue'
import ImageRecognition from './components/ImageRecognition.vue'
import SentimentAnalysis from './components/SentimentAnalysis.vue'
import TravelRecommendations from './components/TravelRecommendations.vue'

const isLoading = ref(true)
const showProfile = ref(false)

const terminalMessages = ref([
  { id: 1, text: 'Initializing terminal...', delay: 500 },
  { id: 2, text: 'Loading configuration files...', delay: 1200 },
  { id: 3, text: 'Establishing secure connection...', delay: 2000 },
  { id: 4, text: 'Fetching portfolio data...', delay: 2800 },
  { id: 5, text: 'Almost there...', delay: 3500 },
  { id: 6, text: 'Welcome to Matthew Chuang\'s Portfolio!', delay: 4200 }
])

function handleLoadingComplete() {
  isLoading.value = false
  showProfile.value = true
}
</script>

<template>
  <!-- Terminal Loading Screen -->
  <Transition name="fade" mode="out-in">
    <LoadingScreen v-if="isLoading" :terminalMessages="terminalMessages" @loading-complete="handleLoadingComplete" />
  </Transition>

  <!-- Main Profile -->
  <Transition name="slide-up" mode="out-in">
    <div v-if="showProfile" class="min-h-screen bg-black font-montserrat relative">
      <!-- Animated Background -->
      <AnimatedBackground />
      
      <!-- Navigation -->
      <nav class="fixed top-0 w-full bg-black/80 backdrop-blur-md z-50 transition-all duration-300 font-montserrat">
        <div class="container-custom px-4 py-4">
          <div class="flex justify-between items-center">
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
      <section class="section-padding pt-32 font-montserrat bg-black relative overflow-hidden">
        <GradientAnimations :triggerOnScroll="false" />
        <div class="container-custom text-center relative z-10">
          <div class="space-y-8">
            <h1 class="text-5xl md:text-7xl font-bold text-white font-montserrat">Matthew Chuang</h1>
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
      <section class="section-padding bg-black font-montserrat relative overflow-hidden">
        <GradientAnimations />
        <div class="container-custom relative z-10">
          <div class="text-center space-y-12">
            <h2 class="text-4xl font-bold text-gray-800 dark:text-white font-montserrat">Skills & Technologies</h2>
            <div class="grid grid-cols-2 md:grid-cols-4 gap-8">
              <div class="group p-6 bg-black/50 backdrop-blur-sm rounded-xl transition-all duration-300 transform hover:scale-105 hover:bg-black/70 border border-gray-800/30 hover:border-blue-500/50">
                <div class="text-4xl mb-4">⚛️</div>
                <h3 class="font-semibold text-gray-800 dark:text-white font-montserrat">React</h3>
              </div>
              <div class="group p-6 bg-black/50 backdrop-blur-sm rounded-xl transition-all duration-300 transform hover:scale-105 hover:bg-black/70 border border-gray-800/30 hover:border-blue-500/50">
                <div class="text-4xl mb-4">⚡</div>
                <h3 class="font-semibold text-gray-800 dark:text-white font-montserrat">Vue.js</h3>
              </div>
              <div class="group p-6 bg-black/50 backdrop-blur-sm rounded-xl transition-all duration-300 transform hover:scale-105 hover:bg-black/70 border border-gray-800/30 hover:border-blue-500/50">
                <div class="text-4xl mb-4">🐍</div>
                <h3 class="font-semibold text-gray-800 dark:text-white font-montserrat">Python</h3>
              </div>
              <div class="group p-6 bg-black/50 backdrop-blur-sm rounded-xl transition-all duration-300 transform hover:scale-105 hover:bg-black/70 border border-gray-800/30 hover:border-blue-500/50">
                <div class="text-4xl mb-4">🚀</div>
                <h3 class="font-semibold text-gray-800 dark:text-white font-montserrat">Node.js</h3>
              </div>
            </div>
          </div>
        </div>
      </section>
      
      <!-- Projects Section -->
      <section class="section-padding bg-black font-montserrat relative overflow-hidden">
        <GradientAnimations />
        <div class="container-custom relative z-10">
          <div class="text-center space-y-12">
            <h2 class="text-4xl font-bold text-gray-800 dark:text-white font-montserrat">Featured Projects</h2>
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
              <div class="group project-card p-6 bg-black/50 backdrop-blur-sm rounded-xl transition-all duration-500 transform hover:scale-105 hover:bg-black/70 border border-gray-800/30 hover:border-blue-500/50 relative overflow-hidden">
                <div class="absolute inset-0 bg-gradient-to-br from-blue-600/10 to-purple-600/10 opacity-0 group-hover:opacity-100 transition-opacity duration-500"></div>
                <div class="relative z-10">
                  <div class="text-4xl mb-4">🚀</div>
                  <h3 class="font-semibold text-gray-800 dark:text-white font-montserrat mb-2">Portfolio Website</h3>
                  <p class="text-gray-600 dark:text-gray-300 text-sm">Modern portfolio built with Vue.js and Tailwind CSS</p>
                </div>
              </div>
              
              <div class="group project-card p-6 bg-black/50 backdrop-blur-sm rounded-xl transition-all duration-500 transform hover:scale-105 hover:bg-black/70 border border-gray-800/30 hover:border-purple-500/50 relative overflow-hidden">
                <div class="absolute inset-0 bg-gradient-to-br from-purple-600/10 to-pink-600/10 opacity-0 group-hover:opacity-100 transition-opacity duration-500"></div>
                <div class="relative z-10">
                  <div class="text-4xl mb-4">⚡</div>
                  <h3 class="font-semibold text-gray-800 dark:text-white font-montserrat mb-2">E-commerce Platform</h3>
                  <p class="text-gray-600 dark:text-gray-300 text-sm">Full-stack e-commerce solution with React and Node.js</p>
                </div>
              </div>
              
              <div class="group project-card p-6 bg-black/50 backdrop-blur-sm rounded-xl transition-all duration-500 transform hover:scale-105 hover:bg-black/70 border border-gray-800/30 hover:border-pink-500/50 relative overflow-hidden">
                <div class="absolute inset-0 bg-gradient-to-br from-pink-600/10 to-blue-600/10 opacity-0 group-hover:opacity-100 transition-opacity duration-500"></div>
                <div class="relative z-10">
                  <div class="text-4xl mb-4">🤖</div>
                  <h3 class="font-semibold text-gray-800 dark:text-white font-montserrat mb-2">AI Chat Bot</h3>
                  <p class="text-gray-600 dark:text-gray-300 text-sm">Intelligent chatbot powered by machine learning</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>
      
      <!-- AI Showcase Section -->
      <section class="section-padding bg-black font-montserrat relative overflow-hidden">
        <GradientAnimations />
        <div class="container-custom relative z-10">
          <div class="text-center space-y-12">
            <h2 class="text-4xl font-bold text-gray-800 dark:text-white font-montserrat">AI & Machine Learning Showcase</h2>
            <p class="text-xl text-gray-600 dark:text-gray-300 max-w-3xl mx-auto leading-relaxed">
              Experience cutting-edge AI technologies in action. These interactive demos showcase Matthew's expertise in machine learning, computer vision, and natural language processing.
            </p>
            
            <div class="space-y-16">
              <!-- AI Chat Demo -->
              <div class="ai-demo-section">
                <h3 class="text-2xl font-bold text-gray-800 dark:text-white mb-6">🤖 AI Portfolio Assistant</h3>
                <p class="text-gray-600 dark:text-gray-300 mb-8 max-w-2xl mx-auto">
                  Chat with an AI assistant that knows everything about Matthew's skills, projects, and experience. 
                  Demonstrates natural language processing and conversational AI capabilities.
                </p>
                <AIChatInterface />
              </div>
              
              <!-- Image Recognition Demo -->
              <div class="ai-demo-section">
                <h3 class="text-2xl font-bold text-gray-800 dark:text-white mb-6">🔍 Computer Vision & Image Recognition</h3>
                <p class="text-gray-600 dark:text-gray-300 mb-8 max-w-2xl mx-auto">
                  Upload images and watch AI identify objects, faces, and scenes in real-time. 
                  Showcases deep learning models for computer vision and image analysis.
                </p>
                <ImageRecognition />
              </div>
              
              <!-- Sentiment Analysis Demo -->
              <div class="ai-demo-section">
                <h3 class="text-2xl font-bold text-gray-800 dark:text-white mb-6">📊 Natural Language Processing & Sentiment Analysis</h3>
                <p class="text-gray-600 dark:text-gray-300 mb-8 max-w-2xl mx-auto">
                  Analyze the emotional tone and sentiment of any text using advanced NLP techniques. 
                  Demonstrates machine learning models for text analysis and emotion detection.
                </p>
                <SentimentAnalysis />
              </div>
              
              <!-- Travel Recommendations Demo -->
              <div class="ai-demo-section">
                <h3 class="text-2xl font-bold text-gray-800 dark:text-white mb-6">✈️ AI-Powered Travel Recommendations</h3>
                <p class="text-gray-600 dark:text-gray-300 mb-8 max-w-2xl mx-auto">
                  Get personalized travel suggestions based on location, time, and preferences using geospatial AI. 
                  Demonstrates machine learning for location-based recommendations and temporal pattern analysis.
                </p>
                <TravelRecommendations />
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

.project-card {
  position: relative;
  overflow: hidden;
}

.project-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.1), transparent);
  transition: left 0.5s;
}

.project-card:hover::before {
  left: 100%;
}

.project-card:hover {
  box-shadow: 0 0 30px rgba(56, 189, 248, 0.3);
}
</style>
