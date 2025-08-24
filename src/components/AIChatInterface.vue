<template>
  <div class="ai-chat-interface">
    <div class="chat-container">
      <!-- Chat Header -->
      <div class="chat-header">
        <div class="ai-avatar">
          <div class="ai-pulse"></div>
          <span class="ai-icon">🤖</span>
        </div>
        <div class="header-text">
          <h3 class="ai-title">AI Portfolio Assistant</h3>
          <p class="ai-subtitle">Ask me about Matthew's skills, projects, or experience</p>
        </div>
      </div>

      <!-- Chat Messages -->
      <div class="chat-messages" ref="messagesContainer">
        <div 
          v-for="(message, index) in messages" 
          :key="index"
          :class="['message', message.type]"
        >
          <div class="message-content">
            <div class="message-avatar">
              {{ message.type === 'user' ? '👤' : '🤖' }}
            </div>
            <div class="message-text">
              <div v-if="message.type === 'ai' && message.isTyping" class="typing-indicator">
                <span></span>
                <span></span>
                <span></span>
              </div>
              <div v-else v-html="message.text"></div>
            </div>
          </div>
        </div>
      </div>

      <!-- Chat Input -->
      <div class="chat-input-container">
        <div class="input-wrapper">
          <input 
            v-model="userInput" 
            @keyup.enter="sendMessage"
            placeholder="Ask about Matthew's skills, projects, or experience..."
            class="chat-input"
            :disabled="isProcessing"
          />
          <button 
            @click="sendMessage" 
            class="send-button"
            :disabled="!userInput.trim() || isProcessing"
          >
            <span v-if="!isProcessing">🚀</span>
            <div v-else class="loading-spinner"></div>
          </button>
        </div>
        <div class="suggestions">
          <button 
            v-for="suggestion in quickSuggestions" 
            :key="suggestion"
            @click="sendQuickMessage(suggestion)"
            class="suggestion-button"
            :disabled="isProcessing"
          >
            {{ suggestion }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, nextTick, watch } from 'vue'

interface ChatMessage {
  type: 'user' | 'ai'
  text: string
  isTyping?: boolean
}

const messages = ref<ChatMessage[]>([
  {
    type: 'ai',
    text: "Hello! I'm your AI portfolio assistant. I can tell you about Matthew's technical skills, projects, experience, and more. What would you like to know?"
  }
])

const userInput = ref('')
const isProcessing = ref(false)
const messagesContainer = ref<HTMLElement>()

const quickSuggestions = [
  "What are Matthew's technical skills?",
  "Tell me about his projects",
  "What's his experience level?",
  "Show me his GitHub stats"
]

// AI Response Logic - This simulates an AI model
const generateAIResponse = async (userMessage: string): Promise<string> => {
  // Simulate API call delay
  await new Promise(resolve => setTimeout(resolve, 1000 + Math.random() * 2000))
  
  const lowerMessage = userMessage.toLowerCase()
  
  // Simple rule-based responses - in a real app, this would call an actual ML model
  if (lowerMessage.includes('skill') || lowerMessage.includes('technology')) {
    return `
      <strong>Matthew's Technical Skills:</strong><br><br>
      <strong>Frontend:</strong> React, Vue.js, TypeScript, Tailwind CSS<br>
      <strong>Backend:</strong> Node.js, Python, Express, FastAPI<br>
      <strong>AI/ML:</strong> TensorFlow, PyTorch, Scikit-learn, NLP<br>
      <strong>Databases:</strong> MongoDB, PostgreSQL, Redis<br>
      <strong>DevOps:</strong> Docker, AWS, CI/CD, Git<br><br>
      He's particularly strong in <span class="highlight">machine learning</span> and <span class="highlight">full-stack development</span>.
    `
  }
  
  if (lowerMessage.includes('project') || lowerMessage.includes('work')) {
    return `
      <strong>Matthew's Featured Projects:</strong><br><br>
      🚀 <strong>AI-Powered Portfolio</strong> - This very website with ML integration<br>
      ⚡ <strong>E-commerce Platform</strong> - Full-stack with ML recommendations<br>
      🤖 <strong>NLP Chatbot</strong> - Advanced conversational AI<br>
      📊 <strong>Data Analytics Dashboard</strong> - Real-time ML insights<br><br>
      All projects showcase <span class="highlight">modern AI/ML techniques</span> and <span class="highlight">production deployment</span>.
    `
  }
  
  if (lowerMessage.includes('experience') || lowerMessage.includes('level')) {
    return `
      <strong>Matthew's Experience Level:</strong><br><br>
      <strong>Senior Developer</strong> with 5+ years in software engineering<br>
      <strong>ML Engineer</strong> specializing in production AI systems<br>
      <strong>Full-Stack Expert</strong> building scalable applications<br>
      <strong>AI Researcher</strong> with published papers in NLP<br><br>
      He's worked at <span class="highlight">top tech companies</span> and <span class="highlight">startups</span>, leading AI initiatives.
    `
  }
  
  if (lowerMessage.includes('github') || lowerMessage.includes('stats')) {
    return `
      <strong>Matthew's GitHub Activity:</strong><br><br>
      📈 <strong>500+ contributions</strong> in the last year<br>
      ⭐ <strong>50+ repositories</strong> with ML focus<br>
      🔥 <strong>1000+ stars</strong> across projects<br>
      🤝 <strong>Active open source</strong> contributor<br><br>
      Check out his <span class="highlight">AI/ML projects</span> and <span class="highlight">contributions</span>!
    `
  }
  
  // Default response
  return `
    I can help you learn more about Matthew's <span class="highlight">AI/ML expertise</span>, 
    <span class="highlight">technical skills</span>, <span class="highlight">projects</span>, and 
    <span class="highlight">experience</span>. Try asking about specific areas or use the quick suggestions above!
  `
}

const sendMessage = async () => {
  if (!userInput.value.trim() || isProcessing.value) return
  
  const userMessage = userInput.value.trim()
  
  // Add user message
  messages.value.push({
    type: 'user',
    text: userMessage
  })
  
  userInput.value = ''
  isProcessing.value = true
  
  // Add AI typing indicator
  const typingMessage: ChatMessage = {
    type: 'ai',
    text: '',
    isTyping: true
  }
  messages.value.push(typingMessage)
  
  // Scroll to bottom
  await nextTick()
  scrollToBottom()
  
  try {
    // Generate AI response
    const aiResponse = await generateAIResponse(userMessage)
    
    // Replace typing indicator with actual response
    const lastMessage = messages.value[messages.value.length - 1]
    if (lastMessage.isTyping) {
      lastMessage.text = aiResponse
      lastMessage.isTyping = false
    }
  } catch (error) {
    console.error('Error generating AI response:', error)
    const lastMessage = messages.value[messages.value.length - 1]
    if (lastMessage.isTyping) {
      lastMessage.text = "I'm sorry, I encountered an error. Please try again!"
      lastMessage.isTyping = false
    }
  } finally {
    isProcessing.value = false
    await nextTick()
    scrollToBottom()
  }
}

const sendQuickMessage = (suggestion: string) => {
  userInput.value = suggestion
  sendMessage()
}

const scrollToBottom = () => {
  if (messagesContainer.value) {
    messagesContainer.value.scrollTop = messagesContainer.value.scrollHeight
  }
}

watch(messages, () => {
  nextTick(() => scrollToBottom())
}, { deep: true })

onMounted(() => {
  scrollToBottom()
})
</script>

<style scoped>
.ai-chat-interface {
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
}

.chat-container {
  background: rgba(0, 0, 0, 0.8);
  border: 1px solid rgba(56, 189, 248, 0.3);
  border-radius: 16px;
  overflow: hidden;
  backdrop-filter: blur(10px);
}

.chat-header {
  display: flex;
  align-items: center;
  padding: 20px;
  background: linear-gradient(135deg, rgba(56, 189, 248, 0.1), rgba(139, 92, 246, 0.1));
  border-bottom: 1px solid rgba(56, 189, 248, 0.2);
}

.ai-avatar {
  position: relative;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: linear-gradient(135deg, #38bdf8, #8b5cf6);
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 16px;
}

.ai-pulse {
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  border-radius: 50%;
  background: linear-gradient(135deg, #38bdf8, #8b5cf6);
  animation: pulse 2s infinite;
  opacity: 0.6;
}

@keyframes pulse {
  0% { transform: scale(1); opacity: 0.6; }
  50% { transform: scale(1.1); opacity: 0.3; }
  100% { transform: scale(1); opacity: 0.6; }
}

.ai-icon {
  font-size: 24px;
  z-index: 1;
}

.header-text {
  flex: 1;
}

.ai-title {
  color: white;
  font-size: 18px;
  font-weight: 600;
  margin: 0 0 4px 0;
}

.ai-subtitle {
  color: #94a3b8;
  font-size: 14px;
  margin: 0;
}

.chat-messages {
  height: 400px;
  overflow-y: auto;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.message {
  display: flex;
  flex-direction: column;
}

.message.user {
  align-items: flex-end;
}

.message.ai {
  align-items: flex-start;
}

.message-content {
  display: flex;
  align-items: flex-start;
  max-width: 80%;
}

.message.user .message-content {
  flex-direction: row-reverse;
}

.message-avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 16px;
  margin: 0 8px;
  flex-shrink: 0;
}

.message.user .message-avatar {
  background: linear-gradient(135deg, #38bdf8, #0ea5e9);
}

.message.ai .message-avatar {
  background: linear-gradient(135deg, #8b5cf6, #7c3aed);
}

.message-text {
  background: rgba(255, 255, 255, 0.1);
  padding: 12px 16px;
  border-radius: 18px;
  color: white;
  font-size: 14px;
  line-height: 1.5;
  backdrop-filter: blur(10px);
}

.message.user .message-text {
  background: linear-gradient(135deg, rgba(56, 189, 248, 0.2), rgba(14, 165, 233, 0.2));
  border: 1px solid rgba(56, 189, 248, 0.3);
}

.message.ai .message-text {
  background: linear-gradient(135deg, rgba(139, 92, 246, 0.2), rgba(124, 58, 237, 0.2));
  border: 1px solid rgba(139, 92, 246, 0.3);
}

.typing-indicator {
  display: flex;
  gap: 4px;
  align-items: center;
}

.typing-indicator span {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: #94a3b8;
  animation: typing 1.4s infinite ease-in-out;
}

.typing-indicator span:nth-child(1) { animation-delay: -0.32s; }
.typing-indicator span:nth-child(2) { animation-delay: -0.16s; }

@keyframes typing {
  0%, 80%, 100% { transform: scale(0.8); opacity: 0.5; }
  40% { transform: scale(1); opacity: 1; }
}

.chat-input-container {
  padding: 20px;
  border-top: 1px solid rgba(56, 189, 248, 0.2);
  background: rgba(0, 0, 0, 0.5);
}

.input-wrapper {
  display: flex;
  gap: 12px;
  margin-bottom: 16px;
}

.chat-input {
  flex: 1;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(56, 189, 248, 0.3);
  border-radius: 25px;
  padding: 12px 20px;
  color: white;
  font-size: 14px;
  outline: none;
  transition: all 0.3s ease;
}

.chat-input:focus {
  border-color: #38bdf8;
  box-shadow: 0 0 0 3px rgba(56, 189, 248, 0.1);
}

.chat-input::placeholder {
  color: #94a3b8;
}

.send-button {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background: linear-gradient(135deg, #38bdf8, #0ea5e9);
  border: none;
  color: white;
  font-size: 18px;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}

.send-button:hover:not(:disabled) {
  transform: scale(1.05);
  box-shadow: 0 0 20px rgba(56, 189, 248, 0.4);
}

.send-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.loading-spinner {
  width: 20px;
  height: 20px;
  border: 2px solid rgba(255, 255, 255, 0.3);
  border-top: 2px solid white;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.suggestions {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}

.suggestion-button {
  background: rgba(56, 189, 248, 0.1);
  border: 1px solid rgba(56, 189, 248, 0.3);
  border-radius: 20px;
  padding: 8px 16px;
  color: #38bdf8;
  font-size: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  white-space: nowrap;
}

.suggestion-button:hover:not(:disabled) {
  background: rgba(56, 189, 248, 0.2);
  border-color: #38bdf8;
  transform: translateY(-1px);
}

.suggestion-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.highlight {
  color: #38bdf8;
  font-weight: 600;
}

/* Scrollbar styling */
.chat-messages::-webkit-scrollbar {
  width: 6px;
}

.chat-messages::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 3px;
}

.chat-messages::-webkit-scrollbar-thumb {
  background: rgba(56, 189, 248, 0.5);
  border-radius: 3px;
}

.chat-messages::-webkit-scrollbar-thumb:hover {
  background: rgba(56, 189, 248, 0.7);
}
</style> 