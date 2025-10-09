<template>
  <view class="speaking-page">
    <view class="header">
      <view class="back" @click="goBack">⟵</view>
      <text class="title">口语训练</text>
    </view>

    <view class="content">
      <text class="target">请跟读：Good morning, my name is Ken.</text>
      <view class="actions">
        <button class="btn" @click="playTarget">播放示范</button>
        <button class="btn primary" @click="toggle">{{ recording ? '结束' : '开始跟读' }}</button>
      </view>
      <view class="hint">结束后自动给出简单反馈</view>
      <view v-if="result" class="result" :class="{ pass: result==='很好' }">{{ result }}</view>
    </view>
  </view>
</template>

<script setup>
import { ref } from 'vue'
const recording = ref(false)
const result = ref('')

const playTarget = () => {
  // #ifdef H5
  if ('speechSynthesis' in window) {
    const u = new SpeechSynthesisUtterance('Good morning, my name is Ken.')
    u.lang = 'en-US'; u.rate = 0.9; speechSynthesis.cancel(); speechSynthesis.speak(u)
  }
  // #endif
  // #ifndef H5
  uni.showToast({ title: '使用系统语音播放', icon: 'none' })
  // #endif
}

const toggle = () => {
  if (recording.value) {
    stop()
  } else {
    start()
  }
}

const start = () => {
  recording.value = true; result.value = ''
  // 简化：真实评分需ASR服务，这里仅做演示
  uni.showToast({ title: '开始录音', icon: 'none' })
}

const stop = () => {
  recording.value = false
  // 演示反馈
  result.value = Math.random() > 0.3 ? '很好' : '需要加强'
}

const goBack = () => uni.navigateBack && uni.navigateBack()
</script>

<style scoped>
.speaking-page{ min-height:100vh; background:#fff; display:flex; flex-direction:column }
.header{ display:flex; align-items:center; gap:12px; padding:12px 16px; border-bottom:1px solid #e5e7eb }
.back{ font-size:22px; color:#111 }
.title{ font-size:18px; font-weight:800; color:#111 }
.content{ flex:1; padding:16px; display:flex; flex-direction:column; gap:12px }
.target{ font-size:16px; color:#111 }
.actions{ display:flex; gap:8px }
.btn{ border:1px solid #e5e7eb; background:#fff; color:#111; border-radius:10px; padding:8px 12px; font-size:12px }
.btn.primary{ border-color:#10b981; color:#10b981 }
.hint{ color:#6b7280; font-size:12px }
.result{ margin-top:8px; color:#ef4444 }
.result.pass{ color:#10b981 }
</style>
