<template>
  <view class="preview-page">
    <view class="preview-header">
      <view class="header-nav">
        <view class="back-arrow" @click="goBack">⟵</view>
        <text class="preview-title">预习内容检测</text>
      </view>
    </view>
    <view class="preview-content">
      <view class="card">
        <text class="title">今日目标</text>
        <view class="section">
          <text class="section-title">Vocabulary</text>
          <view class="list">
            <text v-for="(w, i) in vocab" :key="i" class="item">{{ w }}</text>
          </view>
        </view>
        <view class="section">
          <text class="section-title">Language focus</text>
          <view class="list">
            <text v-for="(s, i) in focus" :key="i" class="item">{{ s }}</text>
          </view>
        </view>
        <button class="start-btn" @click="startQuiz">开始学习</button>
      </view>
    </view>
  </view>

</template>

<script setup>
import { computed, ref } from 'vue'
import { onLoad } from '@dcloudio/uni-app'
import units from '@/data/units.js'

// 使用响应式的单元索引
const unitIndex = ref(0)
const unit = computed(() => units[unitIndex.value] || units[0])

const vocab = computed(() => {
  console.log('当前单元词汇:', unit.value.knowledge.Vocabulary)
  return unit.value.knowledge.Vocabulary
})
const focus = computed(() => {
  console.log('当前单元句子:', unit.value.knowledge['Language focus'])
  return unit.value.knowledge['Language focus']
})

// 页面生命周期函数
onLoad((options) => {
  console.log('preview页面接收到的参数:', options)
  if (options && options.unit !== undefined) {
    unitIndex.value = Number(options.unit) || 0
    console.log('更新单元索引为:', unitIndex.value)
  }
})

const startQuiz = () => {
  uni.navigateTo({ url: `/pages/quiz/index?title=预习内容检测&unit=${unitIndex.value}` })
}

const goBack = () => { uni.navigateBack && uni.navigateBack() }
</script>

<style scoped>
.preview-page {
  min-height: 100vh;
  background: #fff;
  display: flex;
  flex-direction: column;
  padding-top: constant(safe-area-inset-top);
  padding-top: env(safe-area-inset-top);
  box-sizing: border-box;
  width: 100%;
  overflow-x: hidden;
}

.preview-header {
  padding: 12px 16px;
  border-bottom: 1px solid #e5e7eb;
}

.header-nav {
  display: flex;
  align-items: center;
}

.back-arrow {
  font-size: 24px;
  color: #111;
  margin-right: 16px;
  padding: 8px;
}

.preview-title {
  font-size: 20px;
  font-weight: 800;
  color: #111;
}

.preview-content {
  flex: 1;
  padding: 12px 16px;
  overflow-y: auto;
}

.card {
  width: 100%;
  max-width: 100%;
  background: #fff;
  border: 1px solid #e5e7eb;
  border-radius: 16px;
  padding: 16px;
  box-sizing: border-box;
  margin: 0 auto;
}

.title {
  display: block;
  font-size: 16px;
  font-weight: 800;
  color: #111;
  margin-bottom: 10px
}

.section {
  margin: 10px 0
}

.section-title {
  display: block;
  font-size: 14px;
  font-weight: 700;
  color: #111;
  margin-bottom: 6px
}

.list {
  display: flex;
  flex-direction: column;
  gap: 6px
}

.item {
  font-size: 14px;
  color: #111
}

.start-btn {
  margin-top: 12px;
  width: 100%;
  padding: 10px 0;
  border: 1px solid #3b82f6;
  color: #3b82f6;
  background: #fff;
  border-radius: 10px
}
</style>
