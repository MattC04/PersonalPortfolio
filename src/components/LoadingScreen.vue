<template>
  <div class="min-h-screen flex justify-center bg-black loading-screen-firamono">
    <div class="terminal-window p-8 rounded-lg w-full max-w-xl mx-auto mt-8">
      <div class="terminal-bar flex items-center mb-4">
        <span class="w-3 h-3 rounded-full bg-red-500 mr-2"></span>
        <span class="w-3 h-3 rounded-full bg-yellow-400 mr-2"></span>
        <span class="w-3 h-3 rounded-full bg-green-500"></span>
      </div>
      <div class="terminal-content text-lg">
        <div v-for="(msg, idx) in terminalMessages.slice(0, currentTerminalLine)" :key="msg.id" class="neon-text">
          <span>&gt; </span>{{ msg.text }}
        </div>
        <div v-if="currentTerminalLine < terminalMessages.length" class="neon-text">
          <span>&gt; </span>{{ getCurrentLineText() }}<span class="cursor-blink">_</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { defineProps, ref, onMounted, onUnmounted, defineEmits } from 'vue'
import type { PropType } from 'vue'

interface TerminalMessage {
  id: number
  text: string
  delay?: number
}

const props = defineProps({
  terminalMessages: {
    type: Array as PropType<TerminalMessage[]>,
    required: true
  }
})

const emit = defineEmits(['loading-complete'])

const currentTerminalLine = ref(0)
const currentChar = ref(0)
const typewriterInterval = ref<number | null>(null)
const typingSpeed = 50 // ms per character
const linePause = 500 // ms pause between lines

const getCurrentLineText = () => {
  if (currentTerminalLine.value >= props.terminalMessages.length) return ''
  const currentMessage = props.terminalMessages[currentTerminalLine.value]
  return currentMessage.text.slice(0, currentChar.value)
}

function startTypewriter() {
  if (typewriterInterval.value) {
    clearInterval(typewriterInterval.value)
  }
  typewriterInterval.value = setInterval(() => {
    if (currentTerminalLine.value >= props.terminalMessages.length) {
      clearInterval(typewriterInterval.value!)
      emit('loading-complete')
      return
    }
    const currentMessage = props.terminalMessages[currentTerminalLine.value]
    if (currentChar.value < currentMessage.text.length) {
      currentChar.value++
    } else {
      clearInterval(typewriterInterval.value!)
      setTimeout(() => {
        currentChar.value = 0
        currentTerminalLine.value++
        startTypewriter()
      }, linePause)
    }
  }, typingSpeed)
}

onMounted(() => {
  startTypewriter()
})

onUnmounted(() => {
  if (typewriterInterval.value) {
    clearInterval(typewriterInterval.value)
  }
})
</script>

<style scoped>
.terminal-window {
  background: #000;
  border-radius: 0.75rem;
  box-shadow: none;
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
.cursor-blink {
  animation: blink 1s infinite;
}
@keyframes blink {
  0%, 50% { opacity: 1; }
  51%, 100% { opacity: 0; }
}
</style>

<style>
@import url('https://fonts.googleapis.com/css2?family=Fira+Mono:wght@400;500;700&display=swap');
.loading-screen-firamono {
  font-family: 'Fira Mono', 'Consolas', 'Menlo', 'Monaco', monospace !important;
}
</style> 