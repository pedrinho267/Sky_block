<!DOCTYPE html>
<html>
<head>
    <title>Corrida Retro Gemini</title>
    <style>
        body { display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; background: #222; font-family: sans-serif; color: white; overflow: hidden; }
        canvas { border: 4px solid #fff; background: #333; box-shadow: 0 0 20px rgba(0,0,0,0.5); }
        .info { position: absolute; top: 10px; text-align: center; }
    </style>
</head>
<body>
    <div class="info">
        <h2>Corrida Veloz</h2>
        <p>Use as SETAS do teclado para desviar!</p>
    </div>
    <canvas id="gameCanvas" width="400" height="600"></canvas>

<script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    // Configurações do Jogo
    let player = { x: 175, y: 500, w: 50, h: 80, speed: 7 };
    let enemies = [];
    let score = 0;
    let gameActive = true;
    let speedMultiplier = 1;

    // Teclas
    let keys = {};
    document.addEventListener("keydown", (e) => keys[e.keyCode] = true);
    document.addEventListener("keyup", (e) => keys[e.keyCode] = false);

    function spawnEnemy() {
        if (Math.random() < 0.02 * speedMultiplier) {
            let lane = Math.floor(Math.random() * 3);
            enemies.push({ x: lane * 133 + 40, y: -100, w: 50, h: 80 });
        }
    }

    function update() {
        if (!gameActive) return;

        // Movimento do jogador
        if (keys[37] && player.x > 10) player.x -= player.speed;
        if (keys[39] && player.x < canvas.width - player.w - 10) player.x += player.speed;

        // Atualizar inimigos
        for (let i = 0; i < enemies.length; i++) {
            enemies[i].y += 5 * speedMultiplier;

            // Colisão
            if (player.x < enemies[i].x + enemies[i].w &&
                player.x + player.w > enemies[i].x &&
                player.y < enemies[i].y + enemies[i].h &&
                player.y + player.h > enemies[i].y) {
                gameActive = false;
                alert("GAME OVER! Pontuação: " + Math.floor(score));
                location.reload();
            }

            // Remover carros que passaram
            if (enemies[i].y > canvas.height) {
                enemies.splice(i, 1);
                score += 10;
                speedMultiplier += 0.01;
            }
        }
        spawnEnemy();
        score += 0.1;
    }

    function draw() {
        // Fundo (Estrada)
        ctx.fillStyle = "#444";
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        
        // Linhas da estrada
        ctx.strokeStyle = "#fff";
        ctx.setLineDash([20, 20]);
        ctx.lineWidth = 5;
        ctx.beginPath();
        ctx.moveTo(133, 0); ctx.lineTo(133, canvas.height);
        ctx.moveTo(266, 0); ctx.lineTo(266, canvas.height);
        ctx.stroke();

        // Jogador (Carro Azul)
        ctx.fillStyle = "#3498db";
        ctx.fillRect(player.x, player.y, player.w, player.h);
        ctx.fillStyle = "#000"; // Rodas
        ctx.fillRect(player.x - 5, player.y + 10, 5, 20);
        ctx.fillRect(player.x + player.w, player.y + 10, 5, 20);

        // Inimigos (Carros Verdes)
        ctx.fillStyle = "#2ecc71";
        enemies.forEach(en => {
            ctx.fillRect(en.x, en.y, en.w, en.h);
        });

        // Placar
        ctx.fillStyle = "white";
        ctx.font = "20px Arial";
        ctx.fillText("Score: " + Math.floor(score), 20, 30);

        update();
        requestAnimationFrame(draw);
    }

    draw();
</script>
</body>
</html>
# Sky_block
