<!DOCTYPE html>
<html lang="sv">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>Keeshond Jump</title>

<style>
  body {
    margin: 0;
    overflow: hidden;
    background: #70c5ce;
    touch-action: manipulation;
  }

  canvas {
    display: block;
  }

  #startScreen, #gameOverScreen {
    position: absolute;
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    background: rgba(0,0,0,0.5);
    color: white;
    font-family: sans-serif;
  }

  button {
    padding: 15px 25px;
    font-size: 18px;
    border: none;
    border-radius: 10px;
    background: #f4a261;
    color: white;
  }
</style>
</head>

<body>

<canvas id="gameCanvas"></canvas>

<div id="startScreen">
  <h1>Keeshond Jump 🐶</h1>
  <button onclick="startGame()">Starta</button>
</div>

<div id="gameOverScreen" style="display:none;">
  <h1>Game Over</h1>
  <p id="finalScore"></p>
  <button onclick="restartGame()">Spela igen</button>
</div>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

// 🐕 Ladda bild
let dogImg = new Image();
dogImg.src = "https://upload.wikimedia.org/wikipedia/commons/6/6f/Keeshond.jpg";

let dog, pipes, score, gameRunning;

// Starta spelet
function startGame() {
  document.getElementById("startScreen").style.display = "none";
  init();
  gameRunning = true;
  loop();
}

function restartGame() {
  document.getElementById("gameOverScreen").style.display = "none";
  init();
  gameRunning = true;
}

// Init
function init() {
  dog = {
    x: 80,
    y: canvas.height / 2,
    size: 50,
    velocity: 0
  };

  pipes = [];
  score = 0;
}

// Touch + klick
function jump() {
  if (!gameRunning) return;
  dog.velocity = -10;
}

document.addEventListener("touchstart", jump);
document.addEventListener("mousedown", jump);

// Skapa hinder
function createPipe() {
  let gap = 180;
  let top = Math.random() * (canvas.height - gap - 200) + 50;

  pipes.push({
    x: canvas.width,
    width: 70,
    top: top,
    bottom: canvas.height - top - gap
  });
}

// Uppdatera
function update() {
  dog.velocity += 0.6;
  dog.y += dog.velocity;

  if (dog.y < 0 || dog.y > canvas.height) {
    gameOver();
  }

  if (Math.random() < 0.02) createPipe();

  pipes.forEach((pipe, i) => {
    pipe.x -= 3;

    // kollision
    if (
      dog.x < pipe.x + pipe.width &&
      dog.x + dog.size > pipe.x &&
      (dog.y < pipe.top || dog.y + dog.size > pipe.top + 180)
    ) {
      gameOver();
    }

    // poäng
    if (pipe.x + pipe.width === dog.x) {
      score++;
    }

    if (pipe.x < -pipe.width) pipes.splice(i, 1);
  });
}

// Rita
function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // hund
  ctx.drawImage(dogImg, dog.x, dog.y, dog.size, dog.size);

  // rör
  ctx.fillStyle = "green";
  pipes.forEach(pipe => {
    ctx.fillRect(pipe.x, 0, pipe.width, pipe.top);
    ctx.fillRect(pipe.x, pipe.top + 180, pipe.width, pipe.bottom);
  });

  // poäng
  ctx.fillStyle = "white";
  ctx.font = "30px sans-serif";
  ctx.fillText(score, 20, 50);
}

// Game over
function gameOver() {
  gameRunning = false;
  document.getElementById("finalScore").innerText = "Poäng: " + score;
  document.getElementById("gameOverScreen").style.display = "flex";
}

// Loop
function loop() {
  if (!gameRunning) return;
  update();
  draw();
  requestAnimationFrame(loop);
}
</script>

</body>
</html>
