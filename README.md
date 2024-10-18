<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced Whack-a-Circle Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
        }
        .game-container {
            width: 400px;
            height: 400px;
            background-color: #fff;
            border: 2px solid #333;
            position: relative;
            overflow: hidden;
        }
        .circle {
            border-radius: 50%;
            position: absolute;
            cursor: pointer;
            transition: transform 0.1s, opacity 0.3s;
        }
        .circle:active {
            transform: scale(0.9);
        }
        .good {
            background-color: #4CAF50;
        }
        .bad {
            background-color: #F44336;
        }
        .score, .timer, .level {
            position: absolute;
            font-size: 18px;
        }
        .score { top: 10px; left: 10px; }
        .timer { top: 10px; right: 10px; }
        .level { bottom: 10px; left: 10px; }
    </style>
</head>
<body>
    <div class="game-container">
        <div class="score">Score: <span id="score">0</span></div>
        <div class="timer">Time: <span id="timer">30</span>s</div>
        <div class="level">Level: <span id="level">1</span></div>
    </div>

    <script>
        const gameContainer = document.querySelector('.game-container');
        const scoreElement = document.getElementById('score');
        const timerElement = document.getElementById('timer');
        const levelElement = document.getElementById('level');
        let score = 0;
        let timeLeft = 30;
        let level = 1;
        let gameInterval;
        let spawnRate = 1000;
        let circleLifespan = 1500;
        let minSize = 30;
        let maxSize = 60;

        function createCircle() {
            const circle = document.createElement('div');
            circle.classList.add('circle');
            
            const size = Math.random() * (maxSize - minSize) + minSize;
            circle.style.width = `${size}px`;
            circle.style.height = `${size}px`;
            
            circle.style.left = `${Math.random() * (gameContainer.clientWidth - size)}px`;
            circle.style.top = `${Math.random() * (gameContainer.clientHeight - size)}px`;
            
            const isGood = Math.random() > 0.2;
            circle.classList.add(isGood ? 'good' : 'bad');
            
            circle.addEventListener('click', () => {
                if (isGood) {
                    score += Math.floor(100 / size);  // Smaller circles worth more points
                } else {
                    score = Math.max(0, score - 10);
                }
                scoreElement.textContent = score;
                gameContainer.removeChild(circle);
            });

            gameContainer.appendChild(circle);

            setTimeout(() => {
                if (gameContainer.contains(circle)) {
                    if (isGood) score = Math.max(0, score - 5);  // Penalty for missing good circles
                    scoreElement.textContent = score;
                    gameContainer.removeChild(circle);
                }
            }, circleLifespan);
        }

        function updateTimer() {
            timeLeft--;
            timerElement.textContent = timeLeft;
            if (timeLeft === 0) {
                clearInterval(gameInterval);
                alert(`Game Over! Your final score is ${score}`);
            }
        }

        function updateDifficulty() {
            level++;
            levelElement.textContent = level;
            spawnRate = Math.max(300, spawnRate - 100);
            circleLifespan = Math.max(500, circleLifespan - 100);
            minSize = Math.max(15, minSize - 2);
            maxSize = Math.max(30, maxSize - 3);
            clearInterval(gameInterval);
            startGame();
        }

        function startGame() {
            gameInterval = setInterval(() => {
                createCircle();
                updateTimer();
                if (timeLeft % 10 === 0 && timeLeft !== 30) {
                    updateDifficulty();
                }
            }, spawnRate);
        }

        startGame();
    </script>
</body>
</html>
