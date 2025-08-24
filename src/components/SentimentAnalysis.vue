<template>
  <div class="sentiment-analysis">
    <div class="analysis-container">
      <!-- Header -->
      <div class="analysis-header">
        <div class="header-icon">📊</div>
        <div class="header-text">
          <h3 class="analysis-title">AI Sentiment Analysis</h3>
          <p class="analysis-subtitle">Analyze the emotional tone and sentiment of any text</p>
        </div>
      </div>

      <!-- Input Area -->
      <div class="input-area">
        <textarea 
          v-model="inputText" 
          placeholder="Enter text to analyze sentiment (e.g., 'I love this amazing product! It works perfectly and makes me so happy.')"
          class="text-input"
          rows="4"
        ></textarea>
        
        <div class="input-stats">
          <span class="stat-item">
            <span class="stat-label">Characters:</span>
            <span class="stat-value">{{ inputText.length }}</span>
          </span>
          <span class="stat-item">
            <span class="stat-label">Words:</span>
            <span class="stat-value">{{ wordCount }}</span>
          </span>
          <span class="stat-item">
            <span class="stat-label">Sentences:</span>
            <span class="stat-value">{{ sentenceCount }}</span>
          </span>
        </div>
      </div>

      <!-- Analysis Button -->
      <div class="action-area">
        <button 
          @click="analyzeSentiment" 
          :disabled="!inputText.trim() || isProcessing"
          class="analyze-button"
        >
          <span v-if="!isProcessing">🔍 Analyze Sentiment</span>
          <span v-else>Processing...</span>
        </button>
        
        <button 
          @click="clearAnalysis" 
          :disabled="!hasResults"
          class="clear-button"
        >
          Clear Results
        </button>
      </div>

      <!-- Processing State -->
      <div v-if="isProcessing" class="processing-state">
        <div class="processing-spinner"></div>
        <p class="processing-text">AI is analyzing the sentiment...</p>
      </div>

      <!-- Results -->
      <div v-else-if="hasResults" class="analysis-results">
        <h4 class="results-title">Sentiment Analysis Results</h4>
        
        <!-- Overall Sentiment -->
        <div class="overall-sentiment">
          <div class="sentiment-score">
            <div class="score-circle" :class="sentimentClass">
              <span class="score-value">{{ sentimentScore }}</span>
            </div>
            <div class="score-label">{{ sentimentLabel }}</div>
          </div>
          
          <div class="sentiment-breakdown">
            <div class="breakdown-item">
              <span class="breakdown-label">Positive:</span>
              <div class="breakdown-bar">
                <div class="breakdown-fill positive" :style="{ width: positiveScore + '%' }"></div>
              </div>
              <span class="breakdown-value">{{ positiveScore }}%</span>
            </div>
            
            <div class="breakdown-item">
              <span class="breakdown-label">Neutral:</span>
              <div class="breakdown-bar">
                <div class="breakdown-fill neutral" :style="{ width: neutralScore + '%' }"></div>
              </div>
              <span class="breakdown-value">{{ neutralScore }}%</span>
            </div>
            
            <div class="breakdown-item">
              <span class="breakdown-label">Negative:</span>
              <div class="breakdown-bar">
                <div class="breakdown-fill negative" :style="{ width: negativeScore + '%' }"></div>
              </div>
              <span class="breakdown-value">{{ negativeScore }}%</span>
            </div>
          </div>
        </div>
        
        <!-- Detailed Analysis -->
        <div class="detailed-analysis">
          <h5 class="detailed-title">🤖 AI Analysis Details</h5>
          
          <div class="analysis-grid">
            <div class="analysis-item">
              <div class="item-icon">🎯</div>
              <div class="item-content">
                <h6 class="item-title">Primary Emotion</h6>
                <p class="item-value">{{ primaryEmotion }}</p>
              </div>
            </div>
            
            <div class="analysis-item">
              <div class="item-icon">💬</div>
              <div class="item-content">
                <h6 class="item-title">Tone</h6>
                <p class="item-value">{{ tone }}</p>
              </div>
            </div>
            
            <div class="analysis-item">
              <div class="item-icon">📈</div>
              <div class="item-content">
                <h6 class="item-title">Confidence</h6>
                <p class="item-value">{{ confidence }}%</p>
              </div>
            </div>
            
            <div class="analysis-item">
              <div class="item-icon">🔍</div>
              <div class="item-content">
                <h6 class="item-title">Key Phrases</h6>
                <p class="item-value">{{ keyPhrases.join(', ') }}</p>
              </div>
            </div>
          </div>
          
          <div class="ai-insights">
            <h6 class="insights-title">AI Insights</h6>
            <p class="insights-text">{{ aiInsights }}</p>
          </div>
        </div>
      </div>

      <!-- Sample Texts -->
      <div class="sample-texts">
        <h5 class="sample-title">Try with sample texts:</h5>
        <div class="sample-grid">
          <div 
            v-for="(sample, index) in sampleTexts" 
            :key="index"
            class="sample-item"
            @click="loadSampleText(sample)"
          >
            <div class="sample-icon">{{ sample.icon }}</div>
            <p class="sample-text">{{ sample.text }}</p>
            <span class="sample-sentiment">{{ sample.sentiment }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

interface SampleText {
  text: string
  sentiment: string
  icon: string
}

const inputText = ref('')
const isProcessing = ref(false)
const hasResults = ref(false)

// Sentiment results
const sentimentScore = ref(0)
const positiveScore = ref(0)
const neutralScore = ref(0)
const negativeScore = ref(0)
const primaryEmotion = ref('')
const tone = ref('')
const confidence = ref(0)
const keyPhrases = ref<string[]>([])
const aiInsights = ref('')

const sampleTexts: SampleText[] = [
  {
    text: "I absolutely love this product! It's amazing and works perfectly. Best purchase ever!",
    sentiment: "Very Positive",
    icon: "😍"
  },
  {
    text: "This is okay, nothing special. It works but I expected more for the price.",
    sentiment: "Neutral",
    icon: "😐"
  },
  {
    text: "Terrible product! Complete waste of money. I'm very disappointed and frustrated.",
    sentiment: "Very Negative",
    icon: "😠"
  },
  {
    text: "Great service and fast delivery. The quality is good, though it could be better.",
    sentiment: "Positive",
    icon: "👍"
  }
]

const wordCount = computed(() => {
  return inputText.value.trim() ? inputText.value.trim().split(/\s+/).length : 0
})

const sentenceCount = computed(() => {
  return inputText.value.trim() ? inputText.value.split(/[.!?]+/).filter(s => s.trim()).length : 0
})

const sentimentClass = computed(() => {
  if (sentimentScore.value >= 0.6) return 'very-positive'
  if (sentimentScore.value >= 0.2) return 'positive'
  if (sentimentScore.value >= -0.2) return 'neutral'
  if (sentimentScore.value >= -0.6) return 'negative'
  return 'very-negative'
})

const sentimentLabel = computed(() => {
  if (sentimentScore.value >= 0.6) return 'Very Positive'
  if (sentimentScore.value >= 0.2) return 'Positive'
  if (sentimentScore.value >= -0.2) return 'Neutral'
  if (sentimentScore.value >= -0.6) return 'Negative'
  return 'Very Negative'
})

const loadSampleText = (sample: SampleText) => {
  inputText.value = sample.text
  clearAnalysis()
}

const clearAnalysis = () => {
  hasResults.value = false
  sentimentScore.value = 0
  positiveScore.value = 0
  neutralScore.value = 0
  negativeScore.value = 0
  primaryEmotion.value = ''
  tone.value = ''
  confidence.value = 0
  keyPhrases.value = []
  aiInsights.value = ''
}

const analyzeSentiment = async () => {
  if (!inputText.value.trim()) return
  
  isProcessing.value = true
  clearAnalysis()
  
  try {
    // Simulate AI processing delay
    await new Promise(resolve => setTimeout(resolve, 1500 + Math.random() * 1000))
    
    // Simulate AI sentiment analysis - in a real app, this would call an actual ML model
    const text = inputText.value.toLowerCase()
    
    // Simple rule-based sentiment analysis for demo purposes
    let positiveWords = 0
    let negativeWords = 0
    let totalWords = wordCount.value
    
    const positiveKeywords = ['love', 'amazing', 'great', 'excellent', 'perfect', 'wonderful', 'fantastic', 'awesome', 'good', 'best', 'happy', 'joy', 'pleased', 'satisfied']
    const negativeKeywords = ['hate', 'terrible', 'awful', 'horrible', 'bad', 'worst', 'disappointed', 'frustrated', 'angry', 'sad', 'upset', 'annoyed', 'disgusted']
    
    positiveKeywords.forEach(word => {
      const regex = new RegExp(`\\b${word}\\b`, 'g')
      const matches = text.match(regex)
      if (matches) positiveWords += matches.length
    })
    
    negativeKeywords.forEach(word => {
      const regex = new RegExp(`\\b${word}\\b`, 'g')
      const matches = text.match(regex)
      if (matches) negativeWords += matches.length
    })
    
    // Calculate scores
    positiveScore.value = Math.round((positiveWords / totalWords) * 100)
    negativeScore.value = Math.round((negativeWords / totalWords) * 100)
    neutralScore.value = Math.round(100 - positiveScore.value - negativeScore.value)
    
    // Ensure neutral score doesn't go negative
    if (neutralScore.value < 0) neutralScore.value = 0
    
    // Calculate overall sentiment score (-1 to 1)
    sentimentScore.value = parseFloat(((positiveWords - negativeWords) / totalWords).toFixed(2))
    
    // Generate other analysis data
    primaryEmotion.value = generatePrimaryEmotion(sentimentScore.value)
    tone.value = generateTone(sentimentScore.value)
    confidence.value = Math.round(70 + Math.random() * 25) // 70-95%
    keyPhrases.value = extractKeyPhrases(text)
    aiInsights.value = generateAIInsights(sentimentScore.value, positiveScore.value, negativeScore.value)
    
    hasResults.value = true
    
  } catch (error) {
    console.error('Error analyzing sentiment:', error)
    alert('Error analyzing sentiment. Please try again.')
  } finally {
    isProcessing.value = false
  }
}

const generatePrimaryEmotion = (score: number): string => {
  if (score >= 0.6) return 'Joy'
  if (score >= 0.2) return 'Satisfaction'
  if (score >= -0.2) return 'Indifference'
  if (score >= -0.6) return 'Disappointment'
  return 'Frustration'
}

const generateTone = (score: number): string => {
  if (score >= 0.6) return 'Enthusiastic'
  if (score >= 0.2) return 'Positive'
  if (score >= -0.2) return 'Neutral'
  if (score >= -0.6) return 'Critical'
  return 'Hostile'
}

const extractKeyPhrases = (text: string): string[] => {
  const phrases = []
  const words = text.split(/\s+/)
  
  // Extract 2-3 word phrases that might be significant
  for (let i = 0; i < words.length - 1; i++) {
    if (words[i].length > 3 && words[i + 1].length > 3) {
      phrases.push(`${words[i]} ${words[i + 1]}`)
    }
  }
  
  return phrases.slice(0, 3) // Return first 3 phrases
}

const generateAIInsights = (score: number, positive: number, negative: number): string => {
  if (score >= 0.6) {
    return "The text expresses strong positive sentiment with enthusiastic language. The author is clearly satisfied and happy with their experience, using emotionally charged positive words."
  } else if (score >= 0.2) {
    return "Overall positive sentiment detected. The text contains more positive than negative language, suggesting general satisfaction with some room for improvement."
  } else if (score >= -0.2) {
    return "Neutral sentiment detected. The text appears balanced without strong emotional language, suggesting a factual or indifferent tone."
  } else if (score >= -0.6) {
    return "Negative sentiment detected. The text contains critical language and expressions of dissatisfaction, though not extremely hostile."
  } else {
    return "Strong negative sentiment detected. The text uses highly critical and emotionally charged negative language, indicating significant dissatisfaction or frustration."
  }
}
</script>

<style scoped>
.sentiment-analysis {
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
}

.analysis-container {
  background: rgba(0, 0, 0, 0.8);
  border: 1px solid rgba(56, 189, 248, 0.3);
  border-radius: 16px;
  overflow: hidden;
  backdrop-filter: blur(10px);
}

.analysis-header {
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

.analysis-title {
  color: white;
  font-size: 20px;
  font-weight: 600;
  margin: 0 0 4px 0;
}

.analysis-subtitle {
  color: #94a3b8;
  font-size: 14px;
  margin: 0;
}

.input-area {
  padding: 20px;
}

.text-input {
  width: 100%;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(56, 189, 248, 0.3);
  border-radius: 8px;
  padding: 16px;
  color: white;
  font-size: 14px;
  line-height: 1.5;
  outline: none;
  transition: all 0.3s ease;
  resize: vertical;
  min-height: 100px;
}

.text-input:focus {
  border-color: #38bdf8;
  box-shadow: 0 0 0 3px rgba(56, 189, 248, 0.1);
}

.text-input::placeholder {
  color: #94a3b8;
}

.input-stats {
  display: flex;
  gap: 20px;
  margin-top: 12px;
  justify-content: center;
}

.stat-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
}

.stat-label {
  color: #94a3b8;
  font-size: 12px;
}

.stat-value {
  color: #38bdf8;
  font-weight: 600;
  font-size: 16px;
}

.action-area {
  display: flex;
  gap: 16px;
  justify-content: center;
  padding: 20px;
  border-top: 1px solid rgba(56, 189, 248, 0.2);
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

.clear-button {
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

.clear-button:hover:not(:disabled) {
  background: rgba(255, 255, 255, 0.2);
  color: white;
}

.clear-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
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

.analysis-results {
  padding: 20px;
}

.results-title {
  color: white;
  font-size: 18px;
  font-weight: 600;
  margin: 0 0 20px 0;
  text-align: center;
}

.overall-sentiment {
  display: flex;
  align-items: center;
  gap: 40px;
  margin-bottom: 30px;
  justify-content: center;
}

.sentiment-score {
  text-align: center;
}

.score-circle {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 12px;
  font-size: 24px;
  font-weight: bold;
  color: white;
  transition: all 0.3s ease;
}

.score-circle.very-positive { background: linear-gradient(135deg, #10b981, #059669); }
.score-circle.positive { background: linear-gradient(135deg, #38bdf8, #0ea5e9); }
.score-circle.neutral { background: linear-gradient(135deg, #94a3b8, #64748b); }
.score-circle.negative { background: linear-gradient(135deg, #f59e0b, #d97706); }
.score-circle.very-negative { background: linear-gradient(135deg, #ef4444, #dc2626); }

.score-value {
  font-size: 32px;
}

.score-label {
  color: white;
  font-weight: 600;
  font-size: 16px;
}

.sentiment-breakdown {
  flex: 1;
  max-width: 300px;
}

.breakdown-item {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 12px;
}

.breakdown-label {
  color: white;
  font-size: 14px;
  min-width: 70px;
}

.breakdown-bar {
  flex: 1;
  height: 8px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 4px;
  overflow: hidden;
}

.breakdown-fill {
  height: 100%;
  transition: width 0.5s ease;
}

.breakdown-fill.positive { background: #10b981; }
.breakdown-fill.neutral { background: #94a3b8; }
.breakdown-fill.negative { background: #ef4444; }

.breakdown-value {
  color: white;
  font-weight: 600;
  min-width: 40px;
  text-align: right;
}

.detailed-analysis {
  background: linear-gradient(135deg, rgba(56, 189, 248, 0.1), rgba(139, 92, 246, 0.1));
  padding: 20px;
  border-radius: 8px;
  border: 1px solid rgba(56, 189, 248, 0.2);
}

.detailed-title {
  color: white;
  font-size: 16px;
  font-weight: 600;
  margin: 0 0 20px 0;
}

.analysis-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
  margin-bottom: 20px;
}

.analysis-item {
  display: flex;
  align-items: center;
  gap: 12px;
}

.item-icon {
  font-size: 24px;
  width: 40px;
  text-align: center;
}

.item-content {
  flex: 1;
}

.item-title {
  color: #94a3b8;
  font-size: 12px;
  margin: 0 0 4px 0;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.item-value {
  color: white;
  font-weight: 600;
  margin: 0;
}

.ai-insights {
  background: rgba(0, 0, 0, 0.3);
  padding: 16px;
  border-radius: 6px;
}

.insights-title {
  color: #38bdf8;
  font-size: 14px;
  font-weight: 600;
  margin: 0 0 8px 0;
}

.insights-text {
  color: #94a3b8;
  font-size: 14px;
  line-height: 1.6;
  margin: 0;
}

.sample-texts {
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
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 16px;
}

.sample-item {
  background: rgba(255, 255, 255, 0.05);
  padding: 16px;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 1px solid rgba(56, 189, 248, 0.2);
}

.sample-item:hover {
  background: rgba(56, 189, 248, 0.1);
  transform: translateY(-2px);
  border-color: #38bdf8;
}

.sample-icon {
  font-size: 24px;
  margin-bottom: 8px;
}

.sample-text {
  color: white;
  font-size: 12px;
  line-height: 1.4;
  margin: 0 0 8px 0;
}

.sample-sentiment {
  color: #38bdf8;
  font-size: 11px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}
</style> 