<template>
  <div class="fullDisplay">
    <div class="container">
      <div class="board">
        <div class="beside" v-for="(cell,columnIndex) in cells" :key="columnIndex">
          <div class="vertical" v-for="(disk,rowIndex) in cell" :key="disk.index" @click="placePieces(columnIndex,rowIndex)">
            <div v-if="disk==0 && turnAble[columnIndex][rowIndex]===1" class="disk yellow"></div>
            <div v-if="disk==0 && turnAble[columnIndex][rowIndex]!==1" class="disk none"></div>
            <div v-if="disk==1" class="disk white"></div>
            <div v-if="disk==-1" class="disk black"></div>
          </div>
        </div>
      </div>
    </div>
    <div class="footer">
      <p v-if="this.player===-1">黒の番です</p>
      <p v-if="this.player===1">白の番です</p>
      <button @click="restart()">リスタート</button>
    </div>
  </div>
</template>

<script>


export default {
  name: 'App',
  data() {
    return{
      cells: [
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,1,-1,0,0,0],
        [0,0,0,-1,1,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
      ],
      turnAble: [
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
      ],
      directions: [
        [-1, 0], [1, 0], [0, -1], [0, 1],
        [-1, -1], [-1, 1], [1, -1], [1, 1]
      ],
      // playerは先行（黒）を-1、後行（白）を1とします
      player: -1,
      passFlag:false
    }
  },


  mounted() {
    this.checkTurnable();
  },

  methods:{
    //プレイヤーが石を置ける場所を判定
    checkTurnable() {
      this.turnAble=[
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
      ]
      
      for (let i = 0; i < this.cells.length; i++) {
        for (let j = 0; j < this.cells[i].length; j++) {
          if (this.cells[i][j] === 0) {
            for (let [dx, dy] of this.directions) {
              let x = i + dx;
              let y = j + dy;
              let hasOpponent = false;
              while (this.isValidPosition(x, y) && this.cells[x][y] === -this.player) {
                x += dx;
                y += dy;
                hasOpponent = true;
              }
              if (hasOpponent && this.isValidPosition(x, y) && this.cells[x][y] === this.player) {
                this.turnAble[i][j] = 1;
                break;
              }
            }
          }
        }
      }
    },

    //ポインターが枠内か判定する
    isValidPosition(x, y) {
      return x >= 0 && x < 8 && y >= 0 && y < 8;
    },

    placePieces(columnIndex,rowIndex){
      // おいていい位置（turnAbleの中身が1）であれば
      if(this.turnAble[columnIndex][rowIndex]===1){
        this.cells[columnIndex][rowIndex]=this.player

        //挟んだ石をひっくり返す
        for(let[dx,dy] of this.directions){
          let x = columnIndex+dx
          let y = rowIndex+dy
          let hasopponent = false
          while(this.isValidPosition(x,y) && this.cells[x][y]===-this.player){
            x += dx
            y += dy
            hasopponent = true
          }
          if(this.isValidPosition(x,y) && hasopponent && this.cells[x][y]===this.player){
            let pointerX = columnIndex + dx
            let pointerY = rowIndex + dy
            while(!(pointerX===x && pointerY===y)){
              this.cells[pointerX][pointerY] = this.player
              pointerX += dx
              pointerY += dy
            }
          }
        }
        //プレイヤーをチェンジ
        this.player=-this.player
        // 次のプレイヤーが石を置ける場所をチェック
        this.checkTurnable()

        //もし次のプレイヤーが石を置ける場所がなかったら
        if(this.turnAble.every(row => row.every(value => value===0))){
          console.log("置ける場所がない")
          //パスする（プレイヤーを入れ替える）
          this.player=-this.player
          this.checkTurnable()
          //入れ替わったプレイヤーも置くところがなかったら（ゲーム終了）
          if(this.turnAble.every(row => row.every(value => value===0))){
            console.log("ゲーム終了")
            let Sum = this.cells.flat().reduce((sum,num) => sum+num,0)
            if (Sum > 0) {
              console.log("白の勝ち");
            } else if (Sum < 0) {
              console.log("黒の勝ち");
            } else {
              console.log("引き分け");
            }
          }
        }
        
        
      }
    },

    restart(){
      this.cells=[
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,1,-1,0,0,0],
        [0,0,0,-1,1,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
        [0,0,0,0,0,0,0,0],
      ]
      this.player=-1
      this.checkTurnable()
      
    }
  }  
}
</script>

<style>
.fullDisplay{
  width: 430px;
}
.container{
  background-color: #333;
  width: 100%;
  height: 470px;
  display:flex;
  align-items:center;
  justify-content: center;
}

.board{
  background-color: darkgreen;
  width: 410px;
  height:394px;

}

.beside{
  display:flex;
  justify-content:center;
  align-items:center;
}
.vertical{
  border: solid;
  border-color:#333;
  border-width:thin;
  width:50px;
  height:48px;
  display:flex;
  justify-content:center;
  align-items:center;

}

.disk{
  width:80%;
  height:80%;
  border-radius: 50%;
}

.white{
  background-color: white;
}

.black{
  background-color: black;
}

.yellow{
  background-color: yellow;
  width:20%;
  height:20%;
}

.footer{
  width: 430px;
  text-align: center;
}

</style>




