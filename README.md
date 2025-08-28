
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Batalha Naval</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        primary: '#5D5CDE'
                    }
                }
            }
        }
    </script>
</head>
<body class="bg-white dark:bg-gray-900 min-h-screen transition-colors duration-300">
    <div class="container mx-auto p-4 max-w-7xl">
        <!-- Header -->
        <div class="text-center mb-6">
            <h1 class="text-4xl font-bold text-gray-800 dark:text-white mb-2">⚓ Batalha Naval</h1>
            <div id="gameStatus" class="text-xl font-semibold text-primary">Posicione seus navios</div>
        </div>

        <!-- Game Phase Indicator -->
        <div class="text-center mb-6">
            <div id="phaseIndicator" class="inline-flex items-center space-x-2 bg-gray-100 dark:bg-gray-800 px-4 py-2 rounded-lg">
                <span id="currentPhase" class="text-sm font-medium text-gray-600 dark:text-gray-300">Fase de Posicionamento</span>
            </div>
        </div>

        <!-- Ship Placement Controls -->
        <div id="shipPlacement" class="text-center mb-6 bg-gray-50 dark:bg-gray-800 p-4 rounded-lg">
            <div class="mb-4">
                <h3 class="text-lg font-semibold text-gray-700 dark:text-gray-200 mb-2">Posicione seus navios:</h3>
                <div id="shipList" class="flex flex-wrap justify-center gap-2">
                    <!-- Ships will be populated by JavaScript -->
                </div>
            </div>
            <div class="flex justify-center space-x-4">
                <button id="rotateBtn" class="px-4 py-2 bg-primary text-white rounded-lg hover:bg-purple-600 transition-colors">
                    🔄 Girar
                </button>
                <button id="randomBtn" class="px-4 py-2 bg-green-500 text-white rounded-lg hover:bg-green-600 transition-colors">
                    🎲 Posição Aleatória
                </button>
                <button id="startGameBtn" class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors disabled:opacity-50" disabled>
                    ▶️ Iniciar Jogo
                </button>
            </div>
        </div>

        <!-- Game Boards -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-8 mb-6">
            <!-- Player Board -->
            <div class="text-center">
                <h2 class="text-2xl font-bold text-gray-700 dark:text-gray-200 mb-4">⚓ Seus Navios</h2>
                <div class="inline-block bg-gray-100 dark:bg-gray-800 p-4 rounded-lg">
                    <div id="playerBoard" class="grid grid-cols-10 gap-1 mb-2">
                        <!-- Grid will be populated by JavaScript -->
                    </div>
                    <div class="text-sm text-gray-600 dark:text-gray-400">A B C D E F G H I J</div>
                </div>
            </div>

            <!-- Enemy Board -->
            <div class="text-center">
                <h2 class="text-2xl font-bold text-gray-700 dark:text-gray-200 mb-4">🎯 Área de Ataque</h2>
                <div class="inline-block bg-gray-100 dark:bg-gray-800 p-4 rounded-lg">
                    <div id="enemyBoard" class="grid grid-cols-10 gap-1 mb-2">
                        <!-- Grid will be populated by JavaScript -->
                    </div>
                    <div class="text-sm text-gray-600 dark:text-gray-400">A B C D E F G H I J</div>
                </div>
            </div>
        </div>

        <!-- Game Controls -->
        <div id="gameControls" class="text-center space-x-4 hidden">
            <button id="newGameBtn" class="px-6 py-2 bg-primary text-white rounded-lg hover:bg-purple-600 transition-colors">
                🆕 Novo Jogo
            </button>
        </div>

        <!-- Stats -->
        <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mt-6">
            <div class="bg-gray-50 dark:bg-gray-800 p-4 rounded-lg text-center">
                <div class="text-2xl font-bold text-blue-600" id="playerHits">0</div>
                <div class="text-sm text-gray-600 dark:text-gray-400">Seus Acertos</div>
            </div>
            <div class="bg-gray-50 dark:bg-gray-800 p-4 rounded-lg text-center">
                <div class="text-2xl font-bold text-red-600" id="playerMisses">0</div>
                <div class="text-sm text-gray-600 dark:text-gray-400">Seus Erros</div>
            </div>
            <div class="bg-gray-50 dark:bg-gray-800 p-4 rounded-lg text-center">
                <div class="text-2xl font-bold text-green-600" id="playerShipsLeft">5</div>
                <div class="text-sm text-gray-600 dark:text-gray-400">Seus Navios</div>
            </div>
            <div class="bg-gray-50 dark:bg-gray-800 p-4 rounded-lg text-center">
                <div class="text-2xl font-bold text-orange-600" id="enemyShipsLeft">5</div>
                <div class="text-sm text-gray-600 dark:text-gray-400">Navios Inimigos</div>
            </div>
        </div>
    </div>

    <!-- Victory Modal -->
    <div id="victoryModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 hidden">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-lg shadow-lg max-w-md w-full mx-4 text-center">
            <div id="victoryMessage" class="text-2xl font-bold mb-4"></div>
            <div id="victoryStats" class="text-gray-600 dark:text-gray-400 mb-6"></div>
            <button id="playAgainBtn" class="px-6 py-2 bg-primary text-white rounded-lg hover:bg-purple-600 transition-colors">
                🔄 Jogar Novamente
            </button>
        </div>
    </div>

    <script>
        // Dark mode setup
        if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
            document.documentElement.classList.add('dark');
        }
        window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', event => {
            if (event.matches) {
                document.documentElement.classList.add('dark');
            } else {
                document.documentElement.classList.remove('dark');
            }
        });

        class BattleshipGame {
            constructor() {
                this.ships = [
                    { name: 'Porta-aviões', size: 5, placed: false },
                    { name: 'Encouraçado', size: 4, placed: false },
                    { name: 'Cruzador', size: 3, placed: false },
                    { name: 'Submarino', size: 3, placed: false },
                    { name: 'Destroyer', size: 2, placed: false }
                ];
                
                this.playerBoard = Array(10).fill().map(() => Array(10).fill(0));
                this.enemyBoard = Array(10).fill().map(() => Array(10).fill(0));
                this.playerShots = Array(10).fill().map(() => Array(10).fill(0));
                this.enemyShots = Array(10).fill().map(() => Array(10).fill(0));
                
                this.currentShip = 0;
                this.shipOrientation = 'horizontal'; // horizontal or vertical
                this.gamePhase = 'placement'; // placement, playing, ended
                this.playerTurn = true;
                this.playerHits = 0;
                this.playerMisses = 0;
                this.playerShipsLeft = 5;
                this.enemyShipsLeft = 5;
                
                this.init();
            }

            init() {
                this.createBoards();
                this.createShipList();
                this.setupEventListeners();
                this.placeEnemyShips();
                this.updateDisplay();
            }

            createBoards() {
                const playerBoard = document.getElementById('playerBoard');
                const enemyBoard = document.getElementById('enemyBoard');
                
                playerBoard.innerHTML = '';
                enemyBoard.innerHTML = '';
                
                for (let i = 0; i < 100; i++) {
                    const row = Math.floor(i / 10);
                    const col = i % 10;
                    
                    // Player board cell
                    const playerCell = document.createElement('div');
                    playerCell.className = 'w-8 h-8 border border-gray-300 dark:border-gray-600 bg-blue-100 dark:bg-blue-900 cursor-pointer hover:bg-blue-200 dark:hover:bg-blue-800 transition-colors text-xs flex items-center justify-center font-bold';
                    playerCell.dataset.row = row;
                    playerCell.dataset.col = col;
                    playerCell.dataset.board = 'player';
                    playerCell.addEventListener('click', this.handleCellClick.bind(this));
                    playerCell.addEventListener('mouseenter', this.handleCellHover.bind(this));
                    playerCell.addEventListener('mouseleave', this.handleCellLeave.bind(this));
                    playerBoard.appendChild(playerCell);
                    
                    // Enemy board cell
                    const enemyCell = document.createElement('div');
                    enemyCell.className = 'w-8 h-8 border border-gray-300 dark:border-gray-600 bg-gray-200 dark:bg-gray-700 cursor-pointer hover:bg-gray-300 dark:hover:bg-gray-600 transition-colors text-xs flex items-center justify-center font-bold';
                    enemyCell.dataset.row = row;
                    enemyCell.dataset.col = col;
                    enemyCell.dataset.board = 'enemy';
                    enemyCell.addEventListener('click', this.handleCellClick.bind(this));
                    enemyBoard.appendChild(enemyCell);
                }
            }

            createShipList() {
                const shipList = document.getElementById('shipList');
                shipList.innerHTML = '';
                
                this.ships.forEach((ship, index) => {
                    const shipElement = document.createElement('div');
                    shipElement.className = `px-3 py-2 rounded-lg cursor-pointer transition-colors ${
                        index === this.currentShip && !ship.placed 
                            ? 'bg-primary text-white' 
                            : ship.placed 
                                ? 'bg-green-500 text-white' 
                                : 'bg-gray-200 dark:bg-gray-700 text-gray-700 dark:text-gray-300'
                    }`;
                    shipElement.textContent = `${ship.name} (${ship.size})`;
                    shipElement.addEventListener('click', () => {
                        if (!ship.placed) {
                            this.currentShip = index;
                            this.createShipList();
                        }
                    });
                    shipList.appendChild(shipElement);
                });
            }

            setupEventListeners() {
                document.getElementById('rotateBtn').addEventListener('click', () => {
                    this.shipOrientation = this.shipOrientation === 'horizontal' ? 'vertical' : 'horizontal';
                });
                
                document.getElementById('randomBtn').addEventListener('click', () => {
                    this.placeShipsRandomly();
                });
                
                document.getElementById('startGameBtn').addEventListener('click', () => {
                    this.startGame();
                });
                
                document.getElementById('newGameBtn').addEventListener('click', () => {
                    this.resetGame();
                });
                
                document.getElementById('playAgainBtn').addEventListener('click', () => {
                    this.hideVictoryModal();
                    this.resetGame();
                });
            }

            handleCellClick(event) {
                const row = parseInt(event.target.dataset.row);
                const col = parseInt(event.target.dataset.col);
                const board = event.target.dataset.board;
                
                if (this.gamePhase === 'placement' && board === 'player') {
                    this.placeShip(row, col);
                } else if (this.gamePhase === 'playing' && board === 'enemy' && this.playerTurn) {
                    this.playerAttack(row, col);
                }
            }

            handleCellHover(event) {
                if (this.gamePhase !== 'placement') return;
                
                const row = parseInt(event.target.dataset.row);
                const col = parseInt(event.target.dataset.col);
                
                if (this.currentShip < this.ships.length && !this.ships[this.currentShip].placed) {
                    this.previewShipPlacement(row, col, true);
                }
            }

            handleCellLeave(event) {
                if (this.gamePhase !== 'placement') return;
                
                const row = parseInt(event.target.dataset.row);
                const col = parseInt(event.target.dataset.col);
                
                if (this.currentShip < this.ships.length && !this.ships[this.currentShip].placed) {
                    this.previewShipPlacement(row, col, false);
                }
            }

            previewShipPlacement(row, col, show) {
                const ship = this.ships[this.currentShip];
                if (!ship) return;
                
                const positions = this.getShipPositions(row, col, ship.size, this.shipOrientation);
                if (!positions) return;
                
                positions.forEach(([r, c]) => {
                    const cell = document.querySelector(`[data-row="${r}"][data-col="${c}"][data-board="player"]`);
                    if (cell) {
                        if (show) {
                            cell.classList.add('bg-yellow-300', 'dark:bg-yellow-600');
                        } else {
                            cell.classList.remove('bg-yellow-300', 'dark:bg-yellow-600');
                        }
                    }
                });
            }

            getShipPositions(row, col, size, orientation) {
                const positions = [];
                
                for (let i = 0; i < size; i++) {
                    const r = orientation === 'horizontal' ? row : row + i;
                    const c = orientation === 'horizontal' ? col + i : col;
                    
                    if (r >= 10 || c >= 10 || r < 0 || c < 0) {
                        return null; // Ship doesn't fit
                    }
                    
                    positions.push([r, c]);
                }
                
                return positions;
            }

            canPlaceShip(row, col, size, orientation) {
                const positions = this.getShipPositions(row, col, size, orientation);
                if (!positions) return false;
                
                // Check if positions are free and have space around them
                for (let [r, c] of positions) {
                    if (this.playerBoard[r][c] !== 0) return false;
                    
                    // Check surrounding cells
                    for (let dr = -1; dr <= 1; dr++) {
                        for (let dc = -1; dc <= 1; dc++) {
                            const nr = r + dr;
                            const nc = c + dc;
                            if (nr >= 0 && nr < 10 && nc >= 0 && nc < 10) {
                                if (this.playerBoard[nr][nc] !== 0) return false;
                            }
                        }
                    }
                }
                
                return true;
            }

            placeShip(row, col) {
                if (this.currentShip >= this.ships.length) return;
                
                const ship = this.ships[this.currentShip];
                if (ship.placed) return;
                
                if (!this.canPlaceShip(row, col, ship.size, this.shipOrientation)) {
                    return;
                }
                
                const positions = this.getShipPositions(row, col, ship.size, this.shipOrientation);
                positions.forEach(([r, c]) => {
                    this.playerBoard[r][c] = this.currentShip + 1;
                });
                
                ship.placed = true;
                this.currentShip++;
                
                this.updatePlayerBoard();
                this.createShipList();
                this.checkAllShipsPlaced();
            }

            placeShipsRandomly() {
                // Reset all ships
                this.ships.forEach(ship => ship.placed = false);
                this.playerBoard = Array(10).fill().map(() => Array(10).fill(0));
                this.currentShip = 0;
                
                this.ships.forEach((ship, index) => {
                    let placed = false;
                    let attempts = 0;
                    
                    while (!placed && attempts < 100) {
                        const row = Math.floor(Math.random() * 10);
                        const col = Math.floor(Math.random() * 10);
                        const orientation = Math.random() < 0.5 ? 'horizontal' : 'vertical';
                        
                        if (this.canPlaceShip(row, col, ship.size, orientation)) {
                            const positions = this.getShipPositions(row, col, ship.size, orientation);
                            positions.forEach(([r, c]) => {
                                this.playerBoard[r][c] = index + 1;
                            });
                            ship.placed = true;
                            placed = true;
                        }
                        attempts++;
                    }
                });
                
                this.currentShip = this.ships.length;
                this.updatePlayerBoard();
                this.createShipList();
                this.checkAllShipsPlaced();
            }

            placeEnemyShips() {
                this.ships.forEach((ship, index) => {
                    let placed = false;
                    let attempts = 0;
                    
                    while (!placed && attempts < 100) {
                        const row = Math.floor(Math.random() * 10);
                        const col = Math.floor(Math.random() * 10);
                        const orientation = Math.random() < 0.5 ? 'horizontal' : 'vertical';
                        
                        if (this.canPlaceEnemyShip(row, col, ship.size, orientation)) {
                            const positions = this.getShipPositions(row, col, ship.size, orientation);
                            positions.forEach(([r, c]) => {
                                this.enemyBoard[r][c] = index + 1;
                            });
                            placed = true;
                        }
                        attempts++;
                    }
                });
            }

            canPlaceEnemyShip(row, col, size, orientation) {
                const positions = this.getShipPositions(row, col, size, orientation);
                if (!positions) return false;
                
                for (let [r, c] of positions) {
                    if (this.enemyBoard[r][c] !== 0) return false;
                    
                    for (let dr = -1; dr <= 1; dr++) {
                        for (let dc = -1; dc <= 1; dc++) {
                            const nr = r + dr;
                            const nc = c + dc;
                            if (nr >= 0 && nr < 10 && nc >= 0 && nc < 10) {
                                if (this.enemyBoard[nr][nc] !== 0) return false;
                            }
                        }
                    }
                }
                
                return true;
            }

            checkAllShipsPlaced() {
                const allPlaced = this.ships.every(ship => ship.placed);
                document.getElementById('startGameBtn').disabled = !allPlaced;
                
                if (allPlaced) {
                    document.getElementById('startGameBtn').classList.remove('opacity-50');
                } else {
                    document.getElementById('startGameBtn').classList.add('opacity-50');
                }
            }

            startGame() {
                this.gamePhase = 'playing';
                document.getElementById('shipPlacement').classList.add('hidden');
                document.getElementById('gameControls').classList.remove('hidden');
                document.getElementById('currentPhase').textContent = 'Batalha em Andamento';
                this.updateDisplay();
            }

            playerAttack(row, col) {
                if (this.playerShots[row][col] !== 0) return; // Already shot
                
                this.playerShots[row][col] = this.enemyBoard[row][col] === 0 ? 1 : 2; // 1 = miss, 2 = hit
                
                if (this.enemyBoard[row][col] !== 0) {
                    this.playerHits++;
                    
                    // Check if ship is sunk
                    if (this.isShipSunk('enemy', this.enemyBoard[row][col])) {
                        this.enemyShipsLeft--;
                        this.markSunkShip('enemy', this.enemyBoard[row][col]);
                    }
                } else {
                    this.playerMisses++;
                    this.playerTurn = false;
                    setTimeout(() => this.enemyAttack(), 1000);
                }
                
                this.updateEnemyBoard();
                this.updateDisplay();
                
                if (this.enemyShipsLeft === 0) {
                    this.endGame('player');
                }
            }

            enemyAttack() {
                let row, col;
                let attempts = 0;
                
                do {
                    row = Math.floor(Math.random() * 10);
                    col = Math.floor(Math.random() * 10);
                    attempts++;
                } while (this.enemyShots[row][col] !== 0 && attempts < 100);
                
                if (attempts >= 100) return; // Safety check
                
                this.enemyShots[row][col] = this.playerBoard[row][col] === 0 ? 1 : 2;
                
                if (this.playerBoard[row][col] !== 0) {
                    if (this.isShipSunk('player', this.playerBoard[row][col])) {
                        this.playerShipsLeft--;
                        this.markSunkShip('player', this.playerBoard[row][col]);
                    }
                    setTimeout(() => this.enemyAttack(), 500); // Enemy continues if hit
                } else {
                    this.playerTurn = true;
                }
                
                this.updatePlayerBoard();
                this.updateDisplay();
                
                if (this.playerShipsLeft === 0) {
                    this.endGame('enemy');
                }
            }

            isShipSunk(board, shipId) {
                const boardArray = board === 'player' ? this.playerBoard : this.enemyBoard;
                const shotsArray = board === 'player' ? this.enemyShots : this.playerShots;
                
                for (let r = 0; r < 10; r++) {
                    for (let c = 0; c < 10; c++) {
                        if (boardArray[r][c] === shipId && shotsArray[r][c] !== 2) {
                            return false;
                        }
                    }
                }
                return true;
            }

            markSunkShip(board, shipId) {
                const boardArray = board === 'player' ? this.playerBoard : this.enemyBoard;
                const shotsArray = board === 'player' ? this.enemyShots : this.playerShots;
                
                for (let r = 0; r < 10; r++) {
                    for (let c = 0; c < 10; c++) {
                        if (boardArray[r][c] === shipId) {
                            shotsArray[r][c] = 3; // Mark as sunk
                        }
                    }
                }
            }

            updatePlayerBoard() {
                const cells = document.querySelectorAll('[data-board="player"]');
                cells.forEach((cell, index) => {
                    const row = Math.floor(index / 10);
                    const col = index % 10;
                    
                    cell.className = 'w-8 h-8 border border-gray-300 dark:border-gray-600 text-xs flex items-center justify-center font-bold transition-colors';
                    
                    if (this.gamePhase === 'placement') {
                        cell.classList.add('cursor-pointer', 'hover:bg-blue-200', 'dark:hover:bg-blue-800');
                    }
                    
                    if (this.playerBoard[row][col] !== 0) {
                        cell.classList.add('bg-green-400', 'dark:bg-green-600');
                        cell.textContent = '🚢';
                    } else {
                        cell.classList.add('bg-blue-100', 'dark:bg-blue-900');
                        cell.textContent = '';
                    }
                    
                    // Show enemy shots
                    if (this.enemyShots[row][col] === 1) { // Miss
                        cell.classList.remove('bg-blue-100', 'dark:bg-blue-900');
                        cell.classList.add('bg-gray-400', 'dark:bg-gray-600');
                        cell.textContent = '💧';
                    } else if (this.enemyShots[row][col] === 2) { // Hit
                        cell.classList.remove('bg-green-400', 'dark:bg-green-600');
                        cell.classList.add('bg-red-500', 'dark:bg-red-600');
                        cell.textContent = '💥';
                    } else if (this.enemyShots[row][col] === 3) { // Sunk
                        cell.classList.remove('bg-green-400', 'dark:bg-green-600');
                        cell.classList.add('bg-red-700', 'dark:bg-red-800');
                        cell.textContent = '☠️';
                    }
                });
            }

            updateEnemyBoard() {
                const cells = document.querySelectorAll('[data-board="enemy"]');
                cells.forEach((cell, index) => {
                    const row = Math.floor(index / 10);
                    const col = index % 10;
                    
                    cell.className = 'w-8 h-8 border border-gray-300 dark:border-gray-600 text-xs flex items-center justify-center font-bold transition-colors';
                    
                    if (this.gamePhase === 'playing' && this.playerTurn && this.playerShots[row][col] === 0) {
                        cell.classList.add('cursor-pointer', 'hover:bg-gray-300', 'dark:hover:bg-gray-600');
                    }
                    
                    if (this.playerShots[row][col] === 0) {
                        cell.classList.add('bg-gray-200', 'dark:bg-gray-700');
                        cell.textContent = '';
                    } else if (this.playerShots[row][col] === 1) { // Miss
                        cell.classList.add('bg-blue-300', 'dark:bg-blue-600');
                        cell.textContent = '💧';
                    } else if (this.playerShots[row][col] === 2) { // Hit
                        cell.classList.add('bg-red-500', 'dark:bg-red-600');
                        cell.textContent = '💥';
                    } else if (this.playerShots[row][col] === 3) { // Sunk
                        cell.classList.add('bg-red-700', 'dark:bg-red-800');
                        cell.textContent = '☠️';
                    }
                });
            }

            updateDisplay() {
                const status = document.getElementById('gameStatus');
                
                if (this.gamePhase === 'placement') {
                    if (this.currentShip < this.ships.length) {
                        status.textContent = `Posicione: ${this.ships[this.currentShip].name} (${this.ships[this.currentShip].size} casas)`;
                    } else {
                        status.textContent = 'Todos os navios posicionados! Clique em "Iniciar Jogo"';
                    }
                } else if (this.gamePhase === 'playing') {
                    status.textContent = this.playerTurn ? 'Sua vez - Clique no tabuleiro inimigo!' : 'Vez do inimigo...';
                }
                
                document.getElementById('playerHits').textContent = this.playerHits;
                document.getElementById('playerMisses').textContent = this.playerMisses;
                document.getElementById('playerShipsLeft').textContent = this.playerShipsLeft;
                document.getElementById('enemyShipsLeft').textContent = this.enemyShipsLeft;
            }

            endGame(winner) {
                this.gamePhase = 'ended';
                const modal = document.getElementById('victoryModal');
                const message = document.getElementById('victoryMessage');
                const stats = document.getElementById('victoryStats');
                
                if (winner === 'player') {
                    message.textContent = '🎉 Vitória! 🎉';
                    message.className = 'text-2xl font-bold mb-4 text-green-600';
                } else {
                    message.textContent = '💀 Derrota! 💀';
                    message.className = 'text-2xl font-bold mb-4 text-red-600';
                }
                
                const accuracy = this.playerHits + this.playerMisses > 0 ? Math.round((this.playerHits / (this.playerHits + this.playerMisses)) * 100) : 0;
                stats.innerHTML = `
                    <p>Acertos: ${this.playerHits}</p>
                    <p>Erros: ${this.playerMisses}</p>
                    <p>Precisão: ${accuracy}%</p>
                `;
                
                modal.classList.remove('hidden');
            }

            hideVictoryModal() {
                document.getElementById('victoryModal').classList.add('hidden');
            }

            resetGame() {
                this.ships.forEach(ship => ship.placed = false);
                this.playerBoard = Array(10).fill().map(() => Array(10).fill(0));
                this.enemyBoard = Array(10).fill().map(() => Array(10).fill(0));
                this.playerShots = Array(10).fill().map(() => Array(10).fill(0));
                this.enemyShots = Array(10).fill().map(() => Array(10).fill(0));
                
                this.currentShip = 0;
                this.gamePhase = 'placement';
                this.playerTurn = true;
                this.playerHits = 0;
                this.playerMisses = 0;
                this.playerShipsLeft = 5;
                this.enemyShipsLeft = 5;
                
                document.getElementById('shipPlacement').classList.remove('hidden');
                document.getElementById('gameControls').classList.add('hidden');
                document.getElementById('currentPhase').textContent = 'Fase de Posicionamento';
                
                this.createBoards();
                this.createShipList();
                this.placeEnemyShips();
                this.updateDisplay();
            }
        }

        // Initialize game when page loads
        window.addEventListener('DOMContentLoaded', () => {
            new BattleshipGame();
        });
    </script>
</body>
</html>
