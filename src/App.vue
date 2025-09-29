<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import dayjs from 'dayjs';

const haveMoney = ref(0)
const TorF = ref(false)
const trueCount = ref(0)
const falseCount = ref(1)
const incremental = ref(1)
const interval = ref(1000)
const probability = ref(0.01)
const lastSaved = ref("なし")
let timer = null
let saveTimer = null

onMounted(() => {
  const loadItemsString = localStorage.getItem('LI')
  if (loadItemsString !== null) {
    const loadItems = JSON.parse(loadItemsString)
    haveMoney.value = loadItems.haveMoney
    probability.value = loadItems.probability
  }
  saveTimer = setInterval(() => {
    let saveItems = {
      haveMoney: haveMoney.value,
      probability: probability.value,
    }
    localStorage.setItem('LI', JSON.stringify(saveItems))
    lastSaved.value = dayjs().format("YYYY/MM/DD HH:mm:ss")
  }, 60*1000)
  timer = setInterval(() => {
    if (Math.random() <= probability) {
      haveMoney.value += incremental
      TorF.value = true
      trueCount.value ++
      if (probability <= 1) {
        probability.value += 0.01
      }
      falseCount.value = 0
    }
    else {
      TorF.value = false
      falseCount.value ++
      trueCount.value = 0
    }
  }, interval.value)
})

onUnmounted(() => {
  if (timer) {
    clearInterval(timer)
  }
  if (saveTimer) {
    clearInterval(saveTimer)
  }
})
</script>

<template>
<div class="flex text-center text-white w-screen h-screen overflow-hidden bg-stone-950">
  <div class="grow flex flex-col">
    <div class="pt-5 pb-5 border-b border-white text-sky-300">
      <div class="text-4xl">所持金 ￥{{ haveMoney }}</div>
      <div>試行時間{{ interval/1000 }}秒</div>
      <div>成功率{{ probability*100 }}%</div>
    </div>
    <div class="grow flex flex-col justify-center text-2xl">
      <div v-if="TorF" class="text-green-500">True({{ trueCount }})</div>
      <div v-else class="text-red-500">False({{ falseCount }})</div>
    </div>
    <div>
      <div class="border-t">最終保存 {{ lastSaved }}</div>
    </div>
  </div>
  <div class="grow flex flex-col justify-center border-l h-full">
    <div>スキルツリー予定地</div>
  </div>
</div>
</template>