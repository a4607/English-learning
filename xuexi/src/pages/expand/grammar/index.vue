<template>
  <view class="grammar-page">
    <view class="header">
      <view class="back" @click="goBack">⟵</view>
      <text class="title">语法进阶</text>
    </view>

    <scroll-view class="content" scroll-y>
      <view class="section">
        <text class="s-title">句子结构</text>
        <text class="s-desc">主语 + 谓语 + 宾语（SVO）是最常见的英语语序。</text>
      </view>

      <view class="section">
        <text class="s-title">选择题</text>
        <view class="quiz">
          <text class="q">(1) ___ name is Ken. (My/Me)</text>
          <view class="opts">
            <button class="opt" :class="{ ok: ans1==='My' }" @click="ans1='My'">My</button>
            <button class="opt" :class="{ ok: ans1==='Me' }" @click="ans1='Me'">Me</button>
          </view>
        </view>
      </view>

      <button class="submit" @click="submit">提交</button>
    </scroll-view>
  </view>
</template>

<script setup>
import { ref } from 'vue'
const ans1 = ref('')
const submit = () => {
  const correct = ans1.value === 'My'
  uni.showToast({ title: correct ? '全对！' : '还有错误', icon: 'none' })
}
const goBack = () => uni.navigateBack && uni.navigateBack()
</script>

<style scoped>
.grammar-page{ min-height:100vh; background:#fff; display:flex; flex-direction:column }
.header{ display:flex; align-items:center; gap:12px; padding:12px 16px; border-bottom:1px solid #e5e7eb }
.back{ font-size:22px; color:#111 }
.title{ font-size:18px; font-weight:800; color:#111 }
.content{ flex:1; padding:12px 16px }
.section{ border:1px solid #e5e7eb; border-radius:12px; padding:12px; margin-bottom:12px }
.s-title{ display:block; font-size:16px; font-weight:800; color:#111 }
.s-desc{ display:block; margin-top:6px; color:#374151; font-size:14px }
.quiz{ margin-top:10px }
.q{ display:block; color:#111; font-size:14px; margin-bottom:8px }
.opts{ display:flex; gap:8px }
.opt{ border:1px solid #e5e7eb; background:#fff; color:#111; border-radius:10px; padding:6px 12px; font-size:12px }
.opt.ok{ border-color:#22c55e; color:#22c55e }
.submit{ margin:12px 0; border:none; background:#3b82f6; color:#fff; border-radius:10px; padding:10px 16px }
</style>
