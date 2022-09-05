<template>
  <view class="game" style="{ height: innerHeight }">
    <div class="game-chunk">
      <game-chessboard
        ref="game-chessboard-0"
        class="game-chessboard"
        v-model="chessboardData[0]"
        :name="names[0]"
        :disabled="step % 2 === 1"
        @placed="(...args) => onPlaced(0, ...args)"
        @done="onDone"/>
    </div>
    <div class="game-chunk">
      <game-chessboard
        ref="game-chessboard-1"
        class="game-chessboard"
        v-model="chessboardData[1]"
        :name="names[1]"
        :disabled="step % 2 === 0"
        @placed="(...args) => onPlaced(1, ...args)"
        @done="onDone"/>
    </div>
  </view>
</template>

<script>
  import GameChessboard from './game-chessboard'

  export default {
    data () {
      return {
        step: 0,
        chessboardData: [
          [
            [ 0, 0, 0 ],
            [ 0, 0, 0 ],
            [ 0, 0, 0 ]
          ],
          [
            [ 0, 0, 0 ],
            [ 0, 0, 0 ],
            [ 0, 0, 0 ]
          ]
        ],
        names: [ '玩家一', '玩家二' ]
      }
    },
    methods: {
      /**
       *  用户将数字放入了棋盘中
       *  需要判断，对方的棋盘，响应的列中是否有相同的数组
       *  如果有，全部消掉
       **/
      onPlaced: function (chessboardI, colI, itemI, num) {
        var oppositeChessboardData = chessboardI === 0 ?
          this.chessboardData[ 1 ] :
          this.chessboardData[ 0 ]

        // 对比时，需要将第一列和最后一列交换顺序（因为棋盘是相对的）
        colI = colI === 0 ? 2 : colI === 2 ? 0 : colI

        oppositeChessboardData[ colI ] = _.map(oppositeChessboardData[ colI ], _num => {
          return _num === num ? 0 : _num
        })

        // 强制更新视图
        this.chessboardData = _.cloneDeep(this.chessboardData)
        this.step++
      },

      // 游戏结束（棋盘被摆满了）
      onDone: function () {
        var score = [
          this.$refs[ 'game-chessboard-0' ].getSumByAll(),
          this.$refs[ 'game-chessboard-1' ].getSumByAll()
        ]

        // 是否平局
        var dogfall = score[ 0 ] === score[ 1 ]

        // 分数高的下标
        var maxScoreI = score[ 0 ] > score[ 1 ] ? 0 : 1

        uni.showModal({
          title: '结束',
          content: dogfall ? `平局` : `${ this.names[ 0 ] }：${ score[ 0 ] } 分\n${ this.names[ 1 ] }：${ score[ 1 ] } 分\n${ this.names[ maxScoreI ] } 获胜`,
          showCancel: false,
          success: () => {
            uni.navigateBack()
          }
        })

        console.log(score)
      }
    },
    components: {
      'gameChessboard': GameChessboard
    }
  }
</script>

<style scoped lang="scss">
  @import "./src/styles/utils";

  .game {
    height: 100vh;
    padding: rpx(100);
    &-chunk {
      height: 50%;
      padding-top: rpx(50);
      &:nth-child(1) {
        transform: rotate(180deg);
      }
      &:nth-child(2) {
        transform: rotate(360deg);
      }
    }
  }
</style>
