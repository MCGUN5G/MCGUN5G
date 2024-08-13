<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Catch the Falling Objects</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <canvas id="gameCanvas"></canvas>
    <script src="script.js"></script>
</body>
</html>body {
    margin: 0;
    padding: 0;
    overflow: hidden;
    background: #f0f0f0;
}

#gameCanvas {
    display: block;
    background: #ffffff;
    margin: 0 auto;
}const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const playerWidth = 60;
const playerHeight = 10;
const objectSize = 20;
let playerX = canvas.width / 2 - playerWidth / 2;
let playerY = canvas.height - playerHeight - 10;
let objects = [];
let keys = {};
let score = 0;

canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

function drawPlayer() {
    ctx.fillStyle = 'red';
    ctx.fillRect(playerX, playerY, playerWidth, playerHeight);
}

function drawObjects() {
    ctx.fillStyle = 'blue';
    objects.forEach(obj => {
        ctx.fillRect(obj.x, obj.y, objectSize, objectSize);
    });
}

function updateObjects() {
    objects.forEach(obj => {
        obj.y += 5;
        if (obj.y > canvas.height) {
            objects.shift();
            score--;
        }
    });

    // Check for collisions
    objects.forEach((obj, index) => {
        if (obj.y + objectSize > playerY &&
            obj.x < playerX + playerWidth &&
            obj.x + objectSize > playerX) {
            objects.splice(index, 1);
            score++;
        }
    });
}

function drawScore() {
    ctx.fillStyle = 'black';
    ctx.font = '24px Arial';
    ctx.fillText(`Score: ${score}`, 10, 30);
}

function gameLoop() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawPlayer();
    drawObjects();
    updateObjects();
    drawScore();
    requestAnimationFrame(gameLoop);
}

function spawnObject() {
    const x = Math.random() * (canvas.width - objectSize);
    objects.push({ x, y: 0 });
}

window.addEventListener('resize', () => {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
});

window.addEventListener('keydown', (e) => {
    keys[e.code] = true;
});

window.addEventListener('keyup', (e) => {
    keys[e.code] = false;
});

function movePlayer() {
    if (keys['ArrowLeft'] && playerX > 0) {
        playerX -= 10;
    }
    if (keys['ArrowRight'] && playerX < canvas.width - playerWidth) {
        playerX += 10;
    }
}

setInterval(spawnObject, 1000);
requestAnimationFrame(gameLoop);
setInterval(movePlayer, 20);
