<!--
 * @Descripttion: 
 * @version: 
 * @Author: Choicc
 * @Date: 2023-03-21 11:30:14
 * @LastEditors: Choicc
 * @LastEditTime: 2023-03-27 15:08:19
 * @email: ccyforworks@gmail.com
-->
<script setup lang="ts">
import { ref, onMounted, onUnmounted, nextTick, defineEmits } from "vue";
const props = defineProps({
  scratchPercent: {
    type: Number,
    default: 80
  },
  imageUrl: {
    type: String
  },
  maskColor: {
    type: String,
    default: "#cccccc",
  },
  fillStyle: {
    type: String,
    default: "#000000",
  },
  font: {
    type: String,
    default: "18px Arial"
  },
  text: {
    type: String,
    default: "刮一刮",
  },
  radius: {
    type: Number,
    default: 0,
  },
  scratchRadius: {
    type: Number,
    default: 10,
  },
});
const emit = defineEmits(['scratchStart', 'scratchEnd', 'scratchAll'])
const ctx = ref<CanvasRenderingContext2D | null | undefined>(null);
const slot = ref<HTMLInputElement | null>(null);
const canvas = ref<HTMLCanvasElement | null>(null);
const width = ref(0);
const height = ref(0);
const isScratching = ref(false);
const init = () => {
  initCanvas();
  nextTick(() => {
    initDraw();
    bindEvents();
  })
};
const initCanvas = () => {
  width.value = slot.value?.offsetWidth || 0;
  height.value = slot.value?.offsetHeight || 0;
};
//初始化绘制
const initDraw = () => {
  ctx.value = canvas.value?.getContext("2d");
  if (ctx.value) {
    ctx.value.globalAlpha = 1;
    ctx.value.fillStyle = props.maskColor;
    //绘制圆角
    if (props.radius) {
      const radius = props.radius;
      //使用clip剪切出圆角
      ctx.value.beginPath();
      //左上角圆角
      ctx.value.moveTo(radius, 0);
      ctx.value.arcTo(0, 0, 0, radius, radius);
      //左下角圆角
      ctx.value.lineTo(0, height.value - radius);
      ctx.value.arcTo(0, height.value, radius, height.value, radius);
      //右下角圆角
      ctx.value.lineTo(width.value - radius, height.value);
      ctx.value.arcTo(
        width.value,
        height.value,
        width.value,
        height.value - radius,
        radius
      );
      //右上角圆角
      ctx.value.lineTo(width.value, radius);
      ctx.value.arcTo(width.value, 0, width.value - radius, 0, radius);
      ctx.value.closePath();
      ctx.value.clip();
    }
    ctx.value.fillRect(0, 0, width.value, height.value);
    //若没有图片绘制文本
    if (!props.imageUrl) {
      // 文本
      ctx.value.fillStyle = props.fillStyle;
      ctx.value.font = props.font;
      // ctx.textAlign = "center";
      // ctx.textBaseline = "middle";

      // 绘制文字
      let text = props.text;
      let textWidth = ctx.value.measureText(text).width;
      let x = width.value / 2 - textWidth / 2;
      let y = height.value / 2 + 6;

      ctx.value.fillText(text, x, y);
    }
    //绘制图片
    if (props.imageUrl) {
      // 创建 Image 对象
      const img = new Image();

      // 设置图片URL
      img.src = props.imageUrl;

      // 监听图片加载完成事件
      img.onload = () => {
        //确定圆角半径

        // 在 Canvas 上绘制图片
        ctx.value?.drawImage(img, 0, 0, width.value, height.value);
      };
    }
  }



}
//绑定事件
const bindEvents = () => {
  if (!canvas.value) {
    return;
  }
  // pc
  canvas.value.addEventListener("mousedown", (e) => {
    isScratching.value = true;
    drawArc(e);
  });
  canvas.value.addEventListener("mousemove", (e) => {
    if (isScratching.value == true) {
      drawArc(e);
    }
  });
  canvas.value.addEventListener("mouseup", () => {
    isScratching.value = false;
    calcArea();
  });
  // wap
  canvas.value.addEventListener("touchstart", (e) => {
    emit("scratchStart");
    isScratching.value = true;
    drawArc(e);
  });
  canvas.value.addEventListener("touchmove", (e) => {
    if (isScratching.value == true) {
      drawArc(e);
    }
  });
  canvas.value.addEventListener("touchend", () => {
    isScratching.value = false;
    emit("scratchEnd");
    calcArea();
  });
}
// 刮开区域
const drawArc = (e: MouseEvent | TouchEvent) => {
  if (!canvas.value || !ctx.value) {
    return;
  }
  const canvasPos = canvas.value.getBoundingClientRect();
  const pageScrollTop =
    document.documentElement.scrollTop || document.body.scrollTop;
  const pageScrollLeft =
    document.documentElement.scrollLeft || document.body.scrollLeft;
  let mouseX = 0;
  let mouseY = 0;

  if (e instanceof MouseEvent) {
    mouseX = e.pageX - canvasPos.left - pageScrollLeft;
    mouseY = e.pageY - canvasPos.top - pageScrollTop;
  } else if (e instanceof TouchEvent) {
    mouseX = e.targetTouches[0].pageX - canvasPos.left - pageScrollLeft;
    mouseY = e.targetTouches[0].pageY - canvasPos.top - pageScrollTop;
  } else {
    return;
  }

  ctx.value.globalCompositeOperation = "destination-out";
  ctx.value.beginPath();
  ctx.value.fillStyle = "white";
  ctx.value.moveTo(mouseX, mouseY);
  ctx.value.arc(mouseX, mouseY, props.scratchRadius, 0, 3 * Math.PI);
  ctx.value.fill();
}

//重置刮刮乐
const reset = () => {
  width.value = 0;
  height.value = 0;
  nextTick(() => {
    init();
  })
}

// 计算区域
const calcArea = () => {
  if (!canvas.value || !ctx.value) {
    return;
  }
  let myImg = ctx.value.getImageData(0, 0, width.value, height.value);
  let num = 0;
  let max = myImg.data.length / 4;
  for (let i = 0; i < myImg.data.length; i += 4) {
    if (myImg.data[i + 3] == 0) {
      num++;
    }
  }
  if (num >= max * (props.scratchPercent / 100)) {
    canvas.value.remove();
    emit("scratchAll");
  }
}
//重绘
const onResize = () => {
  ctx.value = null;
  init();
}

onMounted(() => {
  init();
  window.addEventListener("resize", onResize);
})
onUnmounted(() => {
  window.removeEventListener("resize", onResize);
})
defineExpose({
  reset
})

</script>

<template>
  <div class="box">
    <div ref="slot" class="slot" :style="{ borderRadius: radius + 'px' }">
      <slot></slot>
    </div>
    <canvas v-if="width && height" class="canvas" ref="canvas" :width="width" :height="height"></canvas>
  </div>
</template>

<style scoped>
.box {
  position: relative;
  display: inline-block;
}

.slot {
  display: inline-block;
  overflow: hidden;
}

.canvas {
  position: absolute;
  top: 0;
  left: 0;
}
</style>
