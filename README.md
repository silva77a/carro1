# carro1
// script.js

// Variáveis do jogo
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
let score = 0;
let gameOver = false;

// Carro
const car = {
    x: 180,
    y: 500,
    width: 40,
    height: 60,
    speed: 4,
    moveUp: false,
    moveDown: false
};

// Obstáculos
const obstacles = [];
const obstacleWidth = 40;
const obstacleHeight = 60;
let obstacleSpeed = 2;

// Função para desenhar o carro
function drawCar() {
    ctx.fillStyle = '#FF0000';
    ctx.fillRect(car.x, car.y, car.width, car.height);
}

// Função para desenhar os obstáculos
function drawObstacles() {
    ctx.fillStyle = '#00FF00';
    obstacles.forEach(obstacle => {
        ctx.fillRect(obstacle.x, obstacle.y, obstacleWidth, obstacleHeight);
    });
}

// Função para atualizar a posição dos obstáculos
function updateObstacles() {
    obstacles.forEach(obstacle => {
        obstacle.y += obstacleSpeed;
    });
    obstacles = obstacles.filter(obstacle => obstacle.y < canvas.height);
}

// Função para gerar novos obstáculos
function generateObstacle() {
    const x = Math.random() * (canvas.width - obstacleWidth);
    obstacles.push({ x, y: -obstacleHeight });
}

// Função para verificar colisão
function checkCollision() {
    obstacles.forEach(obstacle => {
        if (car.x < obstacle.x + obstacleWidth &&
            car.x + car.width > obstacle.x &&
            car.y < obstacle.y + obstacleHeight &&
            car.y + car.height > obstacle.y) {
            gameOver = true;
        }
    });
}

// Função para atualizar o jogo
function update() {
    if (gameOver) {
        ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = '#FFFFFF';
        ctx.font = '30px Arial';
        ctx.fillText('Game Over', 130, 250);
        ctx.font = '20px Arial';
        ctx.fillText('Pontuação final: ' + score, 140, 300);
        return;
    }

    // Atualiza a pontuação
    score++;

    // Limpa o canvas
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Desenha o carro
    drawCar();

    // Desenha os obstáculos
    drawObstacles();

    // Atualiza os obstáculos
    updateObstacles();

    // Verifica colisões
    checkCollision();

    // Atualiza a posição do carro
    if (car.moveUp && car.y > 0) {
        car.y -= car.speed;
    }
    if (car.moveDown && car.y < canvas.height - car.height) {
        car.y += car.speed;
    }

    // Atualiza a pontuação na tela
    document.getElementById('score').textContent = 'Pontuação: ' + score;

    // Solicita o próximo quadro
    if (!gameOver) {
        requestAnimationFrame(update);
    }
}

// Função para lidar com os controles
function handleKeyDown(event) {
    if (event.key === 'ArrowUp') {
        car.moveUp = true;
    } else if (event.key === 'ArrowDown') {
        car.moveDown = true;
    }
}

function handleKeyUp(event) {
    if (event.key === 'ArrowUp') {
        car.moveUp = false;
    } else if (event.key === 'ArrowDown') {
        car.moveDown = false;
    }
}

// Função para iniciar o jogo
function startGame() {
    // Limpa o jogo antes de iniciar
    score = 0;
    gameOver = false;
    obstacles.length = 0;
    car.y = 500;

    // Adiciona obstáculos a cada 1.5 segundos
    setInterval(generateObstacle, 1500);

    // Atualiza o jogo
    update();
}

// Inicia o jogo quando a página carrega
startGame();

// Eventos de teclado
document.addEventListener('keydown', handleKeyDown);
document.addEventListener('keyup', handleKeyUp);
