<script setup>
import StratchCard from "./components/StratchCard.vue";
const imageUrl = new URL(`./assets/thumb-1920-642551.png`, import.meta.url)
const scratchStart = () => {
  console.log('scratchStart')
}
const scratchEnd = () => {
  console.log('scratchEnd')
}
const scratchAll = () => {
  console.log('scratchAll')
}
</script>

<template>
  <StratchCard maskColor="skyblue" fillStyle="red" font="30px 微软雅黑" text="刮一刮文字" :imageUrl="imageUrl" :radius="5"
    :scratchRadius="20" :scratchPercent="80" @scratchStart="scratchStart" @scratchEnd="scratchEnd"
    @scratchAll="scratchAll">
    <!-- 自定义奖品内容插槽 -->
    <div class="prize">我的奖品</div>
  </StratchCard>
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
</style>