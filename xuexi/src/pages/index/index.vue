<template>
  <view class="roadmap-page">
    <view class="header">
      <button class="toggle-btn" @click="onToggle">⇄</button>
      <view class="title">
        <text class="chapter">{{ currentUnit.chapter }}</text>
        <text class="sub">{{ currentUnit.subtitle }}</text>
      </view>
    </view>

    <!-- 顶部状态栏（仿多邻国） -->
    <view class="topbar">
      <view class="tb-item" @click="openCourseMenu">
        <view class="flag">
          <image class="flag-img" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/87938207afff1598611ba626a8c4827c.svg" mode="aspectFit" />
          <text class="flag-level">5</text>
        </view>
      </view>
      <view class="tb-item" @click="openStreak">
        <view class="streak">
          <image class="streak-img" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/ba95e6081679d9d7e8c132da5cfce1ec.svg" mode="aspectFit" />
          <text class="streak-num">{{ userStats.streak }}</text>
        </view>
      </view>
      <view class="tb-item" @click="openGems">
        <image class="gem-img" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/45c14e05be9c1af1d7d0b54c6eed7eee.svg" mode="aspectFit" />
        <text class="gem-num">{{ userStats.gems }}</text>
      </view>
      <view class="tb-item" @click="openHearts">
        <view class="hearts">
          <image class="heart-img" src="https://d35aaqx5ub95lt.cloudfront.net/images/hearts/8fdba477c56a8eeb23f0f7e67fdec6d9.svg" mode="aspectFit" />
          <text class="heart-num">{{ userStats.lives }}</text>
        </view>
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

    <!-- 新底部导航（仿多邻国） -->
    <view class="duo-bottom">
      <view class="duo-item active" @click="goToSync">
        <image class="duo-icon" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/fbe0c187341c280e161f76fb4cbda1d7.svg" mode="aspectFit" />
      </view>
      <view class="duo-item" @click="goReadAloud">
        <image class="duo-icon" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/3b4928101472fce4e9edac920c1b3817.svg" mode="aspectFit" />
      </view>
      <view class="duo-item" @click="goPreview">
        <image class="duo-icon" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/5d2ba4a4504db1b554515043e94cc7da.svg" mode="aspectFit" />
      </view>
      <view class="duo-item" @click="goToExpand">
        <image class="duo-icon" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/0e58a94dda219766d98c7796b910beee.svg" mode="aspectFit" />
      </view>
      <view class="duo-item" @click="goToMine">
        <image class="duo-icon" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/e93ac282acf802a6258c761d3e9f9888.svg" mode="aspectFit" />
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
const goToSync = () => {}
const goToHomework = () => { uni.navigateTo({ url: '/pages/homework/index' }) }
const goToExpand = () => { uni.navigateTo({ url: '/pages/expand/index' }) }
const goToMine = () => { uni.navigateTo({ url: '/pages/mine/index' }) }

const openCourseMenu = () => { uni.showToast({ title: '课程菜单', icon: 'none' }) }
const openStreak = () => { uni.showToast({ title: '连续天数', icon: 'none' }) }
const openGems = () => { uni.showToast({ title: '宝石：' + userStats.value.gems, icon: 'none' }) }
const openHearts = () => { uni.showToast({ title: '生命：' + userStats.value.lives, icon: 'none' }) }
</script>

<style scoped>
.roadmap-page { min-height: 100vh; background: #fff; display: flex; flex-direction: column; padding-bottom: 64px; padding-top: constant(safe-area-inset-top); padding-top: env(safe-area-inset-top); overflow-x: hidden }
.header { display: flex; align-items: center; justify-content: space-between; padding: 16px; box-sizing: border-box }
.toggle-btn { width: 34px; height: 34px; border: 1px solid #e5e7eb; border-radius: 10px; background: #fff; color: #111; display: flex; align-items: center; justify-content: center; font-size: 16px }
.title { display: flex; flex-direction: column }
.chapter { font-size: 20px; font-weight: 800; color: #111 }
.sub { font-size: 16px; color: #333; margin-top: 6px }

/* 顶部状态栏样式 */
.topbar { display:flex; align-items:center; gap:14px; padding:6px 16px 10px }
.tb-item { display:flex; align-items:center }
.flag { position:relative; width:46px; height:37px }
.flag-img { width:46px; height:37px }
.flag-level { position:absolute; right:-8px; top:-6px; background:#10b981; color:#fff; border-radius:999px; font-size:12px; padding:2px 6px; font-weight:700 }
.streak { display:flex; align-items:center; gap:6px }
.streak-img { width:32px; height:32px }
.streak-num { font-weight:700; color:#111 }
.gem-img { width:28px; height:28px; margin-right:6px }
.gem-num { font-weight:700; color:#111 }
.hearts { display:flex; align-items:center; gap:6px }
.heart-img { width:32px; height:32px }
.heart-num { font-weight:700; color:#111 }

.menu-container { flex: 1; padding: 12px 16px; overflow-x: hidden; box-sizing: border-box; width: 100% }
.menu-card { background: #fff; border: 1px solid #e5e7eb; border-radius: 12px; padding: 22px 18px; margin: 0 0 16px 0; box-shadow: 0 1px 2px rgba(0, 0, 0, .04); width: 100%; max-width: 100%; box-sizing: border-box; display: block }
.menu-text { font-size: 18px; font-weight: 800; color: #111 }
/* 知识点弹窗样式 */
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

/* 新底部导航 */
.duo-bottom { position: fixed; left:0; right:0; bottom:0; display:flex; justify-content:space-around; align-items:center; background:#fff; border-top:1px solid #e5e7eb; padding:8px 0; z-index:10000 }
.duo-item { padding:6px 10px; border-radius:10px }
.duo-item.active { background:#eef7e6 }
.duo-icon { width:28px; height:28px }
</style>
