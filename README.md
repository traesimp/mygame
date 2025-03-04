<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Adventure Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: url(download.jpg) no-repeat center center;
             background-size: cover;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            position: relative;
        }
        canvas {
            background: url(satc.jpg) no-repeat center center;
            background-size: cover;
            display: none;
            width: 600px;
            height: 400px;
        }
        #startScreen, #levelScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            background: rgba(0, 0, 0, 0.5);
            padding: 20px;
            border-radius: 10px;
        }
        #startButton, .levelButton {
            padding: 10px 20px;
            font-size: 20px;
            margin: 5px;
        }
        .joystick {
            display: none;
            position: absolute;
            bottom: 50px;
            left: 50px;
            width: 100px;
            height: 100px;
            background: rgba(0, 0, 0, 0.5);
            border-radius: 50%;
            touch-action: none;
        }
        .joystick-inner {
            width: 50px;
            height: 50px;
            background: white;
            border-radius: 50%;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
        }
        #attackButton {
            display: none;
            position: absolute;
            bottom: 50px;
            right: 50px;
            width: 80px;
            height: 80px;
            background: red;
            color: white;
            font-size: 18px;
            border-radius: 50%;
            text-align: center;
            line-height: 80px;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div id="startScreen">
        <h1>Adventure Game</h1>
        <button id="startButton" onclick="showLevelScreen()">Start Game</button>
    </div>
    <div id="levelScreen" style="display: none;">
        <h2>Select Level</h2>
        <div id="levelButtons"></div>
    </div>
    <canvas id="gameCanvas"></canvas>
    <div class="joystick" id="joystick">
        <div class="joystick-inner" id="joystickInner"></div>
    </div>
    <button id="attackButton" onclick="attack()">Attack</button>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        canvas.width = 600;
        canvas.height = 400;
        
        let player = { x: 50, y: 50, size: 30, speed: 5, attacking: false };
        let level = 1;
        let enemies = [];

        function showLevelScreen() {
            document.getElementById("startScreen").style.display = "none";
            document.getElementById("levelScreen").style.display = "block";
            generateLevelButtons();
        }

        function generateLevelButtons() {
            let levelButtons = document.getElementById("levelButtons");
            levelButtons.innerHTML = "";
            for (let i = 1; i <= 10; i++) {
                let button = document.createElement("button");
                button.className = "levelButton";
                button.textContent = "Level " + i;
                button.disabled = i > level;
                button.onclick = () => startGame(i);
                levelButtons.appendChild(button);
            }
        }

        function startGame(selectedLevel) {
            level = selectedLevel;
            document.getElementById("levelScreen").style.display = "none";
            canvas.style.display = "block";
            document.getElementById("joystick").style.display = "block";
            document.getElementById("attackButton").style.display = "block";
            spawnEnemies(level);
            gameLoop();
        }

        function attack() {
            player.attacking = true;
            setTimeout(() => player.attacking = false, 500);
        }

        function spawnEnemies(level) {
            enemies = [];
            for (let i = 0; i < level + 2; i++) {
                enemies.push({
                    x: Math.random() * (canvas.width - 30),
                    y: Math.random() * (canvas.height - 30),
                    size: 30,
                    speed: 2,
                    dx: Math.random() > 0.5 ? 2 : -2,
                    dy: Math.random() > 0.5 ? 2 : -2
                });
            }
        }

        function update() {
            enemies.forEach((enemy, index) => {
                enemy.x += enemy.dx;
                enemy.y += enemy.dy;
                if (enemy.x <= 0 || enemy.x + enemy.size >= canvas.width) enemy.dx *= -1;
                if (enemy.y <= 0 || enemy.y + enemy.size >= canvas.height) enemy.dy *= -1;
                if (player.attacking && player.x < enemy.x + enemy.size && player.x + player.size > enemy.x && player.y < enemy.y + enemy.size && player.y + player.size > enemy.y) {
                    enemies.splice(index, 1);
                }
            });
            if (enemies.length === 0) {
                level++;
                spawnEnemies(level);
            }
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = player.attacking ? "orange" : "red";
            ctx.fillRect(player.x, player.y, player.size, player.size);
            ctx.fillStyle = "black";
            enemies.forEach(enemy => {
                ctx.fillRect(enemy.x, enemy.y, enemy.size, enemy.size);
            });
        }

        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }
    </script>
</body>
</html>

