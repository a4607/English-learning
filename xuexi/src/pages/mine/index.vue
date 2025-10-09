<template>
  <view class="mine-page">
    <view class="profile">
      <image class="avatar" src="/static/logo.png" mode="aspectFill"></image>
      <view class="info">
        <text class="name">未登录用户</text>
        <text class="desc">连续学习 {{ stats.streak }} 天 · 星星 {{ stats.stars }}</text>
      </view>
      <button class="login-btn" @click="login">登录/绑定</button>
    </view>

    <view class="stat-cards">
      <view class="card">
        <text class="num">{{ stats.streak }}</text>
        <text class="label">学习天数</text>
      </view>
      <view class="card">
        <text class="num">{{ stats.words }}</text>
        <text class="label">掌握词汇</text>
      </view>
      <view class="card">
        <text class="num">{{ stats.lessons }}</text>
        <text class="label">完成课时</text>
      </view>
    </view>

    <view class="menu">
      <view class="menu-item" @click="goRecord">
        <text class="m-icon">📝</text>
        <view class="m-texts">
          <text class="m-title">学习记录</text>
          <text class="m-sub">查看最近的学习情况</text>
        </view>
        <text class="m-arrow">›</text>
      </view>

      <view class="menu-item" @click="goSettings">
        <text class="m-icon">⚙️</text>
        <view class="m-texts">
          <text class="m-title">设置</text>
          <text class="m-sub">语音、提醒、外观</text>
        </view>
        <text class="m-arrow">›</text>
      </view>

      <view class="menu-item" @click="contact">
        <text class="m-icon">📮</text>
        <view class="m-texts">
          <text class="m-title">反馈与帮助</text>
          <text class="m-sub">遇到问题？点此联系</text>
        </view>
        <text class="m-arrow">›</text>
      </view>
    </view>

    <!-- 新底部导航（仿多邻国） -->
    <view class="duo-bottom">
      <view class="duo-item" @click="goToSync"><image class="duo-icon" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/fbe0c187341c280e161f76fb4cbda1d7.svg" /></view>
      <view class="duo-item" @click="goToPronounce"><image class="duo-icon" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/3b4928101472fce4e9edac920c1b3817.svg" /></view>
      <view class="duo-item" @click="goToQuests"><image class="duo-icon" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/5d2ba4a4504db1b554515043e94cc7da.svg" /></view>
      <view class="duo-item" @click="goToShop"><image class="duo-icon" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/0e58a94dda219766d98c7796b910beee.svg" /></view>
      <view class="duo-item active"><image class="duo-icon" src="https://d35aaqx5ub95lt.cloudfront.net/vendor/e93ac282acf802a6258c761d3e9f9888.svg" /></view>
    </view>
  </view>
</template>

<script setup>
import { reactive } from 'vue'

const stats = reactive({ streak: 1, stars: 10, words: 35, lessons: 6 })
const login = () => uni.showToast({ title: '模拟登录', icon: 'none' })
const goRecord = () => uni.showToast({ title: '学习记录', icon: 'none' })
const goSettings = () => uni.showToast({ title: '设置', icon: 'none' })
const contact = () => uni.showToast({ title: '反馈与帮助', icon: 'none' })

// 底部导航
const goToSync = () => { uni.reLaunch ? uni.reLaunch({ url: '/pages/index/index' }) : uni.navigateTo({ url: '/pages/index/index' }) }
const goToPronounce = () => { uni.navigateTo({ url: '/pages/index/readaloud/index' }) }
const goToQuests = () => { uni.navigateTo({ url: '/pages/preview/index' }) }
const goToShop = () => { uni.showToast({ title: '小店（占位）', icon: 'none' }) }
</script>

<style scoped>
.mine-page { min-height: 100vh; background: var(--duo-surface); padding-bottom: 64px }
.profile { display:flex; align-items:center; padding:16px; gap:12px }
.avatar { width:56px; height:56px; border-radius:12px; background:#f3f4f6 }
.info { flex:1 }
.name { display:block; font-size:16px; font-weight:800; color: var(--duo-text) }
.desc { display:block; margin-top:4px; color: var(--duo-muted); font-size:12px }
.login-btn { border:1px solid var(--duo-primary); color: var(--duo-primary); background:#fff; border-radius:10px; padding:6px 10px; font-size:12px }

.stat-cards { display:flex; padding:0 16px 8px; gap:12px }
.card { flex:1; border:1px solid var(--duo-border); border-radius: var(--duo-radius); padding:12px; text-align:center; box-shadow: var(--duo-shadow) }
.num { display:block; font-size:18px; font-weight:800; color: var(--duo-text) }
.label { display:block; margin-top:6px; font-size:12px; color: var(--duo-muted) }

.menu { padding:8px 16px; display:flex; flex-direction:column; gap:10px }
.menu-item { display:flex; align-items:center; gap:12px; padding:14px; border:1px solid var(--duo-border); border-radius: var(--duo-radius); box-shadow: var(--duo-shadow) }
.m-icon { font-size:20px }
.m-texts { flex:1 }
.m-title { display:block; font-size:14px; font-weight:700; color: var(--duo-text) }
.m-sub { display:block; margin-top:4px; font-size:12px; color: var(--duo-muted) }
.m-arrow { color:#9ca3af }

/* 新底部导航 */
.duo-bottom { position: fixed; left:0; right:0; bottom:0; display:flex; justify-content:space-around; align-items:center; background:#fff; border-top:1px solid var(--duo-border); padding:8px 0; z-index:10000 }
.duo-item { padding:6px 10px; border-radius:10px }
.duo-item.active { background:#eef7e6 }
.duo-icon { width:28px; height:28px }
</style>

