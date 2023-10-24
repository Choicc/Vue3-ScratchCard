<script setup>
import StratchCard from "./components/StratchCard.vue";
import { ref } from "vue";
const imageUrl = new URL(`./assets/thumb-1920-642551.png`, import.meta.url)

const prize = ref('我的奖品');
const scratchStart = () => {
  console.log('scratchStart')
}
const scratchEnd = () => {
  console.log('scratchEnd')
}
const scratchAll = () => {
  console.log('scratchAll')
}

const stratchCardRef = ref();
const reset = () => {
  console.log('reset')
  prize.value = '重置后我的奖品';
  stratchCardRef.value?.reset();
}
</script>

<template>
  <StratchCard ref="stratchCardRef" :scratchPercent="80" maskColor="skyblue" fillStyle="red" font="30px 微软雅黑" text="刮一刮文字"
    :imageUrl="imageUrl" :radius="5" :scratchRadius="20" @scratchStart="scratchStart" @scratchEnd="scratchEnd"
    @scratchAll="scratchAll">
    <!-- 自定义奖品内容插槽 -->
    <div class="prize">{{ prize }}</div>
  </StratchCard>

  <div class="btn-reset" @click="reset">重置</div>
</template>
<style scoped>
* {
  margin: 0;
  padding: 0;
}

.prize {
  width: 80vw;
  height: 45vw;
  display: flex;
  align-items: center;
  justify-content: center;
  background: lightcoral;
}

.btn-reset {
  display: block;
  width: 10vw;
  height: 2vw;
}
</style>