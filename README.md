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
            background-color: #f0f0f0;
            padding: 10px;
            box-sizing: border-box;
        }
        h1 {
            color: #FF4500;
            text-align: center;
            font-size: 24px;
        }
        #game-board {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            grid-gap: 10px;
            margin-top: 20px;
            max-width: 300px; /* Reduced max width for smaller screens */
            width: 100%;
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
            font-size: 20px; /* Reduced font size for smaller screens */
        }
        .card-front {
            background-color: #FFD700;
        }
        .card-back {
            background-color: #4CAF50;
            transform: rotateY(180deg);
        }
        #result, #code, #attempts-info {
            margin-top: 20px;
            font-size: 16px; /* Reduced font size for smaller screens */
            color: #FF4500;
            text-align: center;
        }
        #code {
            color: green;
        }
    </style>
</head>
<body>

    <h1>Match a Pair and Win!</h1>
    <div id="attempts-info"></div>
    <div id="game-board"></div>
    <div id="result"></div>
    <div id="code"></div>

    <script>
        const images = ['🍎', '🍌', '🍇', '🍎', '🍌', '🍇'];

        let hasFlippedCard = false;
        let firstCard, secondCard;
        let attempts = 0;
        const maxAttempts = 2;
        const gameBoard = document.getElementById('game-board');
        const resultDisplay = document.getElementById('result');
        const codeDisplay = document.getElementById('code');
        const attemptsInfo = document.getElementById('attempts-info');

        // Function to get today's date as a string
        function getTodayDate() {
            return new Date().toISOString().split('T')[0];
        }

        // Function to check and update attempts
        function checkAttempts() {
            const today = getTodayDate(); // Get today's date in YYYY-MM-DD format
            const lastAttemptDate = localStorage.getItem('lastAttemptDate');
            const storedAttempts = localStorage.getItem('attempts') || 0;

            if (lastAttemptDate !== today) {
                // Reset attempts if the last attempt was on a different day
                localStorage.setItem('attempts', 0);
                localStorage.setItem('lastAttemptDate', today);
                attempts = 0;
            } else {
                attempts = parseInt(storedAttempts);
            }

            attemptsInfo.textContent = `Attempts today: ${attempts}/${maxAttempts}`;

            if (attempts >= maxAttempts) {
                resultDisplay.textContent = "You've reached the maximum attempts for today. Please try again tomorrow.";
                disableBoard();
                return false;
            }
            return true;
        }

        function updateAttempts() {
            attempts++;
            localStorage.setItem('attempts', attempts);
            attemptsInfo.textContent = `Attempts today: ${attempts}/${maxAttempts}`;
        }

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
                revealCode();  // Win immediately after matching one pair
            } else {
                unflipCards();
                updateAttempts();
                if (attempts >= maxAttempts) {
                    endGame();
                }
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

        if (checkAttempts()) {
            createBoard();
        }
    </script>

</body>
</html>
