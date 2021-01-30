<template>
  <div class="hello">
    <div class="container">
      <div class="row" v-for="rno in 3" :key="rno">
        <div
          :data-row="rno"
          :data-col="cno"
          v-for="cno in 3"
          :key="cno"
          @click="
            pick($event);
          "
          class="box col"
        >
          {{ `${board[rno - 1][cno - 1]}` }}
        </div>
      </div>
      <br />
      <div>
        <div class="row">
          <div class="col">
            <button type="button" class="btn btn-success reset-btn"
              @click="reset"
              variant="success"
              >RESET</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component';

@Options({
  props: {
    msg: String
  }
})
export default class HelloWorld extends Vue {
  msg!: string

  private isWinner = false;
  private round = 0;
  private player1 = "O";
  private player2 = "X";
  private board = [
    ["", "", ""],
    ["", "", ""],
    ["", "", ""],
  ];
  private combinations = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],

    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],

    [0, 4, 8],
    [2, 4, 6],
  ];

  public calcHeight() {
    const cells = document.getElementsByClassName("box");
    const cell = cells[0] as HTMLElement;
    const positionInfo = cell.getBoundingClientRect();
    const height = positionInfo.width;

    for (let i = 0; i < cells.length; i++) {
      const wrapper = cells[i] as HTMLElement;
      wrapper.style.height = `${height}px`;
    }
    return height;
  }

private pick(e: MouseEvent)
{
this.updateBoard(e);
setTimeout(this.checkResult, 100);
}

  private updateBoard(e: MouseEvent) {
    if (!this.isWinner) {
      const turn = this.round % 2 === 0 ? this.player1 : this.player2;
      const target = e.target as HTMLElement;
      const row: number = +target.dataset.row!;
      const col: number = +target.dataset.col!;
      if (this.board[row - 1][col - 1] === "") {
        target.innerText = turn;
        this.board[row - 1][col - 1] = turn;
        this.round += 1;
      }
    }
  }

  private checkResult() {
    const result = this.board.reduce((total, row) => total.concat(row));
    console.log(result);
    const moves = {
      x: new Array<number>(),
      o: new Array<number>(),
    };

    result.forEach((field, index) => {
      if (field === this.player1) {
        moves.o.push(index);
      } else if (field === this.player2) {
        moves.x.push(index);
      }
    });

    this.combinations.forEach((combination) => {
      if (combination.every((index) => moves.o.indexOf(index) > -1)) {   
        this.isWinner = true;
        this.showWinnerRow(combination);
        alert("Wygrał gracz z kółkiem.");
      }
      if (combination.every((index) => moves.x.indexOf(index) > -1)) {
        
        this.showWinnerRow(combination);
        this.isWinner = true;
        alert("Wygrał gracz z krzyżykiem.");
      }
    });
  }

  private showWinnerRow(combination: Array<number>) {
    const cells = document.getElementsByClassName("box");

    combination.forEach((index) => {
      const winRow = Math.floor(index / 3);
      const winCell = index % 3;

      for (let i = 0; i < cells.length; i++) {
        const element = cells[i] as HTMLElement;
        const row = +element.dataset.row! - 1;
        const col = +element.dataset.col! - 1;

        if (winRow === row && winCell === col) {
          element.classList.add("winner");
        }
      }
    });
  }

  private reset() {
    console.log("reset");

    this.isWinner = false;
    this.round = 0;
    this.board = [
      ["", "", ""],
      ["", "", ""],
      ["", "", ""],
    ];

    const cells = document.getElementsByClassName("box");
    for (let i = 0; i < cells.length; i++) {
      const wrapper = cells[i] as HTMLElement;
      wrapper.innerText = "";
      if (wrapper.classList.contains("winner")) {
        wrapper.classList.remove("winner");
      }
    }
  }

  private myEventHandler() {
    this.calcHeight();
  }

  public created() {
    window.addEventListener("resize", this.myEventHandler);
  }

  public mounted() {
    this.calcHeight();
  }

  public destroyed() {
    window.removeEventListener("resize", this.myEventHandler);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.reset-btn {
  z-index: 7;
}

.box {
  border: 1px solid #ddd;
  cursor: pointer;
  background-color: #fff;
  font-size: 5vw;
  color: #222;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 6;
}
.winner {
  color: crimson;
}

.box:hover {
  background-color: #bad2f1;
}

h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>







