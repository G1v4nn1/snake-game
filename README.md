<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="game-container">
        <canvas id="gameCanvas" width="400" height="400"></canvas>
    </div>
    <script src="script.js"></script>
</body>
</html>


body {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
  background-color: #000;
}

.game-container {
  border: 2px solid #fff;
}

canvas {
  background-color: #000;
  display: block;
}



const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

const grid = 20;
const larguraMapa = 800; // Largura do mapa
const alturaMapa = 600; // Altura do mapa
canvas.width = larguraMapa;
canvas.height = alturaMapa;

let count = 0;
let snake = [{x: 160, y: 160}, {x: 140, y: 160}, {x: 120, y: 160}, {x: 100, y: 160}];
let apple = {x: 320, y: 320};
let dx = grid;
let dy = 0;
let score = 0;

function getRandomInt(min, max) {
    return Math.floor(Math.random() * (max - min)) + min;
}

function createApple() {
    apple.x = getRandomInt(0, larguraMapa / grid) * grid;
    apple.y = getRandomInt(0, alturaMapa / grid) * grid;
}

function drawApple() {
    ctx.fillStyle = 'red';
    ctx.fillRect(apple.x, apple.y, grid, grid);
}

function drawSnake() {
    snake.forEach(function(part) {
        ctx.fillStyle = '#00FF00';
        ctx.fillRect(part.x, part.y, grid, grid);
    });
}

function moveSnake() {
    const head = {x: snake[0].x + dx, y: snake[0].y + dy};
    snake.unshift(head);

    if (snake[0].x === apple.x && snake[0].y === apple.y) {
        score += 10;
        createApple();
    } else {
        snake.pop();
    }
}

function checkCollision() {
    // Wrap around the edges
    if (snake[0].x < 0) snake[0].x = larguraMapa - grid;
    if (snake[0].x >= larguraMapa) snake[0].x = 0;
    if (snake[0].y < 0) snake[0].y = alturaMapa - grid;
    if (snake[0].y >= alturaMapa) snake[0].y = 0;

    for (let i = 4; i < snake.length; i++) {
        if (snake[i].x === snake[0].x && snake[i].y === snake[0].y) {
            resetGame();
        }
    }
}

function resetGame() {
    snake = [{x: 160, y: 160}, {x: 140, y: 160}, {x: 120, y: 160}, {x: 100, y: 160}];
    dx = grid;
    dy = 0;
    createApple();
    score = 0;
}

function update() {
    if (++count < 4) {
        requestAnimationFrame(update);
        return;
    }

    count = 0;
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    moveSnake();
    checkCollision();
    drawApple();
    drawSnake();

    requestAnimationFrame(update);
}

document.addEventListener("keydown", function(e) {
    if (e.key === "ArrowLeft" && dx === 0) {
        dx = -grid;
        dy = 0;
    } else if (e.key === "ArrowUp" && dy === 0) {
        dx = 0;
        dy = -grid;
    } else if (e.key === "ArrowRight" && dx === 0) {
        dx = grid;
        dy = 0;
    } else if (e.key === "ArrowDown" && dy === 0) {
        dx = 0;
        dy = grid;
    }
});

createApple();
requestAnimationFrame(update);
