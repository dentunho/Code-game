# Code-game
products for the community
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

// Khởi tạo biến
let bird = { x: 50, y: 150, width: 20, height: 20, gravity: 0.6, lift: -10, velocity: 0 };
let pipes = [];
let frame = 0;
let score = 0;
let gameOver = false;

// Điều khiển
document.addEventListener("keydown", () => {
    if (gameOver) resetGame();
    bird.velocity += bird.lift;
});

function resetGame() {
    bird.y = 150;
    bird.velocity = 0;
    pipes = [];
    score = 0;
    frame = 0;
    gameOver = false;
    loop();
}

function draw() {
    // Xóa canvas
    ctx.fillStyle = "#70c5ce";
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // Vẽ Chim
    ctx.fillStyle = "yellow";
    ctx.fillRect(bird.x, bird.y, bird.width, bird.height);

    // Vẽ Ống
    ctx.fillStyle = "green";
    pipes.forEach(pipe => {
        ctx.fillRect(pipe.x, 0, 50, pipe.top);
        ctx.fillRect(pipe.x, canvas.height - pipe.bottom, 50, pipe.bottom);
    });

    // Vẽ Điểm
    ctx.fillStyle = "black";
    ctx.font = "20px Arial";
    ctx.fillText(`Score: ${score}`, 10, 25);

    if (gameOver) {
        ctx.fillText("GAME OVER - Press any key", 40, canvas.height / 2);
    }
}

function update() {
    if (gameOver) return;

    bird.velocity += bird.gravity;
    bird.y += bird.velocity;

    // Tạo ống mới mỗi 100 frame
    if (frame % 100 === 0) {
        let spacing = 120;
        let topHeight = Math.random() * (canvas.height / 2);
        pipes.push({ x: canvas.width, top: topHeight, bottom: canvas.height - topHeight - spacing });
    }

    pipes.forEach((pipe, index) => {
        pipe.x -= 2;

        // Kiểm tra va chạm
        if (bird.x < pipe.x + 50 && bird.x + bird.width > pipe.x &&
            (bird.y < pipe.top || bird.y + bird.height > canvas.height - pipe.bottom)) {
            gameOver = true;
        }

        if (pipe.x === 0) score++;
        if (pipe.x + 50 < 0) pipes.splice(index, 1);
    });

    if (bird.y + bird.height > canvas.height || bird.y < 0) gameOver = true;
    frame++;
}

function loop() {
    update();
    draw();
    if (!gameOver) requestAnimationFrame(loop);
}

loop();
