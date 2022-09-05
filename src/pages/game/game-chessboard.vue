<template>
  <view class="game-chessboard">
    <div class="game-chessboard-body">
      <div
        class="game-chessboard-col"
        :class="{ disabled: colArr[ 0 ] && colArr[ 1 ] && colArr[ 2 ] }"
        v-for="(colArr, colI) in value"
        :key="colI">
        <div class="game-chessboard-item">
          {{ colSumArr[ colI ] }}
        </div>
        <div
          class="game-chessboard-item"
          v-for="(item, itemI) in colArr"
          :key="itemI"
          @click="putNum(colI, itemI)">
          {{ item === 0 ? '' : item }}
        </div>
      </div>
    </div>
    <div class="game-chessboard-footer">
      <div class="game-chessboard-rnum">
        {{ ranNum }}
      </div>
      <u-button
        type="primary"
        :disabled="Boolean(ranNum) || disabled"
        @click="randomNum">
        {{ name }} 随机数字
      </u-button>
    </div>
  </view>
</template>

<script>
  import _ from 'lodash'

  export default {
    name: 'gameChessboard',
    props: {
      // 玩家名称
      name: {
        type: String,
        default: '玩家'
      },

      /**
       *  [
       *    [ 0, 0, 0 ],
       *    [ 0, 0, 0 ],
       *    [ 0, 0, 0 ]
       *  ]
       **/
      value: {
        type: Array,
        required: true
      },

      // 是否禁用
      disabled: {
        type: Boolean,
        default: false
      }
    },
    emits: {
      // 已完成棋盘
      'done': null,
      // 将数字放置在了棋盘上，参数为第几列，第几个，数字是几
      'placed': null
    },
    data () {
      return {
        // 随机到的随机数，放入棋盘中后清空
        ranNum: null
      }
    },
    computed: {
      // 每一列的总和
      colSumArr () {
        return _.map(this.value, (colArr, i) => {
          return this.getSumByCol(i)
        })
      }
    },
    methods: {
      // 随机一个数字
      randomNum () {
        this.ranNum = _.random(1, 6)
      },

      // 放置数字到棋盘中
      putNum (colI, i) {
        if (!this.ranNum) return
        if (this.value[ colI ][ i ]) return

        var newValue = _.cloneDeep(this.value)
        newValue[ colI ][ i ] = this.ranNum

        this.$emit('input', newValue)
        this.$emit('placed', colI, i, this.ranNum)

        this.ranNum = null

        // 判断是否所有位置都有数字，若是，则结束游戏
        // PS：这里需要定时器，否则执行顺序不对 this.value 值是旧的
        setTimeout(() => {
          if (!_.includes(_.flatMapDeep(this.value), 0)) {
            this.$emit('done')
          }
        })
      },

      /**
       *  获取某列的总值
       *  若同列有相同的数字，则相乘计算，不同的数字，相加计算
       **/
      getSumByCol (colI) {
        var colArr = this.value[ colI ]

        /**
         *  计算思路：
         *    分别筛选出需要相乘和需要相加的数字，然后分别进行相乘或者相加操作
         *    最后两者相加求和
         *    PS：multiplyArr 是个二维数组，并且索引是错乱的，根据具体的值作为下标，比如：
         *      [
         *        0: undefined
         *        6: [6, 6]
         *      ]
         **/
        var multiplyArr = []
        var addArr = []

        // 分组，方便计算
        _.each(colArr, function (num, i) {
          // 判断相乘思路：
          // 删除当前位置的数，若数组中还有相同的数，则代表需要相乘
          var _colArr = _.cloneDeep(colArr)
          _.pullAt(_colArr, i)

          // 需要相乘
          if (_.includes(_colArr, num)) {
            multiplyArr[ num ] = multiplyArr[ num ] || []
            multiplyArr[ num ].push(num)
          }
          // 需要相加
          else {
            addArr.push(num)
          }
        })

        // 相乘逻辑的数的总和
        var multiplyResult = (function () {
          var result = 0
          // 加上需要相乘逻辑的数
          _.compact(multiplyArr).forEach(function (arr) {
            var _result = arr[ 0 ]
            for (let i = 1; i < arr.length; i++) {
              _result *= arr[ i ]
            }
            result += _result
          })
          return result
        })()
        // 相加逻辑的数的总和
        var addResult = _.sum(addArr)

        return multiplyResult + addResult
      },

      // 获取全部列的总值
      getSumByAll () {
        return _.reduce(this.value, (result, colArr, i) => {
          return result + this.getSumByCol(i)
        }, 0)
      }
    }
  }
</script>

<style scoped lang="scss">
  @import "./src/styles/utils";

  $border-color: #ccc;

  .game-chessboard {
    display: flex;
    flex-direction: column;
    height: 100%;

    &-body {
      display: flex;
      flex-grow: 1;
      border-top: 1px solid $border-color;
      border-left: 1px solid $border-color;
    }
    &-col {
      flex-shrink: 0;
      width: 100 / 3 * 1%;
      height: 100%;
      &.disabled {
        pointer-events: none;
        background-color: #f7f7f7;
      }
    }
    &-item {
      display: flex;
      align-items: center;
      justify-content: center;
      height: 100 / 4 * 1%;
      border-right: 1px solid $border-color;
      border-bottom: 1px solid $border-color;
    }

    &-footer {
      display: flex;
      align-content: center;
      justify-content: center;
      flex-shrink: 0;
      margin-top: rpx(30);
      height: rpx(80);
    }
    &-rnum {
      display: flex;
      align-items: center;
      justify-content: center;
      border: 1px solid $border-color;
      height: 100%;
      width: rpx(80);
      flex-shrink: 0;
      margin-right: rpx(30);
    }
  }
</style>