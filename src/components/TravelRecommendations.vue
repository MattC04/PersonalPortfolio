<template>
  <div class="travel-recommendations">
    <div class="recommendations-container">
      <!-- Header -->
      <div class="recommendations-header">
        <div class="header-icon">✈️</div>
        <div class="header-text">
          <h3 class="recommendations-title">AI Travel Recommendations</h3>
          <p class="recommendations-subtitle">Get personalized travel suggestions based on your location, time, and preferences</p>
        </div>
      </div>

      <!-- Location Input -->
      <div class="location-input-section">
        <div class="input-group">
          <label class="input-label">📍 Current Location</label>
          <input 
            v-model="currentLocation" 
            placeholder="Enter city, country, or coordinates"
            class="location-input"
            @input="debounceSearch"
          />
          <div v-if="locationSuggestions.length > 0" class="suggestions-dropdown">
            <div 
              v-for="suggestion in locationSuggestions" 
              :key="suggestion.id"
              @click="selectLocation(suggestion)"
              class="suggestion-item"
            >
              <span class="suggestion-name">{{ suggestion.name }}</span>
              <span class="suggestion-country">{{ suggestion.country }}</span>
            </div>
          </div>
        </div>
        
        <div class="input-group">
          <label class="input-label">📅 Travel Time</label>
          <select v-model="travelTime" class="time-select">
            <option value="">Select travel time</option>
            <option value="now">Right Now</option>
            <option value="weekend">This Weekend</option>
            <option value="next-week">Next Week</option>
            <option value="next-month">Next Month</option>
            <option value="seasonal">Seasonal (3-6 months)</option>
          </select>
        </div>
        
        <div class="input-group">
          <label class="input-label">🎯 Travel Style</label>
          <div class="style-options">
            <label class="style-option">
              <input type="checkbox" v-model="travelStyles" value="adventure" />
              <span>Adventure</span>
            </label>
            <label class="style-option">
              <input type="checkbox" v-model="travelStyles" value="culture" />
              <span>Culture</span>
            </label>
            <label class="style-option">
              <input type="checkbox" v-model="travelStyles" value="relaxation" />
              <span>Relaxation</span>
            </label>
            <label class="style-option">
              <input type="checkbox" v-model="travelStyles" value="food" />
              <span>Food & Dining</span>
            </label>
            <label class="style-option">
              <input type="checkbox" v-model="travelStyles" value="nature" />
              <span>Nature</span>
            </label>
            <label class="style-option">
              <input type="checkbox" v-model="travelStyles" value="urban" />
              <span>Urban Exploration</span>
            </label>
          </div>
        </div>
        
        <button 
          @click="generateRecommendations" 
          :disabled="!canGenerateRecommendations"
          class="generate-button"
        >
          <span v-if="!isGenerating">🚀 Generate AI Recommendations</span>
          <span v-else>Analyzing...</span>
        </button>
      </div>

      <!-- AI Analysis Progress -->
      <div v-if="isGenerating" class="analysis-progress">
        <div class="progress-header">
          <div class="ai-avatar">🤖</div>
          <div class="progress-text">
            <h4>AI Travel Analysis in Progress</h4>
            <p>{{ currentAnalysisStep }}</p>
          </div>
        </div>
        
        <div class="progress-steps">
          <div 
            v-for="(step, index) in analysisSteps" 
            :key="index"
            :class="['progress-step', { 
              'completed': index < currentStepIndex,
              'current': index === currentStepIndex,
              'pending': index > currentStepIndex
            }]"
          >
            <div class="step-icon">
              {{ step.completed ? '✅' : step.current ? '🔄' : '⏳' }}
            </div>
            <span class="step-text">{{ step.text }}</span>
          </div>
        </div>
      </div>

      <!-- Recommendations Results -->
      <div v-if="recommendations.length > 0" class="recommendations-results">
        <h4 class="results-title">🎯 AI-Generated Travel Recommendations</h4>
        
        <div class="recommendations-grid">
          <div 
            v-for="(rec, index) in recommendations" 
            :key="index"
            class="recommendation-card"
            :class="rec.priority"
          >
            <div class="card-header">
              <div class="location-info">
                <h5 class="destination-name">{{ rec.destination }}</h5>
                <p class="destination-details">{{ rec.country }} • {{ rec.distance }}km away</p>
              </div>
              <div class="priority-badge" :class="rec.priority">
                {{ rec.priority === 'high' ? '🔥 Hot' : rec.priority === 'medium' ? '⭐ Good' : '💡 Hidden' }}
              </div>
            </div>
            
            <div class="card-content">
              <div class="recommendation-reasons">
                <h6>Why This Destination:</h6>
                <ul>
                  <li v-for="reason in rec.reasons" :key="reason">{{ reason }}</li>
                </ul>
              </div>
              
              <div class="travel-insights">
                <div class="insight-item">
                  <span class="insight-label">🌡️ Weather:</span>
                  <span class="insight-value">{{ rec.weather }}</span>
                </div>
                <div class="insight-item">
                  <span class="insight-label">💰 Cost:</span>
                  <span class="insight-value">{{ rec.cost }}</span>
                </div>
                <div class="insight-item">
                  <span class="insight-label">👥 Crowds:</span>
                  <span class="insight-value">{{ rec.crowds }}</span>
                </div>
                <div class="insight-item">
                  <span class="insight-label">🎭 Activities:</span>
                  <span class="insight-value">{{ rec.activities.join(', ') }}</span>
                </div>
              </div>
              
              <div class="ai-confidence">
                <span class="confidence-label">AI Confidence:</span>
                <div class="confidence-bar">
                  <div 
                    class="confidence-fill" 
                    :style="{ width: rec.confidence + '%' }"
                  ></div>
                </div>
                <span class="confidence-value">{{ rec.confidence }}%</span>
              </div>
            </div>
            
            <div class="card-actions">
              <button class="action-button primary">View Details</button>
              <button class="action-button secondary">Save to List</button>
            </div>
          </div>
        </div>
        
        <!-- AI Insights Summary -->
        <div class="ai-insights-summary">
          <h5 class="insights-title">🤖 AI Travel Analysis Summary</h5>
          <div class="insights-grid">
            <div class="insight-card">
              <div class="insight-icon">🌍</div>
              <h6>Geographic Pattern</h6>
              <p>{{ aiInsights.geographic }}</p>
            </div>
            <div class="insight-card">
              <div class="insight-icon">⏰</div>
              <h6>Temporal Analysis</h6>
              <p>{{ aiInsights.temporal }}</p>
            </div>
            <div class="insight-card">
              <div class="insight-icon">🎯</div>
              <h6>Preference Match</h6>
              <p>{{ aiInsights.preferences }}</p>
            </div>
            <div class="insight-card">
              <div class="insight-icon">📊</div>
              <h6>Trend Analysis</h6>
              <p>{{ aiInsights.trends }}</p>
            </div>
          </div>
        </div>
      </div>

      <!-- Sample Travel Data -->
      <div class="sample-data">
        <h5 class="sample-title">Try with sample locations:</h5>
        <div class="sample-locations">
          <button 
            v-for="sample in sampleLocations" 
            :key="sample.name"
            @click="loadSampleData(sample)"
            class="sample-location-button"
          >
            <span class="sample-icon">{{ sample.icon }}</span>
            <span class="sample-name">{{ sample.name }}</span>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

interface LocationSuggestion {
  id: string
  name: string
  country: string
  coordinates: [number, number]
}

interface Recommendation {
  destination: string
  country: string
  distance: number
  priority: 'high' | 'medium' | 'low'
  reasons: string[]
  weather: string
  cost: string
  crowds: string
  activities: string[]
  confidence: number
}

interface AIInsights {
  geographic: string
  temporal: string
  preferences: string
  trends: string
}

const currentLocation = ref('')
const travelTime = ref('')
const travelStyles = ref<string[]>([])
const locationSuggestions = ref<LocationSuggestion[]>([])
const isGenerating = ref(false)
const recommendations = ref<Recommendation[]>([])
const aiInsights = ref<AIInsights>({
  geographic: '',
  temporal: '',
  preferences: '',
  trends: ''
})

const currentStepIndex = ref(0)
const currentAnalysisStep = ref('')

const analysisSteps = [
  { text: 'Analyzing location data', completed: false, current: false },
  { text: 'Processing temporal patterns', completed: false, current: false },
  { text: 'Matching travel preferences', completed: false, current: false },
  { text: 'Generating recommendations', completed: false, current: false },
  { text: 'Applying AI insights', completed: false, current: false }
]

const sampleLocations = [
  { name: 'New York', icon: '🗽', data: 'new-york' },
  { name: 'Tokyo', icon: '🗾', data: 'tokyo' },
  { name: 'Paris', icon: '🗼', data: 'paris' },
  { name: 'Sydney', icon: '🦘', data: 'sydney' },
  { name: 'Cape Town', icon: '🏔️', data: 'cape-town' }
]

const canGenerateRecommendations = computed(() => {
  return currentLocation.value.trim() && travelTime.value && travelStyles.value.length > 0
})

// Simulate location search with debouncing
let searchTimeout: number

const debounceSearch = () => {
  clearTimeout(searchTimeout)
  searchTimeout = setTimeout(() => {
    if (currentLocation.value.trim()) {
      searchLocations()
    } else {
      locationSuggestions.value = []
    }
  }, 300)
}

const searchLocations = () => {
  // Simulate location search API
  const mockSuggestions: LocationSuggestion[] = [
    { id: '1', name: 'New York', country: 'United States', coordinates: [40.7128, -74.0060] },
    { id: '2', name: 'New York Mills', country: 'United States', coordinates: [46.5189, -95.3714] },
    { id: '3', name: 'New York City', country: 'United States', coordinates: [40.7128, -74.0060] },
    { id: '4', name: 'New York State', country: 'United States', coordinates: [42.1657, -74.9481] }
  ]
  
  locationSuggestions.value = mockSuggestions.filter(suggestion => 
    suggestion.name.toLowerCase().includes(currentLocation.value.toLowerCase()) ||
    suggestion.country.toLowerCase().includes(currentLocation.value.toLowerCase())
  )
}

const selectLocation = (suggestion: LocationSuggestion) => {
  currentLocation.value = suggestion.name
  locationSuggestions.value = []
}

const loadSampleData = (sample: any) => {
  currentLocation.value = sample.name
  travelTime.value = 'next-month'
  travelStyles.value = ['culture', 'food', 'urban']
  generateRecommendations()
}

const generateRecommendations = async () => {
  if (!canGenerateRecommendations.value) return
  
  isGenerating.value = true
  recommendations.value = []
  currentStepIndex.value = 0
  
  // Simulate AI analysis steps
  for (let i = 0; i < analysisSteps.length; i++) {
    currentStepIndex.value = i
    currentAnalysisStep.value = analysisSteps[i].text
    
    // Update step status
    analysisSteps[i].current = true
    if (i > 0) analysisSteps[i - 1].completed = true
    
    // Simulate processing time
    await new Promise(resolve => setTimeout(resolve, 800 + Math.random() * 400))
  }
  
  // Mark all steps as completed
  analysisSteps.forEach(step => {
    step.completed = true
    step.current = false
  })
  
  // Generate mock recommendations based on inputs
  generateMockRecommendations()
  
  isGenerating.value = false
}

const generateMockRecommendations = () => {
  const baseDestinations = [
    { name: 'Barcelona', country: 'Spain', distance: 1200, weather: 'Sunny, 25°C', cost: 'Medium', crowds: 'Moderate', activities: ['Beach', 'Architecture', 'Food'] },
    { name: 'Kyoto', country: 'Japan', distance: 2800, weather: 'Mild, 22°C', cost: 'Medium-High', crowds: 'Low', activities: ['Temples', 'Gardens', 'Culture'] },
    { name: 'Portland', country: 'USA', distance: 800, weather: 'Rainy, 18°C', cost: 'Medium', crowds: 'Low', activities: ['Food', 'Nature', 'Art'] },
    { name: 'Reykjavik', country: 'Iceland', distance: 3200, weather: 'Cool, 15°C', cost: 'High', crowds: 'Low', activities: ['Northern Lights', 'Hot Springs', 'Adventure'] },
    { name: 'Marrakech', country: 'Morocco', distance: 1600, weather: 'Warm, 28°C', cost: 'Low', crowds: 'Moderate', activities: ['Markets', 'Culture', 'Desert'] },
    { name: 'Queenstown', country: 'New Zealand', distance: 4200, weather: 'Cool, 16°C', cost: 'Medium-High', crowds: 'Low', activities: ['Adventure', 'Nature', 'Scenery'] }
  ]
  
  // Filter and rank destinations based on inputs
  const filteredDestinations = baseDestinations
    .filter(dest => {
      // Apply travel style filters
      if (travelStyles.value.includes('adventure') && !dest.activities.includes('Adventure')) return false
      if (travelStyles.value.includes('culture') && !dest.activities.includes('Culture')) return false
      if (travelStyles.value.includes('food') && !dest.activities.includes('Food')) return false
      if (travelStyles.value.includes('nature') && !dest.activities.includes('Nature')) return false
      return true
    })
    .map(dest => {
      // Calculate confidence score based on preferences
      let confidence = 70
      if (travelStyles.value.length > 2) confidence += 10
      if (dest.distance < 2000) confidence += 10
      if (dest.cost === 'Medium') confidence += 5
      
      // Determine priority
      let priority: 'high' | 'medium' | 'low' = 'medium'
      if (confidence > 85) priority = 'high'
      else if (confidence < 75) priority = 'low'
      
      // Generate reasons based on inputs
      const reasons = [
        `Perfect for ${travelStyles.value.join(', ')} travel style`,
        `Great weather conditions for ${travelTime.value} travel`,
        `Optimal distance from your location`,
        `Matches your budget preferences`
      ]
      
      return {
        destination: dest.name,
        country: dest.country,
        distance: dest.distance,
        weather: dest.weather,
        cost: dest.cost,
        crowds: dest.crowds,
        activities: dest.activities,
        priority,
        confidence,
        reasons: reasons.slice(0, 3)
      }
    })
    .sort((a, b) => b.confidence - a.confidence)
    .slice(0, 6)
  
  recommendations.value = filteredDestinations
  
  // Generate AI insights
  generateAIInsights()
}

const generateAIInsights = () => {
  const location = currentLocation.value
  const time = travelTime.value
  const styles = travelStyles.value
  
  aiInsights.value = {
    geographic: `Based on your location in ${location}, I've identified optimal destinations within 2000-4000km range. The AI detected a preference for destinations with diverse cultural offerings and natural attractions.`,
    
    temporal: `For ${time} travel, I've analyzed seasonal patterns and crowd levels. The recommendations prioritize destinations with favorable weather conditions and moderate tourist activity during your preferred travel period.`,
    
    preferences: `Your travel style (${styles.join(', ')}) shows a preference for immersive experiences. The AI matched destinations with high activity overlap and cultural depth that align with your interests.`,
    
    trends: `Current travel trends show increasing interest in sustainable tourism and local experiences. The AI has prioritized destinations offering authentic cultural immersion and eco-friendly activities.`
  }
}
</script>

<style scoped>
.travel-recommendations {
  width: 100%;
  max-width: 1000px;
  margin: 0 auto;
}

.recommendations-container {
  background: rgba(0, 0, 0, 0.8);
  border: 1px solid rgba(56, 189, 248, 0.3);
  border-radius: 16px;
  overflow: hidden;
  backdrop-filter: blur(10px);
}

.recommendations-header {
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

.recommendations-title {
  color: white;
  font-size: 20px;
  font-weight: 600;
  margin: 0 0 4px 0;
}

.recommendations-subtitle {
  color: #94a3b8;
  font-size: 14px;
  margin: 0;
}

.location-input-section {
  padding: 20px;
  display: grid;
  gap: 20px;
}

.input-group {
  position: relative;
}

.input-label {
  display: block;
  color: white;
  font-size: 14px;
  font-weight: 500;
  margin-bottom: 8px;
}

.location-input {
  width: 100%;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(56, 189, 248, 0.3);
  border-radius: 8px;
  padding: 12px 16px;
  color: white;
  font-size: 14px;
  outline: none;
  transition: all 0.3s ease;
}

.location-input:focus {
  border-color: #38bdf8;
  box-shadow: 0 0 0 3px rgba(56, 189, 248, 0.1);
}

.suggestions-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.95);
  border: 1px solid rgba(56, 189, 248, 0.3);
  border-radius: 8px;
  margin-top: 4px;
  z-index: 1000;
  max-height: 200px;
  overflow-y: auto;
}

.suggestion-item {
  padding: 12px 16px;
  cursor: pointer;
  transition: background 0.2s ease;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.suggestion-item:hover {
  background: rgba(56, 189, 248, 0.1);
}

.suggestion-name {
  color: white;
  font-weight: 500;
}

.suggestion-country {
  color: #94a3b8;
  font-size: 12px;
}

.time-select {
  width: 100%;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(56, 189, 248, 0.3);
  border-radius: 8px;
  padding: 12px 16px;
  color: white;
  font-size: 14px;
  outline: none;
  transition: all 0.3s ease;
}

.time-select:focus {
  border-color: #38bdf8;
  box-shadow: 0 0 0 3px rgba(56, 189, 248, 0.1);
}

.style-options {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 12px;
}

.style-option {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  padding: 8px 12px;
  border-radius: 6px;
  transition: background 0.2s ease;
}

.style-option:hover {
  background: rgba(56, 189, 248, 0.1);
}

.style-option input[type="checkbox"] {
  accent-color: #38bdf8;
}

.style-option span {
  color: white;
  font-size: 14px;
}

.generate-button {
  background: linear-gradient(135deg, #38bdf8, #0ea5e9);
  border: none;
  color: white;
  padding: 16px 32px;
  border-radius: 25px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  justify-self: center;
}

.generate-button:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(56, 189, 248, 0.3);
}

.generate-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.analysis-progress {
  padding: 20px;
  border-top: 1px solid rgba(56, 189, 248, 0.2);
  background: rgba(0, 0, 0, 0.3);
}

.progress-header {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 20px;
}

.ai-avatar {
  width: 40px;
  height: 40px;
  background: linear-gradient(135deg, #38bdf8, #8b5cf6);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
}

.progress-text h4 {
  color: white;
  margin: 0 0 4px 0;
  font-size: 16px;
}

.progress-text p {
  color: #94a3b8;
  margin: 0;
  font-size: 14px;
}

.progress-steps {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.progress-step {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px;
  border-radius: 8px;
  transition: all 0.3s ease;
}

.progress-step.completed {
  background: rgba(16, 185, 129, 0.1);
  border: 1px solid rgba(16, 185, 129, 0.3);
}

.progress-step.current {
  background: rgba(56, 189, 248, 0.1);
  border: 1px solid rgba(56, 189, 248, 0.3);
}

.progress-step.pending {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.step-icon {
  font-size: 18px;
  width: 24px;
  text-align: center;
}

.step-text {
  color: white;
  font-size: 14px;
}

.recommendations-results {
  padding: 20px;
}

.results-title {
  color: white;
  font-size: 18px;
  font-weight: 600;
  margin: 0 0 20px 0;
  text-align: center;
}

.recommendations-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 20px;
  margin-bottom: 30px;
}

.recommendation-card {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(56, 189, 248, 0.2);
  border-radius: 12px;
  overflow: hidden;
  transition: all 0.3s ease;
}

.recommendation-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(56, 189, 248, 0.2);
}

.recommendation-card.high {
  border-color: rgba(239, 68, 68, 0.4);
}

.recommendation-card.medium {
  border-color: rgba(245, 158, 11, 0.4);
}

.recommendation-card.low {
  border-color: rgba(56, 189, 248, 0.4);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  padding: 16px;
  background: rgba(0, 0, 0, 0.3);
}

.destination-name {
  color: white;
  font-size: 18px;
  font-weight: 600;
  margin: 0 0 4px 0;
}

.destination-details {
  color: #94a3b8;
  font-size: 12px;
  margin: 0;
}

.priority-badge {
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 11px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.priority-badge.high {
  background: rgba(239, 68, 68, 0.2);
  color: #fca5a5;
  border: 1px solid rgba(239, 68, 68, 0.4);
}

.priority-badge.medium {
  background: rgba(245, 158, 11, 0.2);
  color: #fcd34d;
  border: 1px solid rgba(245, 158, 11, 0.4);
}

.priority-badge.low {
  background: rgba(56, 189, 248, 0.2);
  color: #7dd3fc;
  border: 1px solid rgba(56, 189, 248, 0.4);
}

.card-content {
  padding: 16px;
}

.recommendation-reasons {
  margin-bottom: 16px;
}

.recommendation-reasons h6 {
  color: #38bdf8;
  font-size: 14px;
  margin: 0 0 8px 0;
}

.recommendation-reasons ul {
  margin: 0;
  padding-left: 20px;
}

.recommendation-reasons li {
  color: #94a3b8;
  font-size: 13px;
  margin-bottom: 4px;
}

.travel-insights {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
  margin-bottom: 16px;
}

.insight-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.insight-label {
  color: #94a3b8;
  font-size: 12px;
}

.insight-value {
  color: white;
  font-size: 12px;
  font-weight: 500;
}

.ai-confidence {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 16px;
}

.confidence-label {
  color: #94a3b8;
  font-size: 12px;
  min-width: 80px;
}

.confidence-bar {
  flex: 1;
  height: 6px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 3px;
  overflow: hidden;
}

.confidence-fill {
  height: 100%;
  background: linear-gradient(90deg, #38bdf8, #8b5cf6);
  transition: width 0.5s ease;
}

.confidence-value {
  color: #38bdf8;
  font-weight: 600;
  min-width: 40px;
  text-align: right;
}

.card-actions {
  display: flex;
  gap: 12px;
  padding: 16px;
  background: rgba(0, 0, 0, 0.2);
}

.action-button {
  flex: 1;
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  font-size: 12px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
}

.action-button.primary {
  background: #38bdf8;
  color: white;
}

.action-button.primary:hover {
  background: #0ea5e9;
}

.action-button.secondary {
  background: rgba(255, 255, 255, 0.1);
  color: #94a3b8;
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.action-button.secondary:hover {
  background: rgba(255, 255, 255, 0.2);
  color: white;
}

.ai-insights-summary {
  background: linear-gradient(135deg, rgba(56, 189, 248, 0.1), rgba(139, 92, 246, 0.1));
  padding: 20px;
  border-radius: 8px;
  border: 1px solid rgba(56, 189, 248, 0.2);
}

.insights-title {
  color: white;
  font-size: 16px;
  font-weight: 600;
  margin: 0 0 20px 0;
  text-align: center;
}

.insights-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 16px;
}

.insight-card {
  text-align: center;
  padding: 16px;
  background: rgba(0, 0, 0, 0.3);
  border-radius: 6px;
}

.insight-icon {
  font-size: 24px;
  margin-bottom: 8px;
}

.insight-card h6 {
  color: #38bdf8;
  font-size: 14px;
  margin: 0 0 8px 0;
}

.insight-card p {
  color: #94a3b8;
  font-size: 12px;
  line-height: 1.4;
  margin: 0;
}

.sample-data {
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

.sample-locations {
  display: flex;
  gap: 12px;
  justify-content: center;
  flex-wrap: wrap;
}

.sample-location-button {
  display: flex;
  align-items: center;
  gap: 8px;
  background: rgba(56, 189, 248, 0.1);
  border: 1px solid rgba(56, 189, 248, 0.3);
  border-radius: 20px;
  padding: 8px 16px;
  color: #38bdf8;
  font-size: 14px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.sample-location-button:hover {
  background: rgba(56, 189, 248, 0.2);
  transform: translateY(-1px);
}

.sample-icon {
  font-size: 16px;
}

.sample-name {
  font-weight: 500;
}
</style> 