<template>
  <view class="roadmap-page">
    <view class="header">
      <button class="toggle-btn" @click="onToggle">⇄</button>
      <view class="title">
        <text class="chapter">{{ currentUnit.chapter }}</text>
        <text class="sub">{{ currentUnit.subtitle }}</text>
      </view>
      <button class="summary-btn" @click="openKnowledge">知识总结</button>
    </view>

    <scroll-view class="menu-container" scroll-y>
      <view class="menu-card" @click="goQuiz"><text class="menu-text">词句预习</text></view>
      <view class="menu-card" @click="goPreview"><text class="menu-text">预习内容检测</text></view>
      <view class="menu-card" @click="goReadAloud"><text class="menu-text">课文跟读与朗读</text></view>
      <view class="menu-card" @click="goAIDialog"><text class="menu-text">AI场景对话</text></view>
    </scroll-view>

    <!-- 知识点弹窗（当前单元） -->
    <view v-if="showKnowledge" class="knowledge-overlay" @click="closeKnowledge">
      <view class="knowledge-modal" @click.stop>
        <view class="k-header">
          <text class="k-title">{{ currentUnit.chapter }}</text>
          <text class="k-sub">{{ currentUnit.subtitle }}</text>
        </view>
        <view class="k-section" v-for="(items, title) in currentUnit.knowledge" :key="title">
          <text class="k-section-title">{{ title }}</text>
          <view class="k-list">
            <text v-for="(it, idx) in items" :key="idx" class="k-item">{{ it }}</text>
          </view>
        </view>
        <button class="k-btn" @click="closeKnowledge">关闭</button>
      </view>
    </view>

    <view class="bottom-nav">
      <view class="nav-item active" @click="goToSync">
        <text class="nav-icon">🎓</text>
        <text class="nav-label">同步学</text>
        <view class="active-underline"></view>
      </view>
      <view class="nav-item" @click="goToHomework">
        <text class="nav-icon">📝</text>
        <text class="nav-label">作业</text>
      </view>
      <view class="nav-item" @click="goToExpand">
        <text class="nav-icon">🧭</text>
        <text class="nav-label">拓展学</text>
      </view>
      <view class="nav-item" @click="goToMine">
        <text class="nav-icon">🙂</text>
        <text class="nav-label">我的</text>
      </view>
    </view>
  </view>
</template>
<script setup>
import { ref, onMounted } from 'vue'
import unitsData from '@/data/units.js'

const userStats = ref({ streak: 1, gems: 505, lives: 25 })

const units = ref(unitsData)

const currentUnitIndex = ref(0)
const currentUnit = ref(units.value[currentUnitIndex.value])

const lockedTip = ref({ visible: false, levelIndex: 0, subtitle: '' })

const goToQuiz = (level) => {
  uni.navigateTo({ url: `/pages/quiz/quiz?level=${level}` })
}

const goQuiz = () => {
  uni.navigateTo({ url: `/pages/vocab/index?unit=${currentUnitIndex.value}` })
}

const onToggle = () => {
  currentUnitIndex.value = (currentUnitIndex.value + 1) % units.value.length
  currentUnit.value = units.value[currentUnitIndex.value]
}

const goPreview = () => {
  uni.navigateTo({ url: `/pages/preview/index?unit=${currentUnitIndex.value}` })
}

const goReadAloud = () => {
  uni.navigateTo({ url: `/pages/index/readaloud/index?unit=${currentUnitIndex.value}` })
}

const goAIDialog = () => {
  uni.navigateTo({ url: '/pages/index/aidialog/index' })
}

const onNodeClick = (lv, index) => {
  if (lv.status === 'locked') {
    lockedTip.value = { visible: true, levelIndex: index, subtitle: lv.subtitle }
    return
  }
  goToQuiz(lv.id)
}

const hideLockedTip = () => {
  lockedTip.value.visible = false
}

const restoreUnlockState = () => {
  // 移除levels相关逻辑，因为没有levels数据结构
  console.log('restoreUnlockState function called')
}

onMounted(() => {
  setTimeout(() => { }, 0)
  restoreUnlockState()
})

// 知识点弹窗控制
const showKnowledge = ref(false)
const openKnowledge = () => { showKnowledge.value = true }
const closeKnowledge = () => { showKnowledge.value = false }

// 底部导航功能
const goToSync = () => {
  // 当前页面就是同步学，不需要跳转
}

const goToHomework = () => {
  uni.navigateTo({ url: '/pages/homework/index' })
}

const goToExpand = () => {
  uni.navigateTo({ url: '/pages/expand/index' })
}

const goToMine = () => {
  uni.navigateTo({ url: '/pages/mine/index' })
}
</script>

<style scoped>
.roadmap-page {
  min-height: 100vh;
  background: #fff;
  display: flex;
  flex-direction: column;
  padding-bottom: 64px;
  padding-top: constant(safe-area-inset-top);
  padding-top: env(safe-area-inset-top);
  overflow-x: hidden
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px;
  box-sizing: border-box
}

.toggle-btn {
  width: 34px;
  height: 34px;
  border: 1px solid #e5e7eb;
  border-radius: 10px;
  background: #fff;
  color: #111;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 16px
}

.title {
  display: flex;
  flex-direction: column
}

.chapter {
  font-size: 20px;
  font-weight: 800;
  color: #111
}

.sub {
  font-size: 16px;
  color: #333;
  margin-top: 6px
}

.summary-btn {
  border: 1px solid #3b82f6;
  color: #3b82f6;
  background: #fff;
  padding: 6px 12px;
  border-radius: 10px
}

.menu-container {
  flex: 1;
  padding: 12px 16px;
  overflow-x: hidden;
  box-sizing: border-box;
  width: 100%
}

.menu-card {
  background: #fff;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  padding: 22px 18px;
  margin: 0 0 16px 0;
  box-shadow: 0 1px 2px rgba(0, 0, 0, .04);
  width: 100%;
  max-width: 100%;
  box-sizing: border-box;
  display: block
}

.menu-text {
  font-size: 18px;
  font-weight: 800;
  color: #111
}

/* 知识点弹窗样式 */
.knowledge-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, .5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10001
}

.knowledge-modal {
  width: 86%;
  max-width: 420px;
  background: #fff;
  border: 1px solid #e5e7eb;
  border-radius: 16px;
  padding: 16px 16px 20px
}

.k-header {
  margin-bottom: 10px
}

.k-title {
  display: block;
  font-size: 18px;
  font-weight: 800;
  color: #111
}

.k-sub {
  display: block;
  font-size: 14px;
  color: #374151;
  margin-top: 6px
}

.k-section {
  margin-top: 16px
}

.k-section-title {
  display: block;
  font-size: 16px;
  font-weight: 800;
  color: #111;
  margin-bottom: 8px
}

.k-list {
  display: flex;
  flex-direction: column;
  gap: 8px
}

.k-item {
  font-size: 14px;
  color: #111
}

.k-btn {
  margin-top: 16px;
  border: 1px solid #3b82f6;
  color: #3b82f6;
  background: #fff;
  border-radius: 10px;
  padding: 8px
}

.bottom-nav {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: #fff;
  display: flex;
  justify-content: space-around;
  padding: 8px 0;
  border-top: 1px solid #e5e7eb;
  z-index: 10000
}

.nav-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 6px;
  padding: 10px 12px;
  border-radius: 8px;
  position: relative
}

.nav-item.active .nav-icon,
.nav-item.active .nav-label {
  color: #3b82f6
}

.active-underline {
  position: absolute;
  bottom: 4px;
  width: 28px;
  height: 3px;
  border-radius: 3px;
  background: #3b82f6
}

.nav-icon {
  font-size: 26px;
  color: #111
}

.nav-label {
  font-size: 10px;
  color: #6b7280;
  font-weight: 500
}

.locked-tip-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, .4);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999
}

.locked-tip {
  width: 80%;
  max-width: 360px;
  background: rgba(255, 255, 255, .9);
  border-radius: 12px;
  padding: 16px;
  color: #333;
  box-shadow: 0 10px 30px rgba(0, 0, 0, .35)
}

.tip-title {
  display: block;
  font-size: 16px;
  font-weight: 700;
  margin-bottom: 8px
}

.tip-desc {
  display: block;
  font-size: 14px;
  color: #666;
  margin-bottom: 12px
}

.tip-btn {
  width: 100%;
  padding: 10px 0;
  border: none;
  border-radius: 10px;
  background: #e5e5e5;
  color: #9a9a9a;
  font-weight: 700
}
</style>
