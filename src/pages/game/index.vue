<template>
  <view class="game">
    <game-chessboard class="game-chessboard"></game-chessboard>
    <game-chessboard class="game-chessboard"></game-chessboard>
  </view>
</template>

<script>
  import GameChessboard from './game-chessboard'

  export default {
    data () {
      return {}
    },
    components: {
      [ GameChessboard.name ]: GameChessboard
    }
  }
</script>

<style scoped lang="scss">
  @import "./src/styles/utils";

  .game {
    height: 100vh;
    &-chessboard {
      height: 50%;
      &:nth-child(1) {
        background-color: red
      }
      &:nth-child(2) {
        background-color: blue
      }
    }
  }
</style>
