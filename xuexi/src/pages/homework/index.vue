<template>
  <view class="homework-page">
    <view class="header">
      <text class="title">本周作业</text>
      <text class="sub">根据老师布置完成以下任务</text>
    </view>

    <scroll-view class="list" scroll-y>
      <view v-for="task in tasks" :key="task.id" class="task-card" :class="{ done: task.done }" @click="openTask(task)">
        <view class="task-left">
          <view class="task-icon">{{ task.icon }}</view>
        </view>
        <view class="task-body">
          <text class="task-title">{{ task.title }}</text>
          <text class="task-desc">{{ task.desc }}</text>
          <view class="meta">
            <text class="meta-item">截止：{{ task.due }}</text>
            <text class="meta-item" :class="{ highlight: task.urgent }">{{ task.urgent ? '紧急' : '常规' }}</text>
          </view>
        </view>
        <view class="task-right">
          <view class="status-dot" :class="{ ok: task.done }"></view>
          <text class="status-text">{{ task.done ? '已完成' : '待完成' }}</text>
          <button class="cta" @click.stop="startTask(task)">{{ task.done ? '查看' : '去完成' }}</button>
        </view>
      </view>
    </scroll-view>

    <view class="bottom-nav">
      <view class="nav-item" @click="goToSync">
        <text class="nav-icon">🎓</text>
        <text class="nav-label">同步学</text>
      </view>
      <view class="nav-item active">
        <text class="nav-icon">📝</text>
        <text class="nav-label">作业</text>
        <view class="active-underline"></view>
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
import { ref } from 'vue'

const tasks = ref([
  { id: 1, title: '预习词汇与句子', desc: '完成当前单元词句预习', due: '本周五', icon: '📚', route: '/pages/vocab/index', urgent: false, done: false },
  { id: 2, title: '预习内容检测', desc: '完成本课时检查题', due: '本周五', icon: '✅', route: '/pages/preview/index', urgent: true, done: false },
  { id: 3, title: '口语跟读练习', desc: '至少完成10个词/句的跟读', due: '本周日', icon: '🎤', route: '/pages/vocab/index', urgent: false, done: false }
])

const openTask = (task) => {
  uni.showToast({ title: task.title, icon: 'none' })
}

const startTask = (task) => {
  if (task.route) {
    uni.navigateTo({ url: task.route })
  }
}

// 底部导航功能
const goToSync = () => { uni.navigateTo({ url: '/pages/index/index' }) }
const goToExpand = () => { uni.navigateTo({ url: '/pages/expand/index' }) }
const goToMine = () => { uni.navigateTo({ url: '/pages/mine/index' }) }
</script>

<style scoped>
.homework-page {
  min-height: 100vh;
  background: #fff;
  display: flex;
  flex-direction: column;
  padding-bottom: 64px;
}

.header {
  padding: 16px;
}

.title {
  display: block;
  font-size: 20px;
  font-weight: 800;
  color: #111;
}

.sub {
  display: block;
  margin-top: 6px;
  color: #6b7280;
  font-size: 12px;
}

.list { 
  flex: 1; 
  padding: 8px 16px; 
}

.task-card {
  display: flex;
  gap: 12px;
  padding: 16px;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  margin-bottom: 12px;
  align-items: center;
}

.task-card.done { opacity: .7 }

.task-left { width: 44px; height: 44px; display: flex; align-items: center; justify-content: center; background: #f3f4f6; border-radius: 8px }
.task-icon { font-size: 22px }

.task-body { flex: 1 }
.task-title { display: block; font-size: 16px; font-weight: 700; color: #111 }
.task-desc { display: block; font-size: 12px; color: #6b7280; margin-top: 4px }
.meta { margin-top: 8px; display: flex; gap: 10px }
.meta-item { font-size: 12px; color: #6b7280 }
.meta-item.highlight { color: #ef4444 }

.task-right { display: flex; flex-direction: column; align-items: flex-end; gap: 8px }
.status-dot { width: 10px; height: 10px; border-radius: 50%; background: #f59e0b }
.status-dot.ok { background: #22c55e }
.status-text { font-size: 12px; color: #6b7280 }
.cta { border: none; background: #3b82f6; color: #fff; padding: 8px 12px; border-radius: 10px; font-size: 12px }

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

.nav-item { display: flex; flex-direction: column; align-items: center; gap: 6px; padding: 10px 12px; border-radius: 8px; position: relative }
.nav-item.active .nav-icon, .nav-item.active .nav-label { color: #3b82f6 }
.active-underline { position: absolute; bottom: 4px; width: 28px; height: 3px; border-radius: 3px; background: #3b82f6 }
.nav-icon { font-size: 26px; color: #111 }
.nav-label { font-size: 10px; color: #6b7280; font-weight: 500 }
</style>

