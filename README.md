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
            background-color: #f0f0f0;
        }
        h1 {
            color: #FF4500;
        }
        #game-board {
            display: grid;
            grid-template-columns: repeat(5, 100px);
            grid-gap: 10px;
        }
        .card {
            width: 100px;
            height: 100px;
            background-color: #FFD700;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 36px;
            cursor: pointer;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        .card img {
            width: 80%;
            height: 80%;
            display: none;
        }
        .matched img {
            display: block;
        }
        #result {
            margin-top: 20px;
            font-size: 24px;
            color: #FF4500;
        }
        #code {
            margin-top: 10px;
            font-size: 20px;
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
        const images = [
            '🍎', '🍌', '🍇', '🍉', '🍊',
            '🍎', '🍌', '🍇', '🍉', '🍊',
            '🐶', '🐱', '🐭', '🐹', '🐰',
            '🐶', '🐱', '🐭', '🐹', '🐰',
            '🚗', '🚕', '🚙', '🚌', '🚓',
            '🚗', '🚕', '🚙', '🚌', '🚓',
        ];

        let hasFlippedCard = false;
        let firstCard, secondCard;
        let matchedPairs = 0;
        let attempts = 0;
        const maxAttempts = 5;
        const gameBoard = document.getElementById('game-board');
        const resultDisplay = document.getElementById('result');
        const codeDisplay = document.getElementById('code');

        function shuffle(array) {
            array.sort(() => Math.random() - 0.5);
        }

        function createBoard() {
            shuffle(images);
            images.forEach((image, index) => {
                const card = document.createElement('div');
                card.classList.add('card');
                card.setAttribute('data-image', image);
                card.innerHTML = `<span>${image}</span>`;
                card.addEventListener('click', flipCard);
                gameBoard.appendChild(card);
            });
        }

        function flipCard() {
            if (this === firstCard) return;
            this.classList.add('flipped');
            this.querySelector('span').style.display = 'block';

            if (!hasFlippedCard) {
                hasFlippedCard = true;
                firstCard = this;
                return;
            }

            secondCard = this;
            checkForMatch();
        }

        function checkForMatch() {
            let isMatch = firstCard.dataset.image === secondCard.dataset.image;

            if (isMatch) {
                disableCards();
                matchedPairs++;
                if (matchedPairs === 5) {
                    revealCode();
                }
            } else {
                unflipCards();
            }
            attempts++;
            if (attempts >= maxAttempts && matchedPairs < 5) {
                endGame();
            }
        }

        function disableCards() {
            firstCard.removeEventListener('click', flipCard);
            secondCard.removeEventListener('click', flipCard);
            firstCard.classList.add('matched');
            secondCard.classList.add('matched');
            resetBoard();
        }

        function unflipCards() {
            setTimeout(() => {
                firstCard.classList.remove('flipped');
                secondCard.classList.remove('flipped');
                firstCard.querySelector('span').style.display = 'none';
                secondCard.querySelector('span').style.display = 'none';
                resetBoard();
            }, 1000);
        }

        function resetBoard() {
            [hasFlippedCard, firstCard, secondCard] = [false, null, null];
        }

        function revealCode() {
            codeDisplay.textContent = "Congratulations! Use code 'LISTO122' for 20% off!";
        }

        function endGame() {
            resultDisplay.textContent = "Game over! Try again in 24 hours!";
            gameBoard.classList.add('disabled');
            const cards = document.querySelectorAll('.card');
            cards.forEach(card => card.removeEventListener('click', flipCard));
        }

        createBoard();
    </script>

</body>
</html>
