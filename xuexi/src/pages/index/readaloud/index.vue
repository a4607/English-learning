<template>
  <view class="readaloud-page">
    <view class="header">
      <view class="back" @click="goBack">⟵</view>
      <text class="title">课文跟读与朗读</text>
    </view>

    <scroll-view class="content" scroll-y>
      <view class="para-card" v-for="(p, i) in paragraphs" :key="i">
        <text class="para-text">{{ p }}</text>
        <view class="actions">
          <button class="btn" @click="play(p)">播放</button>
          <button class="btn primary" @click="record(i)">{{ recordingIndex === i ? '结束' : '跟读' }}</button>
          <button class="btn" :class="{ disabled: !records.has(i) }" @click="playMy(i)">我的</button>
        </view>
      </view>
    </scroll-view>
  </view>
</template>

<script setup>
import { ref } from 'vue'

const paragraphs = ref([
  'Good morning! My name is Ken.',
  'This is my school bag. It is blue.',
  'I go to school with my friend Amy.'
])

const recordingIndex = ref(-1)
const records = ref(new Map())

const play = (text) => {
  // 简易TTS方案，H5使用Web Speech，其他端提示
  // #ifdef H5
  if ('speechSynthesis' in window) {
    const u = new SpeechSynthesisUtterance(text)
    u.lang = 'en-US'
    u.rate = 0.9
    speechSynthesis.cancel()
    speechSynthesis.speak(u)
  }
  // #endif
  // #ifndef H5
  uni.showToast({ title: '使用系统语音播放', icon: 'none' })
  // #endif
}

const record = (index) => {
  if (recordingIndex.value === index) {
    stop()
    return
  }
  start(index)
}

const start = (index) => {
  recordingIndex.value = index
  // #ifdef H5
  if (!navigator.mediaDevices) {
    uni.showToast({ title: '浏览器不支持录音', icon: 'none' })
    return
  }
  navigator.mediaDevices.getUserMedia({ audio: true }).then(stream => {
    const mr = new MediaRecorder(stream)
    const chunks = []
    mr.ondataavailable = e => chunks.push(e.data)
    mr.onstop = () => {
      const url = URL.createObjectURL(new Blob(chunks, { type: 'audio/wav' }))
      records.value.set(index, url)
      stream.getTracks().forEach(t => t.stop())
      recordingIndex.value = -1
      uni.showToast({ title: '录音完成', icon: 'success' })
    }
    mr.start();
    window.__ra_mr = mr
    uni.showToast({ title: '开始跟读', icon: 'none' })
  }).catch(() => uni.showToast({ title: '录音权限被拒绝', icon: 'none' }))
  // #endif
  // #ifndef H5
  const rm = uni.getRecorderManager()
  rm.start({ duration: 10000, format: 'mp3' })
  rm.onStop(res => {
    if (res.tempFilePath) records.value.set(index, res.tempFilePath)
    recordingIndex.value = -1
    uni.showToast({ title: '录音完成', icon: 'success' })
  })
  // #endif
}

const stop = () => {
  // #ifdef H5
  if (window.__ra_mr && window.__ra_mr.state === 'recording') window.__ra_mr.stop()
  // #endif
  // #ifndef H5
  const rm = uni.getRecorderManager(); rm.stop()
  // #endif
}

const playMy = (index) => {
  if (!records.value.has(index)) return
  const src = records.value.get(index)
  const audio = uni.createInnerAudioContext(); audio.src = src; audio.play()
}

const goBack = () => { uni.navigateBack && uni.navigateBack() }
</script>

<style scoped>
.readaloud-page{ min-height:100vh; background:#fff }
.header{ display:flex; align-items:center; gap:12px; padding:12px 16px; border-bottom:1px solid #e5e7eb }
.back{ font-size:22px; color:#111 }
.title{ font-size:18px; font-weight:800; color:#111 }
.content{ height: calc(100vh - 56px); padding:12px 16px }
.para-card{ border:1px solid #e5e7eb; border-radius:12px; padding:12px; margin-bottom:12px }
.para-text{ display:block; color:#111; font-size:16px; line-height:1.5 }
.actions{ display:flex; gap:8px; margin-top:10px }
.btn{ border:1px solid #e5e7eb; background:#fff; color:#111; border-radius:10px; padding:6px 12px; font-size:12px }
.btn.primary{ border-color:#3b82f6; color:#3b82f6 }
.btn.disabled{ opacity:.5 }
</style>
