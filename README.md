<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dominó Clássico</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap');

        body {
            font-family: 'Inter', sans-serif;
            background-color: #1a202c; /* Fundo escuro */
            color: #e2e8f0; /* Texto claro */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 1rem;
            box-sizing: border-box;
            user-select: none;
        }
        #app {
            width: 100%;
            max-width: 1200px;
            display: flex;
            flex-direction: column;
            gap: 1.5rem;
        }
        .domino-tile {
            width: 3rem;
            height: 6rem;
            background-color: #f7fafc;
            border: 2px solid #a0aec0;
            border-radius: 0.5rem;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            display: flex;
            flex-direction: column;
            justify-content: space-around;
            align-items: center;
            padding: 0.5rem;
            transition: transform 0.2s, box-shadow 0.2s;
            cursor: pointer;
            flex-shrink: 0;
            user-select: none;
            position: relative;
        }
        .domino-tile.horizontal {
            width: 6rem;
            height: 3rem;
            flex-direction: row;
        }
        .domino-tile:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 12px rgba(0, 0, 0, 0.2);
        }
        .dot-container {
            display: flex;
            justify-content: center;
            align-items: center;
            width: 100%;
            height: 100%;
            gap: 2px;
            flex-wrap: wrap;
        }
        .dot {
            width: 0.5rem;
            height: 0.5rem;
            background-color: #1a202c;
            border-radius: 50%;
            display: block;
        }
        .domino-divider {
            width: 90%;
            height: 2px;
            background-color: #a0aec0;
            border-radius: 1px;
        }
        .domino-divider.horizontal {
            height: 90%;
            width: 2px;
        }
        .player-hand-tile.domino-tile:hover {
            transform: translateY(-10px) scale(1.1);
            z-index: 10;
        }
        .ai-hand-tile.domino-tile {
            background-color: #4a5568;
            cursor: not-allowed;
            pointer-events: none;
        }
        .ai-hand-tile.domino-tile:hover {
            transform: none;
            box-shadow: none;
        }
        #board {
            min-height: 200px;
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            align-items: center;
            gap: 0.5rem;
            border: 2px dashed #4a5568;
            padding: 1rem;
            border-radius: 1rem;
            overflow-x: auto;
            max-width: 100%;
        }
        #player-hand {
            display: flex;
            justify-content: center;
            gap: 0.75rem;
            flex-wrap: wrap;
        }
        #ai-hand {
            display: flex;
            justify-content: center;
            gap: 0.5rem;
            flex-wrap: wrap;
        }
        .message-box {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(0, 0, 0, 0.8);
            color: white;
            padding: 2rem 3rem;
            border-radius: 1rem;
            text-align: center;
            z-index: 1000;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.5);
            display: none;
            flex-direction: column;
            gap: 1.5rem;
            animation: fadeIn 0.3s ease-in-out;
        }
        .message-box h2 {
            font-size: 2rem;
            font-weight: bold;
        }
        .btn {
            padding: 0.75rem 2rem;
            border-radius: 9999px;
            font-weight: bold;
            text-transform: uppercase;
            letter-spacing: 0.05em;
            transition: transform 0.2s, box-shadow 0.2s, background-color 0.2s;
            background: linear-gradient(145deg, #4299e1, #3182ce);
            color: white;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 12px rgba(0, 0, 0, 0.2);
            background: linear-gradient(145deg, #3182ce, #2c5282);
        }
        .pass-btn {
            background: linear-gradient(145deg, #f56565, #e53e3e);
            margin-top: 1rem;
        }
        .pass-btn:hover {
            background: linear-gradient(145deg, #e53e3e, #c53030);
        }
        .game-info {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 1rem;
            padding: 1rem;
            background-color: #2d3748;
            border-radius: 1rem;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        .turn-indicator {
            font-weight: bold;
            font-size: 1.25rem;
            transition: color 0.3s;
        }
        .dot-icon {
            font-size: 2rem;
            line-height: 1;
        }
        .domino-tile-ghost {
            opacity: 0.3;
            cursor: not-allowed;
        }

        /* Animação */
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

    </style>
</head>
<body class="bg-gray-900 text-gray-200">
    <div id="app">
        <h1 class="text-3xl md:text-4xl font-bold text-center mb-6">Dominó Clássico</h1>

        <!-- Mensagem de status e info do jogo -->
        <div class="game-info">
            <span id="turn-display" class="turn-indicator">Selecione uma dificuldade para começar.</span>
            <div class="flex items-center gap-2">
                <span class="text-lg">IA: <span id="ai-hand-size">0</span> peças</span>
                <span class="text-lg">Baralho: <span id="deck-size">0</span> peças</span>
            </div>
        </div>

        <!-- Área de jogo -->
        <div class="flex flex-col gap-4">
            <!-- Mão da IA (escondida) -->
            <div id="ai-hand" class="flex justify-center flex-wrap gap-2 md:gap-4"></div>

            <!-- Tabuleiro -->
            <div id="board" class="bg-gray-800">
                <p id="board-placeholder" class="text-gray-500">O tabuleiro está vazio. Comece o jogo!</p>
            </div>

            <!-- Mão do jogador -->
            <div id="player-hand" class="bg-gray-800 p-4 rounded-xl shadow-lg"></div>
        </div>

        <!-- Botões de controle -->
        <div class="flex flex-col md:flex-row justify-center items-center gap-4 mt-4">
            <button id="pass-button" class="btn pass-btn hidden">Passar</button>
        </div>

    </div>

    <!-- Modal de seleção de dificuldade -->
    <div id="difficulty-modal" class="message-box flex-col">
        <h2 class="text-2xl font-bold">Selecione a Dificuldade</h2>
        <div class="flex flex-col gap-4">
            <button id="easy-btn" class="btn">Fácil</button>
            <button id="medium-btn" class="btn">Médio</button>
            <button id="hard-btn" class="btn">Difícil</button>
        </div>
    </div>

    <!-- Modal de resultado do jogo -->
    <div id="game-over-modal" class="message-box hidden">
        <h2 id="game-over-title" class="text-2xl font-bold"></h2>
        <p id="game-over-message" class="text-lg"></p>
        <button id="new-game-btn" class="btn">Novo Jogo</button>
    </div>

<script>
    document.addEventListener('DOMContentLoaded', () => {
        // Objeto principal do jogo para gerenciar o estado
        const Game = {
            deck: [],
            playerHand: [],
            aiHand: [],
            board: [],
            turn: 'player',
            difficulty: 'easy',
            isBlocked: false,
            difficultyModal: document.getElementById('difficulty-modal'),
            gameOverModal: document.getElementById('game-over-modal'),
            boardElement: document.getElementById('board'),
            playerHandElement: document.getElementById('player-hand'),
            aiHandElement: document.getElementById('ai-hand'),
            turnDisplay: document.getElementById('turn-display'),
            passButton: document.getElementById('pass-button'),
            aiHandSizeElement: document.getElementById('ai-hand-size'),
            deckSizeElement: document.getElementById('deck-size'),
        };

        // --- Funções de Lógica do Jogo ---

        // Função para criar todas as 28 peças de dominó (0-0 até 6-6)
        function createDeck() {
            Game.deck = [];
            for (let i = 0; i <= 6; i++) {
                for (let j = i; j <= 6; j++) {
                    Game.deck.push({ a: i, b: j });
                }
            }
        }

        // Função para embaralhar o baralho (algoritmo de Fisher-Yates)
        function shuffleDeck() {
            for (let i = Game.deck.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [Game.deck[i], Game.deck[j]] = [Game.deck[j], Game.deck[i]];
            }
        }

        // Função para distribuir 7 peças para cada jogador
        function dealHands() {
            Game.playerHand = Game.deck.splice(0, 7);
            Game.aiHand = Game.deck.splice(0, 7);
        }

        // Função para renderizar um único dominó
        function renderDomino(domino, isHorizontal = false, isPlayer = true) {
            const tile = document.createElement('div');
            tile.classList.add('domino-tile');
            tile.classList.add(isPlayer ? 'player-hand-tile' : 'ai-hand-tile');
            if (isHorizontal) {
                tile.classList.add('horizontal');
            }
            tile.dataset.valueA = domino.a;
            tile.dataset.valueB = domino.b;

            // Renderiza os pontos da peça
            const dotLayouts = {
                0: [],
                1: [5],
                2: [1, 9],
                3: [1, 5, 9],
                4: [1, 3, 7, 9],
                5: [1, 3, 5, 7, 9],
                6: [1, 2, 3, 7, 8, 9]
            };

            const createDots = (value, container) => {
                const layout = dotLayouts[value];
                const grid = document.createElement('div');
                grid.classList.add('grid', 'grid-cols-3', 'gap-1', 'w-10', 'h-10');
                if (isHorizontal) {
                    grid.classList.remove('w-10', 'h-10');
                    grid.classList.add('w-10', 'h-10');
                }
                for (let i = 1; i <= 9; i++) {
                    const dotWrapper = document.createElement('div');
                    dotWrapper.classList.add('flex', 'justify-center', 'items-center');
                    if (layout.includes(i)) {
                        const dot = document.createElement('span');
                        dot.classList.add('dot');
                        dotWrapper.appendChild(dot);
                    }
                    grid.appendChild(dotWrapper);
                }
                container.appendChild(grid);
            };

            const partA = document.createElement('div');
            partA.classList.add('dot-container');
            createDots(domino.a, partA);
            
            const divider = document.createElement('div');
            divider.classList.add('domino-divider');
            if (isHorizontal) {
                divider.classList.add('horizontal');
            }

            const partB = document.createElement('div');
            partB.classList.add('dot-container');
            createDots(domino.b, partB);

            tile.appendChild(partA);
            tile.appendChild(divider);
            tile.appendChild(partB);

            return tile;
        }

        // Função para atualizar a visualização da mão do jogador
        function updatePlayerHand() {
            Game.playerHandElement.innerHTML = '';
            Game.playerHand.forEach((domino, index) => {
                const tile = renderDomino(domino);
                tile.addEventListener('click', () => handlePlayerMove(domino, index));
                Game.playerHandElement.appendChild(tile);
            });
        }
        
        // Função para atualizar a visualização da mão da IA
        function updateAiHand() {
            Game.aiHandElement.innerHTML = '';
            // Renderiza apenas o verso das peças da IA
            for (let i = 0; i < Game.aiHand.length; i++) {
                const tile = document.createElement('div');
                tile.classList.add('domino-tile', 'ai-hand-tile');
                Game.aiHandElement.appendChild(tile);
            }
            Game.aiHandSizeElement.textContent = Game.aiHand.length;
        }

        // Função para renderizar o tabuleiro
        function updateBoard() {
            Game.boardElement.innerHTML = '';
            if (Game.board.length === 0) {
                Game.boardElement.innerHTML = `<p id="board-placeholder" class="text-gray-500">O tabuleiro está vazio. Comece o jogo!</p>`;
                return;
            }
            // Adiciona um espaço flexível para centralizar a linha
            const flexContainer = document.createElement('div');
            flexContainer.classList.add('flex', 'justify-center', 'flex-wrap');
            
            Game.board.forEach((domino, index) => {
                const isHorizontal = domino.a === domino.b;
                const tile = renderDomino(domino, isHorizontal, false); // IA não é um jogador
                tile.classList.remove('player-hand-tile');
                tile.classList.add('board-tile');
                // Manipular rotação para o layout da peça no tabuleiro
                if (domino.rotated) {
                    if (isHorizontal) {
                        tile.style.transform = 'rotate(90deg)';
                    } else {
                        tile.style.transform = 'rotate(90deg)';
                    }
                }
                flexContainer.appendChild(tile);
            });
            Game.boardElement.appendChild(flexContainer);
        }

        // Função para pegar uma peça do baralho
        function drawFromDeck() {
            if (Game.deck.length > 0) {
                return Game.deck.pop();
            }
            return null;
        }

        // Verifica se um dominó pode ser jogado
        function isValidMove(domino) {
            if (Game.board.length === 0) {
                return true;
            }
            const endA = Game.board[0].a;
            const endB = Game.board[Game.board.length - 1].b;
            return domino.a === endA || domino.a === endB || domino.b === endA || domino.b === endB;
        }

        // Encontra todos os movimentos válidos para uma mão
        function findValidMoves(hand) {
            if (Game.board.length === 0) {
                return hand.map((domino, index) => ({ domino, index }));
            }
            const endA = Game.board[0].a;
            const endB = Game.board[Game.board.length - 1].b;
            const validMoves = [];
            hand.forEach((domino, index) => {
                if (domino.a === endA || domino.b === endA || domino.a === endB || domino.b === endB) {
                    validMoves.push({ domino, index });
                }
            });
            return validMoves;
        }

        // Função para colocar uma peça no tabuleiro
        function placeDomino(domino, playerIsAI) {
            let placed = false;
            if (Game.board.length === 0) {
                // Primeira peça do jogo
                Game.board.push(domino);
                placed = true;
            } else {
                const endA = Game.board[0].a;
                const endB = Game.board[Game.board.length - 1].b;
                
                // Tenta colocar à esquerda
                if (domino.a === endA) {
                    Game.board.unshift({ a: domino.b, b: domino.a });
                    placed = true;
                } else if (domino.b === endA) {
                    Game.board.unshift(domino);
                    placed = true;
                }
                // Tenta colocar à direita
                else if (domino.a === endB) {
                    Game.board.push(domino);
                    placed = true;
                } else if (domino.b === endB) {
                    Game.board.push({ a: domino.b, b: domino.a });
                    placed = true;
                }
            }
            
            if (placed) {
                if (playerIsAI) {
                    const index = Game.aiHand.findIndex(d => d.a === domino.a && d.b === domino.b);
                    if (index !== -1) Game.aiHand.splice(index, 1);
                } else {
                    const index = Game.playerHand.findIndex(d => d.a === domino.a && d.b === domino.b);
                    if (index !== -1) Game.playerHand.splice(index, 1);
                }
            }

            // Atualiza a visualização
            updateBoard();
            updatePlayerHand();
            updateAiHand();
            Game.deckSizeElement.textContent = Game.deck.length;
            checkGameOver();
        }

        // --- Lógica da IA para diferentes dificuldades ---

        // AI Fácil: Joga a primeira peça válida que encontra
        function aiEasyMove() {
            const validMoves = findValidMoves(Game.aiHand);
            if (validMoves.length > 0) {
                const move = validMoves[0];
                placeDomino(move.domino, true);
                Game.turn = 'player';
                updateTurnDisplay();
            } else {
                // Se não houver movimentos, pega uma peça do baralho
                const newDomino = drawFromDeck();
                if (newDomino) {
                    Game.aiHand.push(newDomino);
                    updateAiHand();
                    // Se a nova peça for jogável, joga. Caso contrário, passa.
                    if (isValidMove(newDomino)) {
                        aiEasyMove();
                    } else {
                        Game.turn = 'player';
                        updateTurnDisplay();
                    }
                } else {
                    // Baralho vazio, passa a vez
                    Game.turn = 'player';
                    updateTurnDisplay();
                }
            }
        }

        // AI Médio: Prioriza peças de maior valor total
        function aiMediumMove() {
            const validMoves = findValidMoves(Game.aiHand);
            if (validMoves.length > 0) {
                // Encontra a peça com o maior valor total (a + b)
                const bestMove = validMoves.reduce((best, current) => {
                    return (current.domino.a + current.domino.b) > (best.domino.a + best.domino.b) ? current : best;
                });
                placeDomino(bestMove.domino, true);
                Game.turn = 'player';
                updateTurnDisplay();
            } else {
                // Não tem movimentos, pega do baralho
                const newDomino = drawFromDeck();
                if (newDomino) {
                    Game.aiHand.push(newDomino);
                    updateAiHand();
                    if (isValidMove(newDomino)) {
                        aiMediumMove();
                    } else {
                        Game.turn = 'player';
                        updateTurnDisplay();
                    }
                } else {
                    Game.turn = 'player';
                   
