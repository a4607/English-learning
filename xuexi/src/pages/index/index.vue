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

    <!-- 顶部状态与横幅（仿多邻国） -->
    <view class="status-bar">
      <view class="s-item">⭐ <text class="s-num">{{ userStats.gems }}</text></view>
      <view class="s-item">❤️ <text class="s-num">{{ userStats.lives }}</text></view>
      <view class="s-item">🔥 <text class="s-num">{{ userStats.streak }}</text></view>
    </view>
    <view class="duo-banner">
      <view class="banner-left">
        <text class="b-title">第 {{ currentUnitIndex + 1 }} 单元</text>
        <text class="b-sub">点击开始本单元学习</text>
      </view>
      <button class="b-menu" @click="openKnowledge">☰</button>
    </view>

    <view class="circle-start-wrap">
      <view class="circle">
        <view class="circle-inner"></view>
        <button class="circle-start" @click="goQuizStart">开始</button>
      </view>
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

const userStats = ref({ streak: 1, gems: 505, lives: 5 })

const units = ref(unitsData)

const currentUnitIndex = ref(0)
const currentUnit = ref(units.value[currentUnitIndex.value])

const lockedTip = ref({ visible: false, levelIndex: 0, subtitle: '' })

const goToQuiz = (level) => {
  uni.navigateTo({ url: `/pages/quiz/quiz?level=${level}` })
}

const goQuizStart = () => {
  uni.navigateTo({ url: `/pages/vocab/index?unit=${currentUnitIndex.value}` })
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
  uni.navigateTo({ url: `/pages/readaloud/index?unit=${currentUnitIndex.value}` })
}

const goAIDialog = () => {
  uni.navigateTo({ url: '/pages/aidialog/index' })
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
const goToSync = () => {}
const goToHomework = () => { uni.navigateTo({ url: '/pages/homework/index' }) }
const goToExpand = () => { uni.navigateTo({ url: '/pages/expand/index' }) }
const goToMine = () => { uni.navigateTo({ url: '/pages/mine/index' }) }
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

.header { display:flex; align-items:center; justify-content:space-between; padding:16px }
.toggle-btn { width:34px; height:34px; border:1px solid var(--duo-border); border-radius:10px; background:#fff; color:#111; display:flex; align-items:center; justify-content:center; font-size:16px }
.title { display:flex; flex-direction:column }
.chapter { font-size:20px; font-weight:800; color:#111 }
.sub { font-size:16px; color:#333; margin-top:6px }
.summary-btn { border:1px solid var(--duo-primary); color:var(--duo-primary); background:#fff; padding:6px 12px; border-radius:10px }

/* 顶部状态与横幅 */
.status-bar{ display:flex; gap:12px; padding:0 16px 8px }
.s-item{ display:flex; align-items:center; gap:6px; color:#1f2937; font-weight:700 }
.s-num{ margin-left:2px }

.duo-banner{ margin:0 16px; background:var(--duo-primary); color:#fff; border-radius:14px; display:flex; align-items:center; justify-content:space-between; padding:14px 12px; box-shadow:0 4px 12px rgba(88,204,2,.3) }
.banner-left{ display:flex; flex-direction:column }
.b-title{ font-size:14px; font-weight:800 }
.b-sub{ font-size:12px; opacity:.9; margin-top:4px }
.b-menu{ border:none; background:rgba(255,255,255,.2); color:#fff; padding:8px 10px; border-radius:10px }

.circle-start-wrap{ display:flex; justify-content:center; margin:14px 0 6px }
.circle{ width:104px; height:104px; border-radius:52px; background:#e6f7d9; position:relative; display:flex; align-items:center; justify-content:center; box-shadow:0 6px 14px rgba(0,0,0,.08) }
.circle-inner{ position:absolute; width:86px; height:86px; border-radius:43px; background:#fff }
.circle-start{ position:relative; z-index:1; border:none; background:linear-gradient(135deg, var(--duo-primary), var(--duo-primary-dark)); color:#fff; font-weight:800; padding:8px 16px; border-radius:12px; box-shadow:0 8px 16px rgba(88,204,2,.35) }

.menu-container { flex: 1; padding: 12px 16px; overflow-x: hidden; box-sizing: border-box; width: 100% }
.menu-card { background: #fff; border: 1px solid var(--duo-border); border-radius: 14px; padding: 22px 18px; margin: 0 0 16px 0; box-shadow: 0 1px 2px rgba(0, 0, 0, .06); width: 100%; max-width: 100%; box-sizing: border-box; display: block }
.menu-text { font-size: 18px; font-weight: 800; color: #111 }

/* 弹窗与底部导航保持不变 */
.knowledge-overlay { position: fixed; inset: 0; background: rgba(0,0,0,.5); display:flex; align-items:center; justify-content:center; z-index:10001 }
.knowledge-modal { width: 86%; max-width: 420px; background:#fff; border:1px solid #e5e7eb; border-radius:16px; padding:16px 16px 20px }
.k-header { margin-bottom:10px }
.k-title { display:block; font-size:18px; font-weight:800; color:#111 }
.k-sub { display:block; font-size:14px; color:#374151; margin-top:6px }
.k-section { margin-top:16px }
.k-section-title { display:block; font-size:16px; font-weight:800; color:#111; margin-bottom:8px }
.k-list { display:flex; flex-direction:column; gap:8px }
.k-item { font-size:14px; color:#111 }
.k-btn { margin-top:16px; border:1px solid #3b82f6; color:#3b82f6; background:#fff; border-radius:10px; padding:8px }

.bottom-nav { position: fixed; bottom: 0; left: 0; right: 0; background: #fff; display: flex; justify-content: space-around; padding: 8px 0; border-top: 1px solid #e5e7eb; z-index: 10000 }
.nav-item { display:flex; flex-direction:column; align-items:center; gap:6px; padding:10px 12px; border-radius:8px; position:relative }
.nav-item.active .nav-icon, .nav-item.active .nav-label { color: var(--duo-primary) }
.active-underline { position:absolute; bottom:4px; width:28px; height:3px; border-radius:3px; background:var(--duo-primary) }
.nav-icon { font-size:26px; color:#111 }
.nav-label { font-size:10px; color:#6b7280; font-weight:500 }
</style>
