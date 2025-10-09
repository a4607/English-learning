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

    <view class="bottom-nav">
      <view class="nav-item" @click="goToSync">
        <text class="nav-icon">🎓</text>
        <text class="nav-label">同步学</text>
      </view>
      <view class="nav-item" @click="goToHomework">
        <text class="nav-icon">📝</text>
        <text class="nav-label">作业</text>
      </view>
      <view class="nav-item" @click="goToExpand">
        <text class="nav-icon">🧭</text>
        <text class="nav-label">拓展学</text>
      </view>
      <view class="nav-item active">
        <text class="nav-icon">🙂</text>
        <text class="nav-label">我的</text>
        <view class="active-underline"></view>
      </view>
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
const goToSync = () => { uni.navigateTo({ url: '/pages/index/index' }) }
const goToHomework = () => { uni.navigateTo({ url: '/pages/homework/index' }) }
const goToExpand = () => { uni.navigateTo({ url: '/pages/expand/index' }) }
</script>

<style scoped>
.mine-page { min-height: 100vh; background: #fff; padding-bottom: 64px }
.profile { display: flex; align-items: center; padding: 16px; gap: 12px }
.avatar { width: 56px; height: 56px; border-radius: 12px; background: #f3f4f6 }
.info { flex: 1 }
.name { display: block; font-size: 16px; font-weight: 800; color: #111 }
.desc { display: block; margin-top: 4px; color: #6b7280; font-size: 12px }
.login-btn { border: 1px solid #3b82f6; color: #3b82f6; background: #fff; border-radius: 10px; padding: 6px 10px; font-size: 12px }

.stat-cards { display: flex; padding: 0 16px 8px; gap: 12px }
.card { flex: 1; border: 1px solid #e5e7eb; border-radius: 12px; padding: 12px; text-align: center }
.num { display: block; font-size: 18px; font-weight: 800; color: #111 }
.label { display: block; margin-top: 6px; font-size: 12px; color: #6b7280 }

.menu { padding: 8px 16px; display: flex; flex-direction: column; gap: 10px }
.menu-item { display: flex; align-items: center; gap: 12px; padding: 14px; border: 1px solid #e5e7eb; border-radius: 12px }
.m-icon { font-size: 20px }
.m-texts { flex: 1 }
.m-title { display: block; font-size: 14px; font-weight: 700; color: #111 }
.m-sub { display: block; margin-top: 4px; font-size: 12px; color: #6b7280 }
.m-arrow { color: #9ca3af }

.bottom-nav { position: fixed; bottom: 0; left: 0; right: 0; background: #fff; display: flex; justify-content: space-around; padding: 8px 0; border-top: 1px solid #e5e7eb; z-index: 10000 }
.nav-item { display: flex; flex-direction: column; align-items: center; gap: 6px; padding: 10px 12px; border-radius: 8px; position: relative }
.nav-item.active .nav-icon, .nav-item.active .nav-label { color: #3b82f6 }
.active-underline { position: absolute; bottom: 4px; width: 28px; height: 3px; border-radius: 3px; background: #3b82f6 }
.nav-icon { font-size: 26px; color: #111 }
.nav-label { font-size: 10px; color: #6b7280; font-weight: 500 }
</style>

