<template>
  <view class="root">
    <u-button
      type="primary"
      plain
      @click="startGame">
      开始游戏
    </u-button>
  </view>
</template>

<script>
  export default {
    data () {
      return {}
    },
    methods: {
      startGame: function () {
        uni.navigateTo({ url: '/pages/game/index' })
      }
    }
  }
</script>

<style scoped lang="scss">
  @import "./src/styles/utils";

  .root {
    display: flex;
    align-items: center;
    justify-content: center;
    padding: rpx(30);
    min-height: 100vh;
  }
</style>
