### Vue3  刮奖组件

一个使用Vue3 + typescript 实现的刮刮乐,刮奖组件,canvas宽高根据用户设置的奖品样式自适应宽高

[Vue2版本](https://github.com/Choicc/ScratchCard)



### 效果

![image](https://raw.githubusercontent.com/Choicc/ScratchCard/master/%E6%95%88%E6%9E%9C%E5%9B%BE.png)

### 使用教程

1.将/src/components下ScratchCard.vue复制到自己的项目中

2.在需要的页面导入完成配置,并将自定义奖品内容放入默认插槽,如**/src/App.vue** 

```vue
<script setup>
import StratchCard from "./components/StratchCard.vue";
const scratchStart = ()=>{
  console.log('scratchStart')
}
const scratchEnd = ()=>{
  console.log('scratchEnd')
}
const scratchAll = ()=>{
  console.log('scratchAll')
}
</script>

<template>
  <StratchCard
    maskColor="skyblue"
    fillStyle="red"
    font="30px 微软雅黑"
    text="刮一刮文字"
    :radius="5"
    :scratchRadius="20"
    :scratchPercent="80"
    @scratchStart="scratchStart"
    @scratchEnd="scratchEnd"
    @scratchAll="scratchAll"
  >
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
```

### 相关配置

#### props

组件调用时， 支持传入以下 `props`：

| 参数           | 说明             | 类型     |    默认值    |                        备注                         |
| -------------- | ---------------- | -------- | :----------: | :-------------------------------------------------: |
| maskColor      | 刮奖图层背景颜色 | `String` |  '#cccccc'   |             当`imageUrl`非空时会被覆盖              |
| text           | 刮奖图层文字     | `String` |   '刮一刮'   |             当`imageUrl`非空时会被覆盖              |
| fillStyle      | 刮奖图层文字颜色 | `String` |  '#000000'   |                          -                          |
| font           | 刮奖图层字体     | `String` | '18px Arial' |                                                     |
| imageUrl       | 刮奖图层使用图片 | `String` |      -       |                会覆盖掉刮奖图层文字                 |
| radius         | 刮奖图层圆角     | `Number` |      0       |                          -                          |
| scratchRadius  | 刮奖半径         | `Number` |      10      |                      刮奖半径                       |
| scratchPercent | 刮开占比         | `Number` |      80      | 当刮奖图层挂到`scratchPercent`百分比时,移除刮奖图层 |

#### events

组件调用时， 会触发以下事件，可供监听回调：

| 事件         | 说明       | 备注                     |
| ------------ | ---------- | ------------------------ |
| scratchStart | 开始刮卡时 | 手指触控或鼠标按下       |
| scratchEnd   | 刮卡结束时 | 手指离开或鼠标点击抬起时 |
| scratchAll   | 刮光全部时 | 刮刮卡被刮完时触发       |