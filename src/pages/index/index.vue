<template>
  <view class="wrapper">
    <view class="signature">
      <canvas canvas-id="myCanvas" id="canvas" :disable-scroll="true" @touchstart="doTouchStart"
        @touchmove="doTouchMove" @touchend="doTouchEnd"></canvas>
    </view>
    <view class="menu">
      <view class="btn reset" @click="doCanvasReset">重置</view>
      <view class="btn save" @click="doCanvasSave">保存</view>
      <view class="btn back" @click="doCanvasBack">返回</view>
    </view>
  </view>
</template>

<script setup lang="ts">
import { onLoad, onReady, onShow } from '@dcloudio/uni-app';
import { ref } from 'vue'

let x = ref(0)
let y = ref(0)
let points: Object[] = []
let ctx: any = ""
let instance: any = {}

onLoad((options) => {
  console.log(options)
})

onReady(() => {
  ctx = uni.createCanvasContext('myCanvas')
  ctx.setLineWidth(5)
  ctx.setLineCap('round')

  uni.createSelectorQuery().in(this).select('#canvas').boundingClientRect((e) => {
    console.log(e)

    instance = e
  }).exec()

})

function doTouchStart(e: any) {
  x.value = e.touches[0].x
  y.value = e.touches[0].y

  points.push({
    x: x.value,
    y: y.value
  })

  ctx.beginPath()
}

function doTouchMove(e: any) {
  x.value = e.touches[0].x
  y.value = e.touches[0].y
  points.push({
    x: x.value,
    y: y.value
  })

  draw()
}

function doTouchEnd(e: any) {
  points = []
}

function draw() {
  let pointX: any = points[0]
  let pointY: any = points[1]

  points.shift()

  ctx.moveTo(pointX.x, pointX.y)
  ctx.lineTo(pointY.x, pointY.y)
  ctx.stroke()
  ctx.draw(true)
}

function doCanvasReset() {
  console.log('reset')

  ctx.clearRect(0, 0, instance.width, instance.height)
  ctx.draw(true)
}

function doCanvasSave() {
  console.log('save')

  uni.canvasToTempFilePath({
    x: 0,
    y: 0,
    width: instance.width,
    height: instance.height,
    destWidth: instance.width,
    destHeight: instance.height,
    canvasId: 'myCanvas',
    success: function (res) {
      // 在H5平台下，tempFilePath 为 base64
      console.log(res.tempFilePath)
    }
  })
}

function doCanvasBack() {
  console.log('back')

  uni.navigateBack()
}

</script>

<style lang="scss">
page {

  width: 100%;
  height: 100%;

  .wrapper {
    width: 100%;
    height: 100%;

    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    .signature {
      width: 90%;
      height: 80%;
      overflow: hidden;

      canvas {
        width: 100%;
        height: 100%;
        box-sizing: border-box;
        border: 1rpx solid #f30;
      }

    }

    .menu {
      width: 90%;

      display: flex;
      flex-direction: row;

      justify-content: space-around;
      margin-top: 10rpx;

      .btn {
        width: 65rpx;
        height: 35rpx;
        display: flex;
        justify-content: center;
        align-items: center;
        border-radius: 6rpx;
        border: 1rpx solid #efefef;
      }
    }
  }
}
</style>
