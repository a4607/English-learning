<template>
  <view class="writing-page">
    <view class="header">
      <view class="back" @click="goBack">⟵</view>
      <text class="title">写作练习</text>
    </view>

    <view class="content">
      <text class="prompt">用英文写 3-5 句介绍你的书包和你带到学校的物品。</text>
      <textarea class="editor" v-model="text" placeholder="I bring ..."></textarea>
      <view class="meta">
        <text>字数：{{ count }}</text>
      </view>
      <button class="submit" @click="submit">提交</button>
    </view>
  </view>
</template>

<script setup>
import { ref, computed } from 'vue'
const text = ref('')
const count = computed(() => text.value.trim().split(/\s+/).filter(Boolean).length)
const submit = () => {
  if (count.value < 10) return uni.showToast({ title: '至少 10 个单词', icon: 'none' })
  uni.showToast({ title: '已提交', icon: 'success' })
}
const goBack = () => uni.navigateBack && uni.navigateBack()
</script>

<style scoped>
.writing-page{ min-height:100vh; background:#fff; display:flex; flex-direction:column }
.header{ display:flex; align-items:center; gap:12px; padding:12px 16px; border-bottom:1px solid #e5e7eb }
.back{ font-size:22px; color:#111 }
.title{ font-size:18px; font-weight:800; color:#111 }
.content{ flex:1; padding:12px 16px; display:flex; flex-direction:column; gap:12px }
.prompt{ color:#111 }
.editor{ border:1px solid #e5e7eb; border-radius:12px; min-height:160px; padding:10px 12px }
.meta{ color:#6b7280; font-size:12px }
.submit{ border:none; background:#3b82f6; color:#fff; border-radius:10px; padding:10px 16px }
</style>
