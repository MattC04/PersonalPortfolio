<template>
  <div class="min-h-screen flex items-center justify-center bg-black">
    <div class="terminal-window p-8 rounded-lg shadow-2xl w-full max-w-xl mx-auto">
      <div class="terminal-bar flex items-center mb-4">
        <span class="w-3 h-3 rounded-full bg-red-500 mr-2"></span>
        <span class="w-3 h-3 rounded-full bg-yellow-400 mr-2"></span>
        <span class="w-3 h-3 rounded-full bg-green-500"></span>
      </div>
      <div class="terminal-content font-mono text-lg">
        <div v-for="(msg, idx) in terminalMessages.slice(0, currentTerminalLine)" :key="msg.id" class="neon-text">
          <span>&gt; </span>{{ (msg as TerminalMessage).text }}
        </div>
      </div>
      <div v-if="currentTerminalLine < terminalMessages.length" class="font-mono text-blue-400 animate-pulse mt-4 neon-text">_</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { defineProps } from 'vue'
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
  },
  currentTerminalLine: {
    type: Number,
    required: true
  }
})
</script>

<style scoped>
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
</style> 