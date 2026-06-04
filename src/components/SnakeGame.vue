<template>
  <div class="game-container">
    <h2>Score: {{ score }}</h2>
    <canvas 
      ref="gameCanvas" 
      width="400" 
      height="400" 
      class="game-board"
    ></canvas>
    <div v-if="gameOver" class="overlay">
      <p>GAME OVER</p>
      <button @click="resetGame">Restart</button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

const gameCanvas = ref(null);
const score = ref(0);
const gameOver = ref(false);

// Game Settings
const gridSize = 20;
const tileCount = 20;
let ctx = null;
let snake = [{ x: 10, y: 10 }];
let food = { x: 5, y: 5 };
let dx = 0;
let dy = 0;
let gameInterval = null;

const drawGame = () => {
  // Move Snake
  const head = { x: snake[0].x + dx, y: snake[0].y + dy };
  
  // Wall Collision
  if (head.x < 0 || head.x >= tileCount || head.y < 0 || head.y >= tileCount) {
    return endGame();
  }

  // Self Collision
  if (snake.some(segment => segment.x === head.x && segment.y === head.y)) {
    return endGame();
  }

  snake.unshift(head);

  // Eat Food
  if (head.x === food.x && head.y === food.y) {
    score.value += 10;
    spawnFood();
  } else {
    if (dx !== 0 || dy !== 0) snake.pop();
  }

  // Clear Canvas
  ctx.fillStyle = '#2c3e50';
  ctx.fillRect(0, 0, 400, 400);

  // Draw Food
  ctx.fillStyle = '#e74c3c';
  ctx.fillRect(food.x * gridSize, food.y * gridSize, gridSize - 2, gridSize - 2);

  // Draw Snake
  ctx.fillStyle = '#2ecc71';
  snake.forEach(segment => {
    ctx.fillRect(segment.x * gridSize, segment.y * gridSize, gridSize - 2, gridSize - 2);
  });
};

const spawnFood = () => {
  food = {
    x: Math.floor(Math.random() * tileCount),
    y: Math.floor(Math.random() * tileCount)
  };
};

const handleKeydown = (e) => {
  switch (e.key) {
    case 'ArrowUp': if (dy === 0) { dx = 0; dy = -1; } break;
    case 'ArrowDown': if (dy === 0) { dx = 0; dy = 1; } break;
    case 'ArrowLeft': if (dx === 0) { dx = -1; dy = 0; } break;
    case 'ArrowRight': if (dx === 0) { dx = 1; dy = 0; } break;
  }
};

const endGame = () => {
  gameOver.value = true;
  clearInterval(gameInterval);
};

const resetGame = () => {
  snake = [{ x: 10, y: 10 }];
  dx = 0;
  dy = 0;
  score.value = 0;
  gameOver.value = false;
  spawnFood();
  gameInterval = setInterval(drawGame, 100);
};

onMounted(() => {
  ctx = gameCanvas.value.getContext('2d');
  window.addEventListener('keydown', handleKeydown);
  gameInterval = setInterval(drawGame, 100);
});

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown);
  clearInterval(gameInterval);
});
</script>

<style scoped>
.game-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
}
.game-board {
  border: 4px solid #34495e;
  background-color: #2c3e50;
  box-shadow: 0 0 20px rgba(0,0,0,0.2);
}
.overlay {
  position: absolute;
  top: 50%;
  text-align: center;
  background: rgba(255, 255, 255, 0.9);
  padding: 20px;
  border-radius: 8px;
}
</style>