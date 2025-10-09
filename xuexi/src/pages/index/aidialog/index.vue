<template>
  <view class="ai-page">
    <view class="header">
      <view class="back" @click="goBack">⟵</view>
      <text class="title">AI 场景对话</text>
    </view>

    <scroll-view class="dialog" scroll-y :scroll-into-view="lastId">
      <view v-for="m in messages" :key="m.id" :id="m.id" class="msg" :class="m.role">
        <text class="bubble">{{ m.text }}</text>
      </view>
    </scroll-view>

    <view class="composer">
      <input class="input" v-model="input" placeholder="用英语聊聊今天在学校的一件事..." />
      <button class="send" :disabled="!input.trim()" @click="send">发送</button>
    </view>
  </view>
</template>

<script setup>
import { ref, computed } from 'vue'

const messages = ref([
  { id: 'm1', role: 'ai', text: 'Hi! I am your AI speaking partner. How are you today?' }
])
const input = ref('')
const lastId = computed(() => messages.value.length ? messages.value[messages.value.length - 1].id : '')

const send = () => {
  const text = input.value.trim()
  if (!text) return
  const uid = 'u' + Date.now()
  messages.value.push({ id: uid, role: 'user', text })
  input.value = ''

  // 简易本地应答，模拟AI
  setTimeout(() => {
    const aid = 'a' + Date.now()
    const reply = simpleReply(text)
    messages.value.push({ id: aid, role: 'ai', text: reply })
  }, 400)
}

const simpleReply = (t) => {
  const lower = t.toLowerCase()
  if (lower.includes('school')) return 'That sounds interesting! What subject do you like most at school?'
  if (lower.includes('good') || lower.includes('fine')) return 'Glad to hear that! What did you learn today?'
  if (lower.includes('name')) return 'My name is StudyBot. Nice to meet you!'
  return 'I see. Could you tell me more? Try using full sentences.'
}

const goBack = () => { uni.navigateBack && uni.navigateBack() }
</script>

<style scoped>
.ai-page{ min-height:100vh; background:#fff; display:flex; flex-direction:column }
.header{ display:flex; align-items:center; gap:12px; padding:12px 16px; border-bottom:1px solid #e5e7eb }
.back{ font-size:22px; color:#111 }
.title{ font-size:18px; font-weight:800; color:#111 }
.dialog{ flex:1; padding:12px 16px }
.msg{ display:flex; margin:8px 0 }
.msg .bubble{ max-width:70%; padding:10px 12px; border-radius:12px; font-size:14px; line-height:1.4 }
.msg.user{ justify-content:flex-end }
.msg.user .bubble{ background:#3b82f6; color:#fff; border-bottom-right-radius:4px }
.msg.ai{ justify-content:flex-start }
.msg.ai .bubble{ background:#f3f4f6; color:#111; border-bottom-left-radius:4px }
.composer{ display:flex; gap:8px; padding:10px 12px; border-top:1px solid #e5e7eb }
.input{ flex:1; border:1px solid #e5e7eb; border-radius:10px; padding:10px 12px; font-size:14px }
.send{ border:none; background:#10b981; color:#fff; border-radius:10px; padding:0 16px }
.send:disabled{ opacity:.5 }
</style>
