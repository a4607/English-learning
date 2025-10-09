<template>
  <view class="reading-page">
    <view class="header">
      <view class="back" @click="goBack">⟵</view>
      <text class="title">阅读理解</text>
    </view>

    <scroll-view class="content" scroll-y>
      <view class="passage">
        <text class="p">Ken goes to school every day. He brings a book and a pencil.</text>
        <text class="p">His friend Amy says hello to him in the morning.</text>
      </view>

      <view class="qa">
        <text class="q">1. What does Ken bring? (book/pencil/both)</text>
        <input class="input" v-model="a1" placeholder="Your answer" />
      </view>

      <button class="submit" @click="submit">提交</button>
    </scroll-view>
  </view>
</template>

<script setup>
import { ref } from 'vue'
const a1 = ref('')
const submit = () => {
  const ok = a1.value.trim().toLowerCase().includes('both')
  uni.showToast({ title: ok ? '正确' : '答案：both', icon: 'none' })
}
const goBack = () => uni.navigateBack && uni.navigateBack()
</script>

<style scoped>
.reading-page{ min-height:100vh; background:#fff; display:flex; flex-direction:column }
.header{ display:flex; align-items:center; gap:12px; padding:12px 16px; border-bottom:1px solid #e5e7eb }
.back{ font-size:22px; color:#111 }
.title{ font-size:18px; font-weight:800; color:#111 }
.content{ flex:1; padding:12px 16px }
.passage{ border:1px solid #e5e7eb; border-radius:12px; padding:12px }
.p{ display:block; color:#111; line-height:1.6; margin-bottom:6px }
.qa{ margin:12px 0 }
.q{ display:block; color:#111; margin-bottom:8px }
.input{ border:1px solid #e5e7eb; border-radius:10px; padding:8px 12px }
.submit{ margin:12px 0; border:none; background:#10b981; color:#fff; border-radius:10px; padding:10px 16px }
</style>
