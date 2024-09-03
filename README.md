<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Memory Matching Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            padding: 10px;
            box-sizing: border-box;
            background-image: url('burrito-background.jpg');
            background-size: cover;
            background-position: center;
            color: white; /* Set text color to white for better contrast */
        }
        h1 {
            color: #FFD700; /* Adjusted to stand out against the background */
            text-align: center;
            font-size: 24px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5); /* Add shadow for better readability */
        }
        #game-board {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            grid-gap: 10px;
            margin-top: 20px;
            max-width: 300px;
            width: 100%;
            background-color: rgba(0, 0, 0, 0.5); /* Semi-transparent background for the game area */
            padding: 10px;
            border-radius: 10px;
        }
        .card {
            width: 100%;
            padding-bottom: 100%; /* Maintain square aspect ratio */
            position: relative;
            perspective: 1000px;
        }
        .card-inner {
            position: absolute;
            width: 100%;
            height: 100%;
            text-align: center;
            transition: transform 0.6s;
            transform-style: preserve-3d;
            cursor: pointer;
        }
        .card.flipped .card-inner {
            transform: rotateY(180deg);
        }
        .card-front, .card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            border-radius: 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 20px;
            background-color: white; /* Ensure cards stand out against the background */
            color: #282c34;
        }
        .card-back {
            background-color: #FFD700;
            transform: rotateY(180deg);
        }
        #result, #code {
            margin-top: 20px;
            font-size: 16px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
            color: #FFD700;
            text-align: center;
        }
        #code {
            color: green;
        }
    </style>
</head>
<body>

    <h1>Match the Pairs and Win!</h1>
    <div id="game-board"></div>
    <div id="result"></div>
    <div id="code"></div>

    <script>
        const images = ['🍎', '🍌', '🍇', '🍎', '🍌', '🍇'];

        let hasFlippedCard = false;
        let firstCard, secondCard;
        let matchedPairs = 0;
        let attempts = 0;
        const maxAttempts = 2;
        const gameBoard = document.getElementById('game-board');
        const resultDisplay = document.getElementById('result');
        const codeDisplay = document.getElementById('code');

        function shuffle(array) {
            array.sort(() => Math.random() - 0.5);
        }

        function createBoard() {
            shuffle(images);
            images.forEach((image) => {
                const card = document.createElement('div');
                card.classList.add('card');
                card.innerHTML = `
                    <div class="card-inner">
                        <div class="card-front">?</div>
                        <div class="card-back">${image}</div>
                    </div>
                `;
                card.addEventListener('click', flipCard);
                gameBoard.appendChild(card);
            });
        }

        function flipCard() {
            if (this === firstCard || !checkAttempts()) return;
            this.classList.add('flipped');

            if (!hasFlippedCard) {
                hasFlippedCard = true;
                firstCard = this;
                return;
            }

            secondCard = this;
            checkForMatch();
        }

        function checkForMatch() {
            let isMatch = firstCard.querySelector('.card-back').textContent === secondCard.querySelector('.card-back').textContent;

            if (isMatch) {
                disableCards();
                matchedPairs++;
                if (matchedPairs === 3) {  // Ensure the game is won only after all 3 pairs are matched
                    revealCode();
                }
            } else {
                unflipCards();
            }
            updateAttempts();
            if (attempts >= maxAttempts && matchedPairs < 3) {
                endGame();
            }
        }

        function disableCards() {
            firstCard.removeEventListener('click', flipCard);
            secondCard.removeEventListener('click', flipCard);
            resetBoard();
        }

        function unflipCards() {
            setTimeout(() => {
                firstCard.classList.remove('flipped');
                secondCard.classList.remove('flipped');
                resetBoard();
            }, 1000);
        }

        function resetBoard() {
            [hasFlippedCard, firstCard, secondCard] = [false, null, null];
        }

        function revealCode() {
            codeDisplay.textContent = "Congratulations! Use code 'LISTO29' for 20% off!";
            disableBoard();
        }

        function endGame() {
            resultDisplay.textContent = "Game over! Try again in 24 hours!";
            disableBoard();
        }

        function disableBoard() {
            const cards = document.querySelectorAll('.card');
            cards.forEach(card => card.removeEventListener('click', flipCard));
        }

        function checkAttempts() {
            return true; // For testing, allow unlimited attempts. Adjust this as needed.
        }

        function updateAttempts() {
            attempts++; // For testing, track attempts. Adjust this as needed.
        }

        createBoard();
    </script>

</body>
</html>
