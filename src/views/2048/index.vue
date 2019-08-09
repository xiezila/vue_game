<template>
  <div class="wrapper">
    <div class="heading clearfix">
      <div class="title clearfix">
        <span class="title-left">2048</span>
        <div class="scores-container">
          <div class="score">
            我的分数
            <span class="item">{{score}}</span>
          </div>
          <div class="best">
            最高分数
            <span class="item">1240</span>
          </div>
        </div>
      </div>
      <div class="game-start-btn" @click="handleStartGame">
        点我开始
      </div>
    </div>
    <div class="container">
      <div class="container-detail">
        <div class="cell" v-for="row in list">
          <div class="cell-detail" :class="['n-'+col, {'active': col !== undefined}]" v-for="col in row">{{col}}</div>
        </div>
      </div>
      <!--<div class="grid">-->
        <!--<span class="grid-0-0"></span>-->
        <!--<span class="grid-0-1"></span>-->
        <!--<span class="grid-0-2"></span>-->
        <!--<span class="grid-0-3"></span>-->
        <!--<span class="grid-1-0"></span>-->
        <!--<span class="grid-1-1"></span>-->
        <!--<span class="grid-1-2"></span>-->
        <!--<span class="grid-1-3"></span>-->
        <!--<span class="grid-2-0"></span>-->
        <!--<span class="grid-2-1"></span>-->
        <!--<span class="grid-2-2"></span>-->
        <!--<span class="grid-2-3"></span>-->
        <!--<span class="grid-3-0"></span>-->
        <!--<span class="grid-3-1"></span>-->
        <!--<span class="grid-3-2"></span>-->
        <!--<span class="grid-3-3"></span>-->
      <!--</div>-->
      <!--<div class="container-detail">-->
        <!--<span class="cell"-->
              <!--v-for="( index) in list.length" :key="index"-->
              <!--:style="`-->
                <!--zIndex: ${list[index] ? list[index].num : 0};-->
                <!--display: ${list[index] ? '' : 'none' }-->
                <!--`"-->
          <!--&gt;-->
            <!--<span-->
              <!--class="cell-detail"-->
              <!--:style="`backgroundColor: ${list[index] ? list[index].color : ''}`"-->
            <!--&gt;-->
            <!--{{list[index] ? list[index].num : ''}}-->
          <!--</span>-->
        <!--</span>-->
      <!--</div>-->
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  // transform: translate(${cssTransition(item)});
export default {
  name: "index",
  data(){
    return{
      arrayList: [2, 4, 8, 16, 32, 64, 128, 256, 512, 1024, 2048],
      size: 4,
      score: 0,
      list: [],
    }
  },
  mounted() {
    this.handleStartGame()
    document.addEventListener('keyup', this._keyDown)
  },
  methods: {
    // 随机数:2或者4, 概率9:1
    _random24() {
      return Math.random() > 0.9 ? 4 : 2
    },
    // 随机数: 0, 1, 2, 3
    _random0123() {
      // Math.random产生一个大于等于0，小于1之间的随机数, ~~取整
      return ~~(Math.random() * 4)
    },
    //
    _generateNum(){
      const num = this._random24()
      const x = this._random0123()
      const y = this._random0123()
      const result = {
        x,
        y,
        num,
        id: x * 4 + y
      }
      const isExist = this._isExist({
        x: result.x,
        y: result.y
      });
      if (isExist) return;
      return result;
    },

    // 判断是否格子上已有数存在
    _isExist({x, y}) {
      if (this.list[x][y])
        return true
    },

    // 判断格子是否满了 todo
    _isFull() {
      for (let i = 0; i < 4; i++)
        for (let j = 0; j < 4; j++)
          if (!this.list[i][j]){
            return false
          }
      return true
      // if (this.list.length === 16)
      //   return true
    },

    // 格子上产生一个数
    _add() {
      if (this._isFull()) {
        console.log("满了不能再加了，但是游戏还没结束啊");
        return false;
      }
      const result = this._generateNum();
      if (result) {
        // this.list.push(result);
        this.list[result.x][result.y] = result.num
      } else {
        this._add();
      }
    },

    // init
    handleStartGame() {
      this.score = 0
      this.list = Array.from(Array(this.size)).map(() => Array(this.size).fill(undefined))
      this._add()
      this._add()
    },

    // isGameOver() {
    //   let rowsList = [...this.list]
    //   let colsList = [...this.list]
    //   this._swapXY(colsList)
    //   if (this._isFull()){
    //     console.log('1111111111')
    //     if (this._isCommon(rowsList) && this._isCommon(colsList)) {
    //       alert('Game Over!')
    //     }
    //   }
    // },

    // move
    handleMove(direct) {
      // vue深拷贝-----直接赋值无效
      // let newList = JSON.parse(JSON.stringify(this.list))
      let newList = [...this.list]

      if (direct === 'left' ) {
        for (let i = 0 ; i < 4; i++){
          // console.log(i, this.list[i])
          newList = this._moveLeft(newList, i, newList[i])
        }
      }
      else if (direct === 'right') {
        for (let i = 0 ; i < 4; i++) {
          newList = this._moveRight(newList, i, newList[i])
        }
      }
      else {
        // 交换x， y
        this._swapXY(newList)
        if (direct === 'up') {
          for (let i = 0 ; i < 4; i++){
            // console.log(i, this.list[i])
            newList = this._moveLeft(newList, i, newList[i])
          }
        } else {
          for (let i = 0 ; i < 4; i++) {
            newList = this._moveRight(newList, i, newList[i])
          }
        }
        this._swapXY(newList)
      }

      this.list = newList
    },

    // 判断行或者列相邻两位是否有相同的元素
    _isCommon(arr) {
      for (let i = 0; i < 4; i++) {
        for (let j = 1; j < 4; j++) {
          if (arr[i][j-1] === arr[i][j])
            return false
        }
      }
      return true
    },


    // swap x and y
    _swapXY(arr) {
      for(let i = 0, temp = 0; i < 4; i++)
        for(let j = 0; j < i; j++)
        {
          temp = arr[i][j];
          arr[i][j] = arr[j][i];
          arr[j][i] = temp;
        }
    },

    _moveRight(newList, rowIndex, rowList) {
      //当前行非空格子
      let _rowList = []
      for (let i = 0; i < rowList.length; i++) {
        if ( rowList[i] ) {
          _rowList.push(i)
        }
      }
      // 右移
      for (let i = 3, j = _rowList.length-1; i >= 0; i--, j--) {
        if (i !== _rowList[j]){
          newList[rowIndex][i] = newList[rowIndex][_rowList[j]]
          newList[rowIndex][_rowList[j]] = undefined
        }
      }
      // 合并 todo test
      for (let i = 3; i > 4 - _rowList.length; i--){
        if (newList[rowIndex][i] === newList[rowIndex][i-1])
        {
          newList[rowIndex][i] = newList[rowIndex][i] * 2
          newList[rowIndex][i-1] = undefined
        }
      }
      // 移位
      for (let i = 3; i > 0; i--) {
        if (newList[rowIndex][i] === undefined && newList[rowIndex][i-1]) {
            newList[rowIndex][i] = newList[rowIndex][i-1]
            newList[rowIndex][i-1] = undefined
        }
      }
      return newList
    },

    _moveLeft(newList, rowIndex, rowList) {
      //当前行非空格子
      let _rowList = []
      for (let i = 0; i < rowList.length; i++) {
        if ( rowList[i] ) {
          _rowList.push(i)
        }
      }
      // 左移
      for (let i = 0; i < _rowList.length; i++){
        if (i !== _rowList[i]){
          newList[rowIndex][i] = newList[rowIndex][_rowList[i]]
          newList[rowIndex][_rowList[i]] = undefined
        }
      }
      // 合并 todo test
      for (let i = 1; i < _rowList.length; i++){
        if (newList[rowIndex][i-1] === newList[rowIndex][i])
        {
          newList[rowIndex][i-1] = newList[rowIndex][i] * 2
          newList[rowIndex][i] = undefined
        }
      }
      // 移位
      for (let i = 1; i < 4; i++) {
        if (newList[rowIndex][i-1] === undefined && newList[rowIndex][i]) {
          newList[rowIndex][i-1] = newList[rowIndex][i]
          newList[rowIndex][i] = undefined
        }
      }
      return newList
    },

    //键盘监听事件
    _keyDown(event) {
      switch (event.keyCode) {
        case 37:
          this.handleMove('left')
          break
        case 38:
          this.handleMove('up')
          break
        case 39:
          this.handleMove('right')
          break
        case 40:
          this.handleMove('down')
          break
      }
      this._add()
      // this.isGameOver()
    }
  }
}
</script>

<style rel="stylesheet/stylus" lang="stylus" scoped>
.clearfix:after
  content: ''
  display: block
  height: 0
  clear: both
  visibility: hidden
.scores-container
  .item
    display block
    font-size 25px
    font-weight bold
    color #fff
.wrapper
  width: 500px
  margin: 0 auto
  .heading
    margin-bottom 40px
    .title
      .title-left
        float left
        font-size 80px
        font-weight bold
        color: #776e65
      .scores-container
        float right
        padding-top 10px
        color #eee4da
        .score
          display inline-block
          padding 5px 0
          width 105px
          font-size 13px
          border-radius 3px
          background #bbab9f
        .best
          display inline-block
          width 105px
          padding 5px 0
          font-size 13px
          border-radius 3px
          background #bbab9f
    .game-start-btn
      float right
      padding 4px 0
      width 112px
      font-weight: bold;
      font-size: 18px;
      border-radius 3px
      cursor pointer
      color #fff
      background #8f7a66
  .container
    width 500px
    height 500px
    .container-detail
      box-sizing: border-box;
      width: 480px;
      height: 480px;
      padding: 10px;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      background #bbada0
      border-radius 6px
      .cell
        width: 100%;
        height: 23%;
        /*margin: 10px*/
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        .cell-detail {
          width: 23%;
          height: 100%;
          display: flex;
          align-items: center;
          justify-content: center;
          font-size 48px
          font-weight: bold;
          border-radius: 8px;
          //background: #cec1b3;
          background: rgba(238, 228, 218, 0.35)
        }
        .active {
          /*animation-fill-mode: backwards;*/
          animation: appear 200ms;
          -webkit-animation: appear 200ms;
        }
        .n-2 {
          background: #eee4da
        }
        .n-4 {
          background #ede0c8
        }
        .n-8 {
          background #f2b179
        }
        .n-16 {
          background #f59563
        }
        .n-32 {
          background #f67c5f
        }
        .n-64 {
          background #f65e3b
        }
        .n-128 {
          background #edcf72
        }
        .n-256 {
          background #edcc61
        }
        .n-512 {
          background #0444BF
        }
        .n-1024 {
          background #A79674
        }
        .n-2048 {
          background #282726
        }
@keyframes appear {
  0% {
    opacity: 0;
    transform: scale(0);
  }
  80% {
    opacity: 0;
    transform: scale(0.8);
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}
@-webkit-keyframes appear{
  0% {
    opacity: 0;
    transform: scale(0);
  }
  80% {
    opacity: 0;
    transform: scale(0.8);
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}
</style>
<style rel="stylesheet/stylus" lang="stylus">
html, body
  background #faf8ef
  color: #776e65
</style>
