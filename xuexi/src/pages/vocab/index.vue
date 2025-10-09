<template>
  <view class="vocab-page">
    <!-- 今日目标页面 -->
    <view v-if="showPreview" class="preview-section">
      <view class="preview-header">
        <view class="header-nav">
          <view class="back-arrow" @click="goBack">⟵</view>
          <text class="preview-title">词句预习</text>
        </view>
      </view>
      
      <view class="preview-content">
        <view class="goal-card">
          <text class="goal-title">今日目标</text>
          
          <view class="section">
            <text class="section-title">Vocabulary</text>
            <view class="vocab-list">
              <text v-for="(item, index) in vocab" :key="index" class="vocab-item">{{ item }}</text>
            </view>
          </view>
          
          <view class="section">
            <text class="section-title">Language focus</text>
            <view class="focus-list">
              <text v-for="(item, index) in focus" :key="index" class="focus-item">{{ item }}</text>
            </view>
          </view>
          
          <button class="start-btn" @click="startLearning">开始学习</button>
        </view>
      </view>
    </view>

    <!-- 词汇学习页面 -->
    <view v-else class="learning-section">
      <view class="vocab-header">
        <view class="header-nav">
          <view class="back-arrow" @click="goBack">⟵</view>
          <text class="vocab-title">词句预习</text>
        </view>
        <view class="progress-section">
          <view class="progress-bar">
            <view class="progress-track">
              <view class="progress-fill" :style="progressStyle"></view>
              <text class="progress-label">{{ currentIndex + 1 }}/{{ learningItems.length }}</text>
            </view>
          </view>
        </view>
      </view>

      <view class="content-section">
        <!-- 导航按钮 -->
        <view class="nav-buttons">
          <view class="nav-btn prev-btn" @click="prevItem" :class="{ disabled: currentIndex === 0 }">
            <text class="nav-arrow">‹</text>
          </view>
          <view class="nav-btn next-btn" @click="nextItem" :class="{ disabled: !canProceed }">
            <text class="nav-arrow">›</text>
          </view>
        </view>

        <!-- 词汇图片（仅词汇显示） -->
        <view v-if="currentItem.type === 'vocab'" class="image-wrap" @click="playAudio">
          <image class="vocab-image" :src="currentItem.image" mode="aspectFit"></image>
        </view>

        <!-- 句子显示区域（仅句子显示） -->
        <view v-if="currentItem.type === 'sentence'" class="sentence-wrap" @click="playAudio">
          <view class="sentence-card">
            <text class="sentence-text">{{ currentItem.text }}</text>
          </view>
        </view>

        <!-- 文本显示 -->
        <view class="text-section">
          <text class="vocab-text">{{ currentItem.text }}</text>
          <text class="item-type">{{ currentItem.type === 'vocab' ? '词汇' : '句子' }}</text>
        </view>
      </view>

      <!-- 底部操作区 -->
      <view class="bottom-actions">
        <view class="action-item" @click="playAudio">
          <view class="action-icon listen-icon">♪</view>
          <text class="action-label">再听一遍</text>
        </view>
        
        <view class="action-item main-action" @click="toggleRecording">
          <view class="action-icon record-icon" :class="{ recording: isRecording }">
            {{ isRecording ? '⏹' : '🎤' }}
          </view>
          <text class="action-label">{{ isRecording ? '点击结束' : '点击跟读' }}</text>
        </view>
        
        <view class="action-item" @click="playMyRecording" :class="{ disabled: !hasRecording }">
          <view class="action-icon play-icon">▶</view>
          <text class="action-label">我的</text>
        </view>
      </view>

      <!-- 提示文本 -->
      <view class="tips">
        <text class="tip-text" v-if="currentIndex > 0">点击返回上一词</text>
        <text class="tip-text" v-if="!canProceed">当前单词未过关时，"下一个"按钮无法点击</text>
        <text class="tip-text" v-if="!hasRecording">未识别到用户语音时，"我的"按钮无法点击</text>
      </view>
    </view>
  </view>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { onLoad } from '@dcloudio/uni-app'
import unitsData from '@/data/units.js'

// 页面状态
const showPreview = ref(true)

// 单元数据
const unitIndex = ref(0)
const unit = computed(() => unitsData[unitIndex.value] || unitsData[0])

// 从单元数据获取词汇和语言重点
const vocab = computed(() => {
  return unit.value.knowledge.Vocabulary
})

const focus = computed(() => {
  return unit.value.knowledge['Language focus']
})

// 学习模式下的学习项目（词汇+句子）
const learningItems = computed(() => {
  const items = []
  
  // 添加词汇项目
  vocab.value.forEach(item => {
    items.push({
      text: item,
      type: 'vocab',
      image: `/static/${item.replace(/\s+/g, '-').replace(/[^a-zA-Z0-9-]/g, '')}.png`,
      audio: getAudioUrl(item) // 使用更可靠的音频源
    })
  })
  
  // 添加句子项目
  focus.value.forEach(item => {
    items.push({
      text: item,
      type: 'sentence',
      image: null, // 句子没有图片
      audio: getAudioUrl(item) // 使用更可靠的音频源
    })
  })
  
  return items
})

// 获取音频URL的函数
const getAudioUrl = (text) => {
  // 优先使用有道词典TTS，如果失败会fallback到Web Speech API
  return `https://dict.youdao.com/dictvoice?audio=${encodeURIComponent(text)}&type=1`
}

// 保持向后兼容
const vocabItems = computed(() => learningItems.value)
const languageFocus = computed(() => focus.value)

const currentIndex = ref(0)
const isRecording = ref(false)
const hasRecording = ref(false)
const completedItems = ref(new Set())
const userRecordings = ref(new Map()) // 存储用户录音文件路径

const currentItem = computed(() => vocabItems.value[currentIndex.value])

const progressStyle = computed(() => {
  const progress = ((currentIndex.value + 1) / learningItems.value.length) * 100
  return `width: ${progress}%`
})

const canProceed = computed(() => {
  return completedItems.value.has(currentIndex.value)
})

// 返回上一页
const goBack = () => {
  if (showPreview.value) {
    uni.navigateBack()
  } else {
    showPreview.value = true
  }
}

// 开始学习
const startLearning = () => {
  showPreview.value = false
  
  // 开始学习后自动播放第一个词汇的音频
  setTimeout(() => {
    if (currentItem.value) {
      playAudio()
    }
  }, 300)
}

// 播放音频
const playAudio = () => {
  if (currentItem.value && currentItem.value.audio) {
    console.log('尝试播放音频:', currentItem.value.audio)
    
    // 创建音频上下文
    const audioContext = uni.createInnerAudioContext()
    audioContext.src = currentItem.value.audio
    audioContext.volume = 1.0
    
    audioContext.onPlay(() => {
      console.log('开始播放音频')
      uni.showToast({ title: '正在播放...', icon: 'none' })
    })
    
    audioContext.onEnded(() => {
      console.log('音频播放结束')
      audioContext.destroy()
    })
    
    audioContext.onError((res) => {
      console.log('音频播放错误:', res)
      // 如果网络音频失败，尝试使用系统TTS
      trySystemTTS()
      audioContext.destroy()
    })
    
    audioContext.onCanplay(() => {
      console.log('音频可以播放')
    })
    
    audioContext.onWaiting(() => {
      console.log('音频加载中...')
    })
    
    // 尝试播放 - 在uni-app中play()不返回Promise
    try {
      audioContext.play()
    } catch (error) {
      console.log('播放失败:', error)
      // 如果播放失败，尝试使用系统TTS
      trySystemTTS()
      audioContext.destroy()
    }
  } else {
    uni.showToast({ title: '暂无音频文件', icon: 'none' })
  }
}

// 尝试使用系统TTS作为备用方案
const trySystemTTS = () => {
  if (currentItem.value && currentItem.value.text) {
    console.log('尝试TTS播放:', currentItem.value.text)
    
    // #ifdef H5
    // 使用Web Speech API
    if ('speechSynthesis' in window) {
      const utterance = new SpeechSynthesisUtterance(currentItem.value.text)
      utterance.lang = 'en-US'
      utterance.rate = 0.8
      utterance.pitch = 1
      utterance.volume = 1
      
      utterance.onstart = () => {
        console.log('TTS开始播放')
        uni.showToast({ title: '正在播放...', icon: 'none' })
      }
      
      utterance.onend = () => {
        console.log('TTS播放结束')
      }
      
      utterance.onerror = (event) => {
        console.log('TTS播放错误:', event)
        uni.showToast({ title: '语音播放失败', icon: 'none' })
      }
      
      speechSynthesis.speak(utterance)
    } else {
      uni.showToast({ title: '浏览器不支持语音播放', icon: 'none' })
    }
    // #endif
    
    // #ifndef H5
    // 原生平台可以尝试其他TTS服务
    uni.showToast({ title: '使用语音合成播放', icon: 'none' })
    console.log('尝试TTS播放:', currentItem.value.text)
    // #endif
  }
}

// 上一个词汇
const prevItem = () => {
  if (currentIndex.value > 0) {
    currentIndex.value--
    resetRecordingState()
    
    // 切换词汇后自动播放音频
    setTimeout(() => {
      if (currentItem.value) {
        playAudio()
      }
    }, 200)
  }
}

// 下一个项目
const nextItem = () => {
  if (canProceed.value && currentIndex.value < learningItems.value.length - 1) {
    currentIndex.value++
    resetRecordingState()
    
    // 切换词汇后自动播放音频
    setTimeout(() => {
      if (currentItem.value) {
        playAudio()
      }
    }, 200)
  }
}

// 重置录音状态
const resetRecordingState = () => {
  isRecording.value = false
  hasRecording.value = completedItems.value.has(currentIndex.value)
}

// 切换录音状态
const toggleRecording = () => {
  if (isRecording.value) {
    // 结束录音
    stopRecording()
  } else {
    // 开始录音
    startRecording()
  }
}

// 开始录音
const startRecording = () => {
  // 检查平台类型
  // #ifdef H5
  startWebRecording()
  // #endif
  
  // #ifndef H5
  // 原生平台使用uni.authorize
  if (uni.authorize) {
    uni.authorize({
      scope: 'scope.record',
      success() {
        startNativeRecording()
      },
      fail() {
        uni.showModal({
          title: '需要录音权限',
          content: '请在设置中开启录音权限',
          showCancel: false
        })
      }
    })
  } else {
    startNativeRecording()
  }
  // #endif
}

// H5平台录音
const startWebRecording = () => {
  if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
    uni.showToast({ title: '浏览器不支持录音功能', icon: 'none' })
    return
  }
  
  navigator.mediaDevices.getUserMedia({ audio: true })
    .then(stream => {
      const mediaRecorder = new MediaRecorder(stream)
      const audioChunks = []
      
      mediaRecorder.ondataavailable = (event) => {
        audioChunks.push(event.data)
      }
      
      mediaRecorder.onstop = () => {
        const audioBlob = new Blob(audioChunks, { type: 'audio/wav' })
        const audioUrl = URL.createObjectURL(audioBlob)
        
        isRecording.value = false
        hasRecording.value = true
        completedItems.value.add(currentIndex.value)
        
        // 保存录音文件URL
        userRecordings.value.set(currentIndex.value, audioUrl)
        console.log('录音文件保存:', audioUrl)
        
        uni.showToast({ title: '录音完成', icon: 'success' })
        
        // 停止所有音频轨道
        stream.getTracks().forEach(track => track.stop())
      }
      
      mediaRecorder.onerror = (event) => {
        console.log('录音错误:', event)
        isRecording.value = false
        uni.showToast({ title: '录音失败', icon: 'none' })
        
        // 停止所有音频轨道
        stream.getTracks().forEach(track => track.stop())
      }
      
      mediaRecorder.start()
      isRecording.value = true
      uni.showToast({ title: '开始录音，请跟读', icon: 'none' })
      
      // 存储MediaRecorder实例以便停止
      window.currentMediaRecorder = mediaRecorder
      window.currentStream = stream
    })
    .catch(error => {
      console.log('获取录音权限失败:', error)
      uni.showModal({
        title: '需要录音权限',
        content: '请在浏览器中允许录音权限',
        showCancel: false
      })
    })
}

// 原生平台录音
const startNativeRecording = () => {
  const recorderManager = uni.getRecorderManager()
  recorderManager.start({
    duration: 10000, // 最长录音时间10秒
    sampleRate: 16000,
    numberOfChannels: 1,
    encodeBitRate: 96000,
    format: 'mp3'
  })
  
  isRecording.value = true
  uni.showToast({ title: '开始录音，请跟读', icon: 'none' })
  
  // 录音结束回调
  recorderManager.onStop((res) => {
    console.log('录音结束:', res)
    isRecording.value = false
    hasRecording.value = true
    completedItems.value.add(currentIndex.value)
    
    // 保存录音文件路径
    if (res.tempFilePath) {
      userRecordings.value.set(currentIndex.value, res.tempFilePath)
      console.log('录音文件保存:', res.tempFilePath)
    }
    
    uni.showToast({ title: '录音完成', icon: 'success' })
  })
  
  // 录音错误回调
  recorderManager.onError((res) => {
    console.log('录音错误:', res)
    isRecording.value = false
    uni.showToast({ title: '录音失败', icon: 'none' })
  })
}

// 停止录音
const stopRecording = () => {
  // #ifdef H5
  if (window.currentMediaRecorder && window.currentMediaRecorder.state === 'recording') {
    window.currentMediaRecorder.stop()
  }
  // #endif
  
  // #ifndef H5
  const recorderManager = uni.getRecorderManager()
  recorderManager.stop()
  // #endif
}

// 播放我的录音
const playMyRecording = () => {
  if (hasRecording.value && userRecordings.value.has(currentIndex.value)) {
    const recordingPath = userRecordings.value.get(currentIndex.value)
    console.log('播放用户录音:', recordingPath)
    
    // 创建音频上下文播放用户录音
    const audioContext = uni.createInnerAudioContext()
    audioContext.src = recordingPath
    audioContext.volume = 1.0
    
    audioContext.onPlay(() => {
      console.log('开始播放用户录音')
      uni.showToast({ title: '正在播放您的录音...', icon: 'none' })
    })
    
    audioContext.onEnded(() => {
      console.log('用户录音播放结束')
      audioContext.destroy()
    })
    
    audioContext.onError((res) => {
      console.log('播放用户录音错误:', res)
      uni.showToast({ title: '播放录音失败', icon: 'none' })
      audioContext.destroy()
    })
    
    // 尝试播放 - 在uni-app中play()不返回Promise
    try {
      audioContext.play()
    } catch (error) {
      console.log('播放用户录音失败:', error)
      uni.showToast({ title: '播放录音失败', icon: 'none' })
      audioContext.destroy()
    }
  } else {
    uni.showToast({ title: '请先完成录音', icon: 'none' })
  }
}

// 清理音频资源
const cleanupAudio = () => {
  // 清理H5录音资源
  if (window.currentStream) {
    window.currentStream.getTracks().forEach(track => track.stop())
    window.currentStream = null
  }
  if (window.currentMediaRecorder) {
    window.currentMediaRecorder = null
  }
  
  // 停止TTS播放
  if ('speechSynthesis' in window) {
    speechSynthesis.cancel()
  }
}

// 页面生命周期函数
onLoad((options) => {
  console.log('vocab页面接收到的参数:', options)
  if (options && options.unit !== undefined) {
    unitIndex.value = Number(options.unit) || 0
    console.log('更新单元索引为:', unitIndex.value)
  }
})

onMounted(() => {
  // 页面加载时的初始化
  resetRecordingState()
  
  // 延迟播放音频，确保页面完全加载
  setTimeout(() => {
    if (currentItem.value && !showPreview.value) {
      playAudio()
    }
  }, 500)
})

// 页面卸载时清理资源
onUnmounted(() => {
  cleanupAudio()
})
</script>

<style scoped>
.vocab-page {
  min-height: 100vh;
  background: #fff;
  display: flex;
  flex-direction: column;
  padding-top: constant(safe-area-inset-top);
  padding-top: env(safe-area-inset-top);
}

/* 今日目标页面样式 */
.preview-section {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.preview-header {
  padding: 8px 16px;
  border-bottom: 1px solid #e5e7eb;
}

.preview-title {
  font-size: 20px;
  font-weight: 800;
  color: #111;
}

.preview-content {
  flex: 1;
  padding: 8px 16px;
  overflow-y: auto;
}

.goal-card {
  background: #fff;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  padding: 16px;
  box-shadow: 0 1px 2px rgba(0, 0, 0, .04);
}

.goal-title {
  font-size: 18px;
  font-weight: 800;
  color: #111;
  margin-bottom: 16px;
  display: block;
}

.section {
  margin-bottom: 16px;
}

.section-title {
  font-size: 14px;
  font-weight: 800;
  color: #111;
  margin-bottom: 8px;
  display: block;
}

.vocab-list, .focus-list {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.vocab-item, .focus-item {
  font-size: 14px;
  color: #111;
  line-height: 1.5;
}

.start-btn {
  width: 100%;
  background: #3b82f6;
  color: #fff;
  border: none;
  border-radius: 12px;
  padding: 12px;
  font-size: 16px;
  font-weight: 600;
  margin-top: 16px;
}

.vocab-header {
  padding: 8px 16px;
  border-bottom: 1px solid #e5e7eb;
}

.header-nav {
  display: flex;
  align-items: center;
  margin-bottom: 8px;
}

.back-arrow {
  font-size: 24px;
  color: #111;
  margin-right: 16px;
  padding: 8px;
}

.vocab-title {
  font-size: 20px;
  font-weight: 800;
  color: #111;
}

.progress-section {
  margin-top: 8px;
}

.progress-bar {
  width: 100%;
  height: 8px;
  background: #e5e7eb;
  border-radius: 4px;
  overflow: hidden;
}

.progress-track {
  position: relative;
  width: 100%;
  height: 100%;
}

.progress-fill {
  height: 100%;
  background: #10b981;
  transition: width 0.3s ease;
}

.progress-label {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 12px;
  color: #111;
  font-weight: 600;
}

.content-section {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 32px 16px;
  position: relative;
}

.nav-buttons {
  position: absolute;
  top: 50%;
  left: 0;
  right: 0;
  display: flex;
  justify-content: space-between;
  padding: 0 16px;
  pointer-events: none;
  z-index: 10;
}

.nav-btn {
  width: 48px;
  height: 48px;
  border-radius: 24px;
  background: rgba(255, 255, 255, 0.9);
  border: 1px solid #e5e7eb;
  display: flex;
  align-items: center;
  justify-content: center;
  pointer-events: auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.nav-btn.disabled {
  opacity: 0.5;
  pointer-events: none;
}

.nav-arrow {
  font-size: 20px;
  color: #111;
  font-weight: bold;
}

.image-wrap {
  width: 280px;
  height: 280px;
  border-radius: 16px;
  overflow: hidden;
  margin-bottom: 24px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
}

.vocab-image {
  width: 100%;
  height: 100%;
}

.sentence-wrap {
  width: 280px;
  height: 280px;
  border-radius: 16px;
  overflow: hidden;
  margin-bottom: 24px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
  display: flex;
  align-items: center;
  justify-content: center;
}

.sentence-card {
  background: #f8f9fa;
  border: 2px solid #e9ecef;
  border-radius: 12px;
  padding: 24px;
  width: 90%;
  text-align: center;
}

.sentence-text {
  font-size: 18px;
  font-weight: 600;
  color: #111;
  line-height: 1.4;
}

.text-section {
  margin-bottom: 32px;
  text-align: center;
}

.vocab-text {
  font-size: 24px;
  font-weight: 600;
  color: #111;
  text-align: center;
  display: block;
  margin-bottom: 8px;
}

.item-type {
  font-size: 14px;
  color: #6b7280;
  font-weight: 500;
  display: block;
}

.bottom-actions {
  display: flex;
  justify-content: space-around;
  align-items: center;
  padding: 24px 32px;
  border-top: 1px solid #e5e7eb;
  background: #fff;
}

.action-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  flex: 1;
}

.action-item.disabled {
  opacity: 0.5;
}

.main-action {
  flex: 1.5;
}

.action-icon {
  width: 48px;
  height: 48px;
  border-radius: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  margin-bottom: 4px;
}

.listen-icon {
  background: #f3f4f6;
  color: #6b7280;
}

.record-icon {
  background: #3b82f6;
  color: #fff;
}

.record-icon.recording {
  background: #ef4444;
  animation: pulse 1s infinite;
}

@keyframes pulse {
  0% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.1);
    opacity: 0.8;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

.play-icon {
  background: #f3f4f6;
  color: #6b7280;
}

.action-label {
  font-size: 12px;
  color: #6b7280;
  text-align: center;
}

.tips {
  padding: 16px;
  text-align: center;
}

.tip-text {
  font-size: 12px;
  color: #ef4444;
  display: block;
  margin-bottom: 4px;
}
</style>
