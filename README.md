<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Bouncing Blue Balls Game</title>
<style>
    body {
        font-family: Arial, sans-serif;
    }
    #container {
        position: relative;
        width: 500px;
        height: 500px;
        border: 1px solid black;
        overflow: hidden;
        margin: 0 auto;
        background-color: #f0f0f0;
    }

    .ball {
        position: absolute;
        width: 30px;
        height: 30px;
        border-radius: 50%;
        background-color: blue;
        animation: moveBall 2s linear infinite alternate;
    }

    .wall {
        position: absolute;
        background-color: black;
    }

    #leftWall {
        width: 20px;
        height: 500px;
        top: 0;
        left: 0;
        opacity: 1;
    }

    #rightWall {
        width: 20px;
        height: 500px;
        top: 0;
        right: 0;
        opacity: 1;
    }

    #topWall {
        width: 500px;
        height: 20px;
        top: 0;
        left: 0;
        opacity: 1;
    }

    #bottomWall {
        width: 500px;
        height: 20px;
        bottom: 0;
        left: 0;
        opacity: 1;
    }

    #cube {
        position: absolute;
        width: 50px;
        height: 50px;
        background-color: red;
        top: 10px;
        left: 10px;
        transition: top 0.1s, left 0.1s;
    }

    #finishLine {
        position: absolute;
        width: 50px;
        height: 50px;
        background-color: green;
        bottom: 10px;
        right: 10px;
    }

    #yellowBall {
        position: absolute;
        width: 30px;
        height: 30px;
        border-radius: 50%;
        background-color: yellow;
        top: 250px;
        left: 250px;
    }

    #endScreen {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        font-size: 40px;
        color: black;
        display: none;
    }

    #playAgainButton, #nextLevelButton {
        position: absolute;
        top: 10px;
        right: 10px;
        padding: 10px 20px;
        font-size: 16px;
        background-color: #4CAF50;
        color: white;
        border: none;
        cursor: pointer;
        display: none;
    }

    @keyframes moveBall {
        from {
            top: 0;
        }
        to {
            top: calc(100% - 30px);
        }
    }

    @keyframes moveBallHorizontal {
        from {
            left: 0;
        }
        to {
            left: calc(100% - 30px);
        }
    }

    #score {
        position: absolute;
        top: 10px;
        left: 10px;
        font-size: 20px;
    }
</style>
</head>
<body>
<div id="container">
    <!-- Walls -->
    <div id="leftWall" class="wall"></div>
    <div id="rightWall" class="wall"></div>
    <div id="topWall" class="wall"></div>
    <div id="bottomWall" class="wall"></div>

    <!-- Blue Balls -->
    <div class="ball" style="top: 100px; left: 100px; animation: moveBall 2s linear infinite alternate;"></div>
    <div class="ball" style="top: 200px; left: 300px; animation: moveBallHorizontal 3s linear infinite alternate;"></div>
    <div class="ball" style="top: 300px; left: 400px; animation: moveBall 4s linear infinite alternate;"></div>

    <!-- Red Cube -->
    <div id="cube"></div>

    <!-- Finish Line -->
    <div id="finishLine"></div>

    <!-- Yellow Ball -->
    <div id="yellowBall"></div>

    <!-- End Screen -->
    <div id="endScreen">GG</div>

    <!-- Score -->
    <div id="score">Score: 0</div>

    <!-- Play Again Button -->
    <button id="playAgainButton">Play Again</button>

    <!-- Next Level Button -->
    <button id="nextLevelButton">Next Level</button>
</div>

<script>
    document.addEventListener("DOMContentLoaded", function() {
        const container = document.getElementById('container');
        const cube = document.getElementById('cube');
        const yellowBall = document.getElementById('yellowBall');
        const endScreen = document.getElementById('endScreen');
        const playAgainButton = document.getElementById('playAgainButton');
        const nextLevelButton = document.getElementById('nextLevelButton');
        const scoreDisplay = document.getElementById('score');
        let positionX = 0;
        let positionY = 0;
        let score = 0;
        let level = 1;
        let gameOver = false;
        let yellowBallCollected = false;

        const moveSounds = new Audio('move.mp3'); // Replace with actual path to sound file
        const collisionSound = new Audio('collision.mp3'); // Replace with actual path to sound file
        const winSound = new Audio('win.mp3'); // Replace with actual path to sound file
        const collectSound = new Audio('collect.mp3'); // Replace with actual path to sound file for collecting yellow ball

        function updateScore() {
            scoreDisplay.innerText = `Score: ${score}`;
        }

        function resetGame() {
            gameOver = false;
            yellowBallCollected = false;
            cube.style.top = '10px';
            cube.style.left = '10px';
            positionX = 0;
            positionY = 0;
            yellowBall.style.display = 'block';
            endScreen.style.display = 'none';
            playAgainButton.style.display = 'none';
            nextLevelButton.style.display = 'none';
        }

        function checkCollision() {
            if (!gameOver) {
                const cubeRect = cube.getBoundingClientRect();
                const balls = document.getElementsByClassName('ball');
                const finishLineRect = document.getElementById('finishLine').getBoundingClientRect();
                const yellowBallRect = yellowBall.getBoundingClientRect();

                // Check collisions with blue balls
                for (let ball of balls) {
                    const ballRect = ball.getBoundingClientRect();
                    if (
                        cubeRect.right >= ballRect.left &&
                        cubeRect.left <= ballRect.right &&
                        cubeRect.bottom >= ballRect.top &&
                        cubeRect.top <= ballRect.bottom
                    ) {
                        // Show end screen and play again button
                        gameOver = true;
                        collisionSound.play();
                        endScreen.style.display = 'block';
                        playAgainButton.style.display = 'block';
                        break;
                    }
                }

                // Check collision with finish line
                if (
                    cubeRect.right >= finishLineRect.left &&
                    cubeRect.left <= finishLineRect.right &&
                    cubeRect.bottom >= finishLineRect.top &&
                    cubeRect.top <= finishLineRect.bottom
                ) {
                    if (yellowBallCollected) {
                        // Show end screen and next level button
                        gameOver = true;
                        winSound.play();
                        score += 100; // Increase score
                        updateScore();
                        endScreen.style.display = 'block';
                        nextLevelButton.style.display = 'block';
                    }
                }

                // Check collision with yellow ball
                if (
                    cubeRect.right >= yellowBallRect.left &&
                    cubeRect.left <= yellowBallRect.right &&
                    cubeRect.bottom >= yellowBallRect.top &&
                    cubeRect.top <= yellowBallRect.bottom
                ) {
                    collectSound.play();
                    yellowBall.style.display = 'none';
                    yellowBallCollected = true;
                    score += 50; // Increase score for collecting yellow ball
                    updateScore();
                }
            }
        }

        function moveCube(event) {
            if (!gameOver) {
                moveSounds.play();
                switch(event.key) {
                    case 'ArrowUp':
                        if (positionY > 0) {
                            positionY -= 20;
                        }
                        break;
                    case 'ArrowDown':
                        if (positionY < container.clientHeight - cube.clientHeight) {
                            positionY += 20;
                        }
                        break;
                    case 'ArrowLeft':
                        if (positionX > 0) {
                            positionX -= 20;
                        }
                        break;
                    case 'ArrowRight':
                        if (positionX < container.clientWidth - cube.clientWidth) {
                            positionX += 20;
                        }
                        break;
                }
                cube.style.left = positionX + 'px';
                cube.style.top = positionY + 'px';

                checkCollision();
            }
        }

        function playAgain() {
            resetGame();
        }

        function goToNextLevel() {
            level++;
            alert(`Next level: ${level}`);
            // Increase difficulty by adding more balls or changing their speed
            // Example: Add more balls
            const newBall = document.createElement('div');
            newBall.className = 'ball';
            newBall.style.top = '150px';
            newBall.style.left = '200px';
            newBall.style.animation = 'moveBall 3s linear infinite alternate';
            container.appendChild(newBall);
            resetGame();
        }

        document.addEventListener('keydown', moveCube);
        playAgainButton.addEventListener('click', playAgain);
        nextLevelButton.addEventListener('click', goToNextLevel);

        // Continuous collision detection
        setInterval(checkCollision, 100); // Check for collisions every 100ms
    });
</script>
</body>
</html>
