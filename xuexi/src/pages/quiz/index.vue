<template>
  <view class="quiz-page">
    <view class="quiz-header">
      <view class="header-nav">
        <view class="back-arrow" @click="goBack"> ← </view>
        <text class="quiz-title">{{ pageTitle }}</text>
      </view>
      <view class="progress-section">
        <view class="progress-bar">
          <view class="progress-track">
            <view class="progress-fill" :style="progressStyle"></view>
            <text class="progress-label">{{ answeredCount }}/{{ questions.length }}</text>
          </view>
        </view>
      </view>
    </view>

    <view class="question-section">
      <view class="image-wrap" @click="playAudio">
        <image class="word-image" :src="currentQuestion.image || defaultImage" mode="aspectFit"></image>
      </view>
    </view>

    <view class="options-section">
      <view class="options-list">
        <view v-for="(option, index) in currentQuestion.options"
              :key="index"
              class="option-card"
              :class="{ selected: selectedOption === index, correct: showResult && option.isCorrect, wrong: showResult && selectedOption === index && !option.isCorrect }"
              @click="selectOption(index)"
        >
          <view class="option-image"><text class="image-emoji">{{ option.emoji }}</text></view>
          <text class="option-text">{{ option.text }}</text>
          <view v-if="showResult && option.isCorrect" class="correct-mark">✓</view>
        </view>
      </view>
    </view>

    <view class="bottom-section">
      <button v-if="!showResult" class="check-btn" :class="{ active: selectedOption !== null }" :disabled="selectedOption === null" @click="checkAnswer">检查</button>
      <button v-else class="continue-btn" @click="nextQuestion">继续</button>
    </view>

    <view v-if="showCelebration" class="celebration-overlay" @click="closeCelebration">
      <view class="celebration-modal" @click.stop>
        <view class="celebration-title">恭喜通关!</view>
        <view class="celebration-desc">全对已解锁下一关。</view>
        <button class="celebration-btn" @click="closeCelebration">确定</button>
      </view>
    </view>

    <view v-if="showFailed" class="failed-overlay" @click="closeFailed">
      <view class="failed-modal" @click.stop>
        <view class="failed-title">完成本关</view>
        <view class="failed-desc">未全对，下一关仍锁定。</view>
        <button class="failed-btn" @click="closeFailed">确定</button>
      </view>
    </view>

    <!-- 统一总结页 -->
    <view v-if="showSummary" class="summary-overlay" @click="continueStudy">
      <view class="summary-modal" @click.stop>
        <view class="summary-title">恭喜你完成本课时的预习！</view>
        <view class="summary-block">
          <text class="summary-label">你已经掌握：</text>
          <view class="summary-item">词汇 <text class="summary-strong">{{ Math.floor(correctCount / 2) }}</text> 个！</view>
          <view class="summary-item">句子 <text class="summary-strong">{{ Math.ceil(correctCount / 2) }}</text> 个！</view>
        </view>
        <view class="summary-score">
          <text class="score-label">获得积分：</text>
          <text class="score-star">⭐</text>
          <text class="score-num">{{ correctCount * 2 + 2 }}</text>
        </view>
        <button class="summary-btn" @click="continueStudy">继续学习</button>
      </view>
    </view>
  </view>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import { onLoad } from '@dcloudio/uni-app'
import units from '@/data/units.js'

// 获取路由参数
const getRouteQuery = () => {
  try {
    // 尝试多种方式获取路由参数
    if (uni.getEnterOptionsSync) {
      const options = uni.getEnterOptionsSync()
      return options.query || {}
    }
    // 如果上面方法失败，尝试从页面参数获取
    const pages = getCurrentPages()
    if (pages && pages.length > 0) {
      const currentPage = pages[pages.length - 1]
      return currentPage.options || {}
    }
    return {}
  } catch (e) {
    console.log('获取路由参数失败:', e)
    return {}
  }
}

// 路由参数
const routeParams = ref({})
const pageTitle = ref('预习内容检测')
const levelId = ref(1)
const selectedOption = ref(null)
const showResult = ref(false)
const isCorrect = ref(false)
const isPlaying = ref(false)
const currentQuestionIndex = ref(0)
const correctCount = ref(0)
const answeredCount = ref(0)
const showCelebration = ref(false)
const showFailed = ref(false)
const showSummary = ref(false)

// 使用onLoad获取路由参数
onLoad((options) => {
  console.log('onLoad接收到的参数:', options)
  routeParams.value = options || {}
})

const defaultImage = '/static/logo.png'

// 生成词汇题目
const generateVocabularyQuestions = (unit) => {
  const vocab = unit.knowledge.Vocabulary || []
  const questions = []
  
  vocab.forEach((word, index) => {
    // 创建干扰选项（从其他词汇中随机选择）
    const otherWords = vocab.filter((_, i) => i !== index)
    const shuffledOthers = [...otherWords].sort(() => Math.random() - 0.5)
    const wrongOptions = shuffledOthers.slice(0, 2)
    
    // 创建选项
    const options = [
      { text: `a. ${word}`, isCorrect: true },
      { text: `b. ${wrongOptions[0] || '选项1'}`, isCorrect: false },
      { text: `c. ${wrongOptions[1] || '选项2'}`, isCorrect: false }
    ]
    
    // 打乱选项顺序
    const shuffledOptions = options.sort(() => Math.random() - 0.5)
    
    questions.push({
      word: word,
      image: '/static/logo.png',
      options: shuffledOptions
    })
  })
  
  return questions
}

// 生成句子题目
const generateSentenceQuestions = (unit) => {
  const sentences = unit.knowledge['Language focus'] || []
  const questions = []
  
  sentences.forEach((sentence, index) => {
    // 创建干扰选项（从其他句子中随机选择）
    const otherSentences = sentences.filter((_, i) => i !== index)
    const shuffledOthers = [...otherSentences].sort(() => Math.random() - 0.5)
    const wrongOptions = shuffledOthers.slice(0, 2)
    
    // 创建选项
    const options = [
      { text: `a. ${sentence}`, isCorrect: true },
      { text: `b. ${wrongOptions[0] || '选项1'}`, isCorrect: false },
      { text: `c. ${wrongOptions[1] || '选项2'}`, isCorrect: false }
    ]
    
    // 打乱选项顺序
    const shuffledOptions = options.sort(() => Math.random() - 0.5)
    
    questions.push({
      word: sentence,
      image: '/static/logo.png',
      options: shuffledOptions
    })
  })
  
  return questions
}

// 生成混合题目（词汇+句子）
const generateMixedQuestions = (unit) => {
  const vocabQuestions = generateVocabularyQuestions(unit)
  const sentenceQuestions = generateSentenceQuestions(unit)
  return [...vocabQuestions, ...sentenceQuestions].sort(() => Math.random() - 0.5)
}

const questions = ref([])

const currentQuestion = computed(() => questions.value[currentQuestionIndex.value])
const progressFillWidth = computed(() => {
  const total = Math.max(1, questions.value.length)
  const completed = Math.min(answeredCount.value, total)
  return Math.round((completed / total) * 100)
})
const remainingCount = computed(() => Math.max(0, questions.value.length - answeredCount.value))
const progressStyle = computed(() => ({ width: progressFillWidth.value + '%' }))

const playAudio = () => {
  if (isPlaying.value) return
  isPlaying.value = true
  try {
    const utter = new SpeechSynthesisUtterance(currentQuestion.value.word)
    utter.lang = 'en-US'
    utter.rate = 1
    utter.onend = () => { isPlaying.value = false }
    window.speechSynthesis && window.speechSynthesis.cancel()
    window.speechSynthesis && window.speechSynthesis.speak(utter)
  } catch (e) {
    setTimeout(() => { isPlaying.value = false }, 1000)
  }
  uni.vibrateShort && uni.vibrateShort()
}

const selectOption = (index) => {
  if (showResult.value) return
  selectedOption.value = index
  uni.vibrateShort && uni.vibrateShort()
}

const checkAnswer = () => {
  if (selectedOption.value === null) return
  const selectedOptionData = currentQuestion.value.options[selectedOption.value]
  isCorrect.value = !!selectedOptionData.isCorrect
  showResult.value = true
  answeredCount.value = Math.min(questions.value.length, answeredCount.value + 1)
  if (isCorrect.value) {
    uni.vibrateShort && uni.vibrateShort()
    correctCount.value += 1
  } else {
    uni.vibrateLong && uni.vibrateLong()
  }
}

const nextQuestion = () => {
  if (currentQuestionIndex.value < questions.value.length - 1) {
    currentQuestionIndex.value++
    selectedOption.value = null
    showResult.value = false
    isCorrect.value = false
    setTimeout(() => { playAudio() }, 300)
  } else {
    const passed = correctCount.value === questions.value.length
    try {
      uni.setStorageSync(`level-${levelId.value}-passed`, passed ? '1' : '0')
      uni.setStorageSync(`level-${levelId.value}-score`, `${correctCount.value}/${questions.value.length}`)
    } catch (e) {}
    showSummary.value = true
    showCelebration.value = false
    showFailed.value = false
  }
}

const closeCelebration = () => { showCelebration.value = false; goBack() }
const closeFailed = () => { showFailed.value = false; goBack() }
const goBack = () => { uni.navigateBack && uni.navigateBack() }
const continueStudy = () => {
  showSummary.value = false
  if (uni.reLaunch) {
    uni.reLaunch({ url: '/pages/index/index' })
  } else {
    uni.navigateTo && uni.navigateTo({ url: '/pages/index/index' })
  }
}

onMounted(() => {
  const q = routeParams.value || {}
  console.log('路由参数:', q)
  
  const fromParam = Number(q.level)
  levelId.value = Number.isFinite(fromParam) && fromParam > 0 ? fromParam : 1
  if (q.title) pageTitle.value = q.title
  
  // 根据传入 unit 获取对应单元数据
  const unitIndex = Number(q.unit) || 0
  console.log('单元索引:', unitIndex)
  console.log('可用单元数量:', units.length)
  
  const currentUnit = units[unitIndex] || units[0]
  console.log('当前单元数据:', currentUnit)
  
  // 生成混合题目（词汇+句子）
  questions.value = generateMixedQuestions(currentUnit)
  console.log('生成的题目数量:', questions.value.length)
  
  setTimeout(() => { playAudio() }, 500)
})
</script>

<style scoped>
.quiz-page {
  min-height: 100vh;
  background: #fff;
  display: flex;
  flex-direction: column;
  padding-top: env(safe-area-inset-top);
  padding-top: constant(safe-area-inset-top);
}

.quiz-header {
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.header-nav {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 12px;
}

.back-arrow {
  color: #111;
  font-size: 20px;
  font-weight: 700;
  padding: 8px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}

.quiz-title {
  color: #111;
  font-size: 16px;
  font-weight: 700;
  flex: 1;
  text-align: center;
}

.progress-section {
  width: 100%;
}

.progress-bar {
  width: 100%;
  display: flex;
  align-items: center;
}

.progress-track {
  position: relative;
  display: block;
  width: 100%;
  height: 12px;
  background: #e9ecef;
  border-radius: 999px;
  overflow: hidden;
}

.progress-fill {
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  background: #58cc02;
  transition: width 0.5s ease;
}

.progress-label {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  font-size: 12px;
  color: #333;
}

.remain-badge {
  min-width: 28px;
  height: 18px;
  padding: 0 6px;
  background: #eef2ff;
  color: #1f2937;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 12px;
  font-weight: 700;
}

.question-section {
  padding: 16px 16px 8px;
  text-align: center;
}

.progress-mini {
  display: none;
}

.image-wrap {
  width: 100%;
  display: flex;
  justify-content: center;
}

.word-image {
  width: 70%;
  max-width: 320px;
  height: 160px;
  border-radius: 8px;
  background: #f3f4f6;
}

.options-section {
  flex: 1;
  padding: 8px 16px 16px;
}

.options-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.option-card {
  background: #fff;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  padding: 20px;
  text-align: center;
  position: relative;
  transition: all 0.3s ease;
}

.option-card.selected {
  border: 2px solid #3b82f6;
  box-shadow: 0 0 10px rgba(59, 130, 246, 0.15);
}

.option-card.correct {
  border: 2px solid #58cc02;
  background: linear-gradient(135deg, rgba(88, 204, 2, 0.06), rgba(76, 176, 0, 0.06));
  box-shadow: 0 0 10px rgba(88, 204, 2, 0.2);
}

.option-card.wrong {
  border: 2px solid #ff4757;
  background: linear-gradient(135deg, rgba(255, 71, 87, 0.06), rgba(255, 71, 87, 0.03));
  box-shadow: 0 0 10px rgba(255, 71, 87, 0.18);
}

.image-emoji {
  font-size: 40px;
}

.option-text {
  color: #111;
  font-size: 16px;
  font-weight: 600;
}

.correct-mark {
  position: absolute;
  top: 8px;
  right: 8px;
  width: 24px;
  height: 24px;
  background: #58cc02;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  font-size: 14px;
  font-weight: 700;
}

.bottom-section {
  padding: 16px;
}

.check-btn,
.continue-btn {
  width: 100%;
  padding: 16px;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 600;
  border: none;
}

.check-btn {
  background: #f3f4f6;
  color: #6b7280;
}

.check-btn.active {
  background: linear-gradient(135deg, #58cc02, #4cb000);
  color: #fff;
  box-shadow: 0 4px 12px rgba(88, 204, 2, 0.3);
}

.continue-btn {
  background: linear-gradient(135deg, #58cc02, #4cb000);
  color: #fff;
  box-shadow: 0 4px 12px rgba(88, 204, 2, 0.3);
}

.celebration-overlay,
.failed-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
}

.celebration-modal,
.failed-modal {
  background: #fff;
  border-radius: 16px;
  padding: 32px 24px;
  text-align: center;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
}

.celebration-title,
.failed-title {
  font-size: 24px;
  font-weight: 700;
  color: #333;
  margin-bottom: 12px;
  display: block;
}

.celebration-desc,
.failed-desc {
  font-size: 16px;
  color: #666;
  margin-bottom: 24px;
  display: block;
}

.celebration-btn,
.failed-btn {
  background: linear-gradient(135deg, #58cc02, #4cb000);
  color: #fff;
  border: none;
  border-radius: 12px;
  padding: 12px 32px;
  font-size: 16px;
  font-weight: 600;
}

.failed-btn {
  background: linear-gradient(135deg, #ff4757, #ff3742);
}

/* 总结页样式 */
.summary-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10000;
}

.summary-modal {
  background: #fff;
  border-radius: 16px;
  padding: 24px 20px;
  text-align: center;
  width: 82%;
  max-width: 360px;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
}

.summary-title {
  font-size: 16px;
  font-weight: 700;
  color: #333;
  margin-bottom: 16px;
  display: block;
}

.summary-block {
  margin: 6px 0 14px;
  color: #444;
}

.summary-label {
  display: block;
  margin-bottom: 8px;
  color: #666;
  font-size: 14px;
}

.summary-item {
  margin: 6px 0;
  font-size: 15px;
}

.summary-strong {
  font-weight: 700;
  color: #111;
}

.summary-score {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  margin: 8px 0 16px;
  color: #333;
}

.score-star {
  font-size: 16px;
}

.score-num {
  font-size: 18px;
  font-weight: 700;
}

.summary-btn {
  border: 1px solid #22c55e;
  color: #22c55e;
  background: #fff;
  padding: 8px 16px;
  border-radius: 8px;
}
</style>

