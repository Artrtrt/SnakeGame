<template>
  <div class="game-container">
    <div>Max Score: {{ getMaxScore() }}</div>
    <button class="game-button" v-if="!isGameRunning" @click="startGame">Start Game</button>
    <button class="game-button" v-if="isGameRunning" @click="stopGame">Stop Game</button>
    <table>
      <tr v-for="(row, rowIndex) in rows" :key="rowIndex">
        <td v-for="(col, colIndex) in cols" :key="colIndex"
          :class="{ snake: isSnake(rowIndex, colIndex), food: isFood(rowIndex, colIndex) }"></td>
      </tr>
    </table>
    <div> {{ score }}</div>
  </div>
</template>
  
<script>
import Vue from 'vue';
export default Vue.extend({
  name: 'SnakeGame',
  data() {
    return {
      rows: new Array(10).fill(null),
      cols: new Array(10).fill(null),

      snake: [{ x: 0, y: 0 }],
      direction: "right",
      food: {},
      gameLoop: null,
      isGameRunning: false,
      score: 0,
    };
  },
  methods: {
    startGame() {
      this.resetGame();
      this.generateFood();
      this.gameLoop = setInterval(this.moveSnake, 200);
      window.addEventListener("keydown", (event) => {
        if (event.key === "ArrowUp" || event.key === "ArrowDown" ||
          event.key === "ArrowLeft" || event.key === "ArrowRight") {
          this.changeDirection(event);
        }
      });
      this.isGameRunning = true;
    },
    resetGame() {
      clearInterval(this.gameLoop);
      this.snake = [{ x: 0, y: 0 }];
      this.direction = "right";
      this.score = 0;
      this.food = {};
    },
    moveSnake() {
      const head = { ...this.snake[0] };

      switch (this.direction) {
        case "up":
          head.y--;
          break;
        case "down":
          head.y++;
          break;
        case "left":
          head.x--;
          break;
        case "right":
          head.x++;
          break;
      }

      if (this.checkCollision(head)) {
        this.stopGame();
        return;
      }

      if (head.x === this.food.x && head.y === this.food.y) {
        this.generateFood();
        this.score++;
      } else {
        this.snake.pop();
      }

      this.snake.unshift(head);
    },
    changeDirection(event) {
      const directions = {
        ArrowUp: "up",
        ArrowDown: "down",
        ArrowLeft: "left",
        ArrowRight: "right",
      };
      const newDirection = directions[event.key];
      const opposites = {
        up: "down",
        down: "up",
        left: "right",
        right: "left",
      };
      if (newDirection !== this.direction && newDirection !== opposites[this.direction]) {
        this.direction = newDirection;
      }
    },
    isSnake(row, col) {
      return this.snake.some((segment) => segment.x === col && segment.y === row);
    },
    isFood(row, col) {
      return this.food.x === col && this.food.y === row;
    },
    generateFood() {
      do {
        this.food = {
          x: Math.floor(Math.random() * this.cols.length),
          y: Math.floor(Math.random() * this.rows.length),
        };
      } while (this.isSnake(this.food.y, this.food.x));
    },

    checkCollision(head) {
      return (
        head.x < 0 ||
        head.y < 0 ||
        head.x >= this.cols.length ||
        head.y >= this.rows.length ||
        this.isSnake(head.y, head.x)
      );
    },
    stopGame() {
      this.saveMaxScore();
      this.resetGame();
      this.isGameRunning = false;
    },
    getMaxScore() {
      const maxScore = localStorage.getItem("maxScore");
      return maxScore ? parseInt(maxScore) : 0;
    },
    saveMaxScore() {
      const maxScore = this.getMaxScore();
      if (this.score > maxScore) {
        localStorage.setItem("maxScore", this.score.toString());
      }
    },
  },
});
</script>
  
<style scoped>
.game-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.game-button {
  margin: 20px;
}

table {
  border-collapse: collapse;
}

td {
  width: 30px;
  height: 30px;
  border: 1px solid black;
}

.snake {
  background-color: green;
}

.food {
  background-color: red;
}
</style>