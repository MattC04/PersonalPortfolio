<template>
  <div class="image-recognition">
    <div class="recognition-container">
      <!-- Header -->
      <div class="recognition-header">
        <div class="header-icon">🔍</div>
        <div class="header-text">
          <h3 class="recognition-title">AI Image Recognition</h3>
          <p class="recognition-subtitle">Upload an image and see AI identify objects, faces, and scenes</p>
        </div>
      </div>

      <!-- Upload Area -->
      <div class="upload-area" @click="triggerFileInput" @dragover.prevent @drop="handleDrop">
        <input 
          ref="fileInput" 
          type="file" 
          accept="image/*" 
          @change="handleFileSelect" 
          class="hidden-input"
        />
        
        <div v-if="!selectedImage" class="upload-placeholder">
          <div class="upload-icon">📸</div>
          <p class="upload-text">Click to upload or drag & drop an image</p>
          <p class="upload-hint">Supports JPG, PNG, GIF up to 10MB</p>
        </div>
        
        <div v-else class="image-preview">
          <img :src="selectedImage" alt="Preview" class="preview-image" />
          <button @click="clearImage" class="clear-button">✕</button>
        </div>
      </div>

      <!-- Recognition Results -->
      <div v-if="isProcessing" class="processing-state">
        <div class="processing-spinner"></div>
        <p class="processing-text">AI is analyzing your image...</p>
      </div>

      <div v-else-if="recognitionResults.length > 0" class="recognition-results">
        <h4 class="results-title">AI Recognition Results</h4>
        
        <div class="results-grid">
          <div 
            v-for="(result, index) in recognitionResults" 
            :key="index"
            class="result-item"
          >
            <div class="result-label">{{ result.label }}</div>
            <div class="result-confidence">
              <div class="confidence-bar">
                <div 
                  class="confidence-fill" 
                  :style="{ width: result.confidence + '%' }"
                ></div>
              </div>
              <span class="confidence-text">{{ result.confidence }}%</span>
            </div>
          </div>
        </div>
        
        <div class="ai-insights">
          <h5 class="insights-title">🤖 AI Insights</h5>
          <p class="insights-text">{{ aiInsights }}</p>
        </div>
      </div>

      <!-- Action Buttons -->
      <div class="action-buttons">
        <button 
          @click="analyzeImage" 
          :disabled="!selectedImage || isProcessing"
          class="analyze-button"
        >
          <span v-if="!isProcessing">🔍 Analyze with AI</span>
          <span v-else>Processing...</span>
        </button>
        
        <button 
          @click="clearAll" 
          :disabled="!selectedImage && recognitionResults.length === 0"
          class="clear-all-button"
        >
          Clear All
        </button>
      </div>

      <!-- Sample Images -->
      <div class="sample-images">
        <h5 class="sample-title">Try with sample images:</h5>
        <div class="sample-grid">
          <div 
            v-for="(sample, index) in sampleImages" 
            :key="index"
            class="sample-item"
            @click="loadSampleImage(sample)"
          >
            <img :src="sample.url" :alt="sample.description" class="sample-thumbnail" />
            <p class="sample-description">{{ sample.description }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

interface RecognitionResult {
  label: string
  confidence: number
}

interface SampleImage {
  url: string
  description: string
}

const fileInput = ref<HTMLInputElement>()
const selectedImage = ref<string>('')
const isProcessing = ref(false)
const recognitionResults = ref<RecognitionResult[]>([])
const aiInsights = ref('')

const sampleImages: SampleImage[] = [
  {
    url: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=150&h=150&fit=crop',
    description: 'Portrait'
  },
  {
    url: 'https://images.unsplash.com/photo-1441974231531-c6227db76b6e?w=150&h=150&fit=crop',
    description: 'Nature'
  },
  {
    url: 'https://images.unsplash.com/photo-1560472354-b33ff0c44a43?w=150&h=150&fit=crop',
    description: 'City'
  },
  {
    url: 'https://images.unsplash.com/photo-1518709268805-4e9042af2176?w=150&h=150&fit=crop',
    description: 'Food'
  }
]

const triggerFileInput = () => {
  fileInput.value?.click()
}

const handleFileSelect = (event: Event) => {
  const target = event.target as HTMLInputElement
  if (target.files && target.files[0]) {
    const file = target.files[0]
    if (file.size > 10 * 1024 * 1024) {
      alert('File size must be less than 10MB')
      return
    }
    const reader = new FileReader()
    reader.onload = (e) => {
      selectedImage.value = e.target?.result as string
      clearResults()
    }
    reader.readAsDataURL(file)
  }
}

const handleDrop = (event: DragEvent) => {
  event.preventDefault()
  const files = event.dataTransfer?.files
  if (files && files[0]) {
    const file = files[0]
    if (file.type.startsWith('image/')) {
      if (file.size > 10 * 1024 * 1024) {
        alert('File size must be less than 10MB')
        return
      }
      const reader = new FileReader()
      reader.onload = (e) => {
        selectedImage.value = e.target?.result as string
        clearResults()
      }
      reader.readAsDataURL(file)
    }
  }
}

const loadSampleImage = (sample: SampleImage) => {
  selectedImage.value = sample.url
  clearResults()
}

const clearImage = () => {
  selectedImage.value = ''
  clearResults()
}

const clearResults = () => {
  recognitionResults.value = []
  aiInsights.value = ''
}

const clearAll = () => {
  selectedImage.value = ''
  clearResults()
}

const analyzeImage = async () => {
  if (!selectedImage.value) return
  
  isProcessing.value = true
  clearResults()
  
  try {
    // Simulate AI processing delay
    await new Promise(resolve => setTimeout(resolve, 2000 + Math.random() * 2000))
    
    // Simulate AI recognition results - in a real app, this would call an actual ML model
    const mockResults: RecognitionResult[] = [
      { label: 'Person', confidence: 95 },
      { label: 'Portrait', confidence: 87 },
      { label: 'Human face', confidence: 82 },
      { label: 'Photography', confidence: 78 },
      { label: 'Professional', confidence: 65 }
    ]
    
    recognitionResults.value = mockResults
    
    // Generate AI insights based on results
    aiInsights.value = generateAIInsights(mockResults)
    
  } catch (error) {
    console.error('Error analyzing image:', error)
    alert('Error analyzing image. Please try again.')
  } finally {
    isProcessing.value = false
  }
}

const generateAIInsights = (results: RecognitionResult[]): string => {
  const topResult = results[0]
  
  if (topResult.label.toLowerCase().includes('person') || topResult.label.toLowerCase().includes('face')) {
    return "This image contains a human subject with high confidence. The AI detected facial features, suggesting this is a portrait or professional photograph. The lighting and composition appear well-balanced."
  } else if (topResult.label.toLowerCase().includes('nature')) {
    return "The AI identified natural elements in this image. The composition suggests outdoor photography with natural lighting conditions. Consider the rule of thirds for enhanced visual appeal."
  } else if (topResult.label.toLowerCase().includes('city')) {
    return "Urban architecture detected! The AI recognizes man-made structures and cityscapes. The image likely contains geometric patterns and urban textures that are characteristic of city photography."
  } else if (topResult.label.toLowerCase().includes('food')) {
    return "Culinary content identified! The AI detected food-related elements. Food photography often benefits from close-up shots, good lighting, and appealing presentation to showcase textures and colors."
  }
  
  return "The AI has analyzed this image and provided detailed recognition results. The confidence levels indicate how certain the model is about each identified element. Higher confidence suggests more reliable detection."
}
</script>

<style scoped>
.image-recognition {
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
}

.recognition-container {
  background: rgba(0, 0, 0, 0.8);
  border: 1px solid rgba(56, 189, 248, 0.3);
  border-radius: 16px;
  overflow: hidden;
  backdrop-filter: blur(10px);
}

.recognition-header {
  display: flex;
  align-items: center;
  padding: 20px;
  background: linear-gradient(135deg, rgba(56, 189, 248, 0.1), rgba(139, 92, 246, 0.1));
  border-bottom: 1px solid rgba(56, 189, 248, 0.2);
}

.header-icon {
  font-size: 32px;
  margin-right: 16px;
}

.header-text {
  flex: 1;
}

.recognition-title {
  color: white;
  font-size: 20px;
  font-weight: 600;
  margin: 0 0 4px 0;
}

.recognition-subtitle {
  color: #94a3b8;
  font-size: 14px;
  margin: 0;
}

.upload-area {
  padding: 40px;
  text-align: center;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 2px dashed rgba(56, 189, 248, 0.3);
  margin: 20px;
  border-radius: 12px;
}

.upload-area:hover {
  border-color: #38bdf8;
  background: rgba(56, 189, 248, 0.05);
}

.hidden-input {
  display: none;
}

.upload-placeholder {
  color: #94a3b8;
}

.upload-icon {
  font-size: 48px;
  margin-bottom: 16px;
}

.upload-text {
  font-size: 18px;
  margin: 0 0 8px 0;
  color: white;
}

.upload-hint {
  font-size: 14px;
  margin: 0;
  opacity: 0.7;
}

.image-preview {
  position: relative;
  display: inline-block;
}

.preview-image {
  max-width: 300px;
  max-height: 300px;
  border-radius: 8px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
}

.clear-button {
  position: absolute;
  top: -10px;
  right: -10px;
  width: 30px;
  height: 30px;
  border-radius: 50%;
  background: #ef4444;
  border: none;
  color: white;
  font-size: 16px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.clear-button:hover {
  background: #dc2626;
  transform: scale(1.1);
}

.processing-state {
  padding: 40px;
  text-align: center;
  color: white;
}

.processing-spinner {
  width: 40px;
  height: 40px;
  border: 4px solid rgba(56, 189, 248, 0.3);
  border-top: 4px solid #38bdf8;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin: 0 auto 16px;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.processing-text {
  font-size: 16px;
  margin: 0;
  color: #94a3b8;
}

.recognition-results {
  padding: 20px;
}

.results-title {
  color: white;
  font-size: 18px;
  font-weight: 600;
  margin: 0 0 20px 0;
  text-align: center;
}

.results-grid {
  display: grid;
  gap: 16px;
  margin-bottom: 24px;
}

.result-item {
  background: rgba(255, 255, 255, 0.05);
  padding: 16px;
  border-radius: 8px;
  border: 1px solid rgba(56, 189, 248, 0.2);
}

.result-label {
  color: white;
  font-weight: 500;
  margin-bottom: 8px;
}

.result-confidence {
  display: flex;
  align-items: center;
  gap: 12px;
}

.confidence-bar {
  flex: 1;
  height: 8px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 4px;
  overflow: hidden;
}

.confidence-fill {
  height: 100%;
  background: linear-gradient(90deg, #38bdf8, #8b5cf6);
  transition: width 0.5s ease;
}

.confidence-text {
  color: #38bdf8;
  font-weight: 600;
  min-width: 40px;
}

.ai-insights {
  background: linear-gradient(135deg, rgba(56, 189, 248, 0.1), rgba(139, 92, 246, 0.1));
  padding: 20px;
  border-radius: 8px;
  border: 1px solid rgba(56, 189, 248, 0.2);
}

.insights-title {
  color: white;
  font-size: 16px;
  font-weight: 600;
  margin: 0 0 12px 0;
}

.insights-text {
  color: #94a3b8;
  font-size: 14px;
  line-height: 1.6;
  margin: 0;
}

.action-buttons {
  display: flex;
  gap: 16px;
  justify-content: center;
  padding: 20px;
}

.analyze-button {
  background: linear-gradient(135deg, #38bdf8, #0ea5e9);
  border: none;
  color: white;
  padding: 12px 24px;
  border-radius: 25px;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
}

.analyze-button:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(56, 189, 248, 0.3);
}

.analyze-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.clear-all-button {
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  color: #94a3b8;
  padding: 12px 24px;
  border-radius: 25px;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
}

.clear-all-button:hover:not(:disabled) {
  background: rgba(255, 255, 255, 0.2);
  color: white;
}

.clear-all-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.sample-images {
  padding: 20px;
  border-top: 1px solid rgba(56, 189, 248, 0.2);
  background: rgba(0, 0, 0, 0.3);
}

.sample-title {
  color: white;
  font-size: 16px;
  font-weight: 600;
  margin: 0 0 16px 0;
  text-align: center;
}

.sample-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
  gap: 16px;
}

.sample-item {
  text-align: center;
  cursor: pointer;
  transition: all 0.3s ease;
  padding: 12px;
  border-radius: 8px;
}

.sample-item:hover {
  background: rgba(56, 189, 248, 0.1);
  transform: translateY(-2px);
}

.sample-thumbnail {
  width: 100%;
  height: 80px;
  object-fit: cover;
  border-radius: 6px;
  margin-bottom: 8px;
}

.sample-description {
  color: #94a3b8;
  font-size: 12px;
  margin: 0;
}
</style> 