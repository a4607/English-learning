<template>
  <view class="page">
    <view class="placeholder">我的页</view>
    
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
// 底部导航功能
const goToSync = () => {
  uni.navigateTo({ url: '/pages/index/index' })
}

const goToHomework = () => {
  uni.navigateTo({ url: '/pages/homework/index' })
}

const goToExpand = () => {
  uni.navigateTo({ url: '/pages/expand/index' })
}
</script>

<style scoped>
.page{
  min-height:100vh;
  background:#fff;
  padding-bottom: 64px;
}

.placeholder{
  padding:48rpx;
  text-align:center;
  color:#666
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
</style>

