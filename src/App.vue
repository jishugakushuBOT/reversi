<template>
  <div class="container">
    <div class="board">
      <div class="beside" v-for="(cell,columnIndex) in cells" :key="columnIndex">
        <div class="vertical" v-for="(disk,rowIndex) in cell" :key="disk.index" @click="placePieces(columnIndex,rowIndex)">
          <div v-if="disk==0" class="disk none"></div>
          <div v-if="disk==1" class="disk white"></div>
          <div v-if="disk==-1" class="disk black"></div>
        </div>
      </div>
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
      player: -1
    }
  },

  methods:{
    checkTurnable() {
      for (let i = 0; i < this.cells.length; i++) {
        console.log(i)
        for (let j = 0; j < this.cells[i].length; j++) {
          if (this.cells[i][j] === 0) {
            for (let [dx, dy] of directions) {
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
          while(this.isValidPosition && this.cells[x][y]===-this.player){
            x += dx
            y += dy
            hasopponent = true
          }
          if(this.isValidPosition && hasopponent && this.cells[x][y]===this.player){
            let pointerX = columnIndex + dx
            let pointerY = rowIndex + dy
            while(!(pointerX===x && pointerY===y)){
              this.cells[pointerX][pointerY] = this.player
              pointerX += dx
              pointerY += dy
            }
          }
        }

        this.player=-this.player
      }
    }
  },

  mounted() {
    this.checkTurnable();
  }
}
</script>

<style>
.container{
  background-color: #333;
  width: 430px;
  height: 470px;
  display:flex;
  align-items:center;
  justify-content: center;
}

.board{
  background-color: darkgreen;
  width: 410px;
  height:394px;
  /* display:flex;
  justify-content:center;
  align-items:center; */

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

</style>




