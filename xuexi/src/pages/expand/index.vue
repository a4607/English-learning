<template>
  <view class="expand-page">
    <view class="header">
      <button class="toggle-btn" @click="onToggle">⇄</button>
      <view class="title">
        <text class="chapter">拓展学习</text>
        <text class="sub">扩展你的知识边界</text>
      </view>
      <button class="summary-btn" @click="openKnowledge">知识总结</button>
    </view>

    <scroll-view class="menu-container" scroll-y>
      <view class="menu-card" @click="goAdvancedQuiz">
        <text class="menu-text">高级词汇练习</text>
      </view>
      <view class="menu-card" @click="goGrammar">
        <text class="menu-text">语法进阶</text>
      </view>
      <view class="menu-card" @click="goReading">
        <text class="menu-text">阅读理解</text>
      </view>
      <view class="menu-card" @click="goWriting">
        <text class="menu-text">写作练习</text>
      </view>
      <view class="menu-card" @click="goSpeaking">
        <text class="menu-text">口语训练</text>
      </view>
      <view class="menu-card" @click="goCulture">
        <text class="menu-text">文化知识</text>
      </view>
    </scroll-view>

    <!-- 知识点弹窗 -->
    <view v-if="showKnowledge" class="knowledge-overlay" @click="closeKnowledge">
      <view class="knowledge-modal" @click.stop>
        <view class="k-header">
          <text class="k-title">拓展学习</text>
          <text class="k-sub">扩展你的知识边界</text>
        </view>
        <view class="k-section">
          <text class="k-section-title">学习内容</text>
          <view class="k-list">
            <text class="k-item">• 高级词汇练习</text>
            <text class="k-item">• 语法进阶</text>
            <text class="k-item">• 阅读理解</text>
            <text class="k-item">• 写作练习</text>
            <text class="k-item">• 口语训练</text>
            <text class="k-item">• 文化知识</text>
          </view>
        </view>
        <button class="k-btn" @click="closeKnowledge">关闭</button>
      </view>
    </view>

    <view class="bottom-nav">
      <view class="nav-item" @click="goToSync">
        <text class="nav-icon">🎓</text>
        <text class="nav-label">同步学</text>
      </view>
      <view class="nav-item" @click="goToHomework">
        <text class="nav-icon">📝</text>
        <text class="nav-label">作业</text>
      </view>
      <view class="nav-item active">
        <text class="nav-icon">🧭</text>
        <text class="nav-label">拓展学</text>
        <view class="active-underline"></view>
      </view>
      <view class="nav-item" @click="goToMine">
        <text class="nav-icon">🙂</text>
        <text class="nav-label">我的</text>
      </view>
    </view>
  </view>
</template>

<script setup>
import { ref } from 'vue'

// 知识点弹窗控制
const showKnowledge = ref(false)
const openKnowledge = () => { showKnowledge.value = true }
const closeKnowledge = () => { showKnowledge.value = false }

// 切换功能
const onToggle = () => {
  console.log('切换拓展学习内容')
}

// 拓展学习功能
const goAdvancedQuiz = () => { uni.navigateTo({ url: '/pages/grammar/index' }) }
const goGrammar = () => { uni.navigateTo({ url: '/pages/grammar/index' }) }
const goReading = () => { uni.navigateTo({ url: '/pages/reading/index' }) }
const goWriting = () => { uni.navigateTo({ url: '/pages/writing/index' }) }
const goSpeaking = () => { uni.navigateTo({ url: '/pages/speaking/index' }) }
const goCulture = () => { uni.showToast({ title: '敬请期待', icon: 'none' }) }

// 底部导航功能
const goToSync = () => { uni.navigateTo({ url: '/pages/index/index' }) }
const goToHomework = () => { uni.navigateTo({ url: '/pages/homework/index' }) }
const goToMine = () => { uni.navigateTo({ url: '/pages/mine/index' }) }
</script>

<style scoped>
.expand-page {
  min-height: 100vh;
  background: var(--duo-surface);
  display: flex;
  flex-direction: column;
  padding-bottom: 64px;
  padding-top: constant(safe-area-inset-top);
  padding-top: env(safe-area-inset-top);
  overflow-x: hidden
}

.header { display:flex; align-items:center; justify-content:space-between; padding:16px }
.toggle-btn { width:34px; height:34px; border:1px solid var(--duo-border); border-radius:10px; background:#fff; color:var(--duo-text); display:flex; align-items:center; justify-content:center; font-size:16px }
.title { display:flex; flex-direction:column }
.chapter { font-size:20px; font-weight:800; color:var(--duo-text) }
.sub { font-size:16px; color:#333; margin-top:6px }
.summary-btn { border:1px solid var(--duo-primary); color:var(--duo-primary); background:#fff; padding:6px 12px; border-radius:10px }
.menu-container { flex:1; padding:12px 16px; overflow-x:hidden; box-sizing:border-box; width:100% }
.menu-card { background:#fff; border:1px solid var(--duo-border); border-radius:var(--duo-radius); padding:22px 18px; margin:0 0 16px 0; box-shadow: var(--duo-shadow); width:100%; display:block }
.menu-card:active{ transform: scale(0.997) }
.menu-text { font-size:18px; font-weight:800; color:var(--duo-text) }
.knowledge-overlay { position:fixed; inset:0; background:rgba(0,0,0,.5); display:flex; align-items:center; justify-content:center; z-index:10001 }
.knowledge-modal { width:86%; max-width:420px; background:#fff; border:1px solid var(--duo-border); border-radius:16px; padding:16px 16px 20px }
.k-header{ margin-bottom:10px }
.k-title{ display:block; font-size:18px; font-weight:800; color:var(--duo-text) }
.k-sub{ display:block; font-size:14px; color:#374151; margin-top:6px }
.k-section{ margin-top:16px }
.k-section-title{ display:block; font-size:16px; font-weight:800; color:var(--duo-text); margin-bottom:8px }
.k-list{ display:flex; flex-direction:column; gap:8px }
.k-item{ font-size:14px; color:var(--duo-text) }
.k-btn{ margin-top:16px; border:1px solid var(--duo-primary); color:var(--duo-primary); background:#fff; border-radius:10px; padding:8px }
.bottom-nav{ position:fixed; bottom:0; left:0; right:0; background:#fff; display:flex; justify-content:space-around; padding:8px 0; border-top:1px solid var(--duo-border); z-index:10000 }
.nav-item{ display:flex; flex-direction:column; align-items:center; gap:6px; padding:10px 12px; border-radius:8px; position:relative }
.nav-item.active .nav-icon, .nav-item.active .nav-label{ color:var(--duo-primary) }
.active-underline{ position:absolute; bottom:4px; width:28px; height:3px; border-radius:3px; background:var(--duo-primary) }
.nav-icon{ font-size:26px; color:var(--duo-text) }
.nav-label{ font-size:10px; color:#6b7280; font-weight:500 }
</style>
