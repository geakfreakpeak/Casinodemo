<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Golden Slots - Однорукий бандит</title>
    <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>🎰</text></svg>">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        :root {
            --primary-gold: #ffd700;
            --secondary-gold: #ffed4e;
            --dark-bg: #1a1a2e;
            --darker-bg: #16213e;
            --win-color: #4ecdc4;
            --jackpot-color: #ff6b6b;
            --slot-bg: linear-gradient(145deg, #ffffff, #f0f0f0);
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, var(--dark-bg), var(--darker-bg));
            color: white;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
            overflow-x: hidden;
        }

        .container {
            max-width: 1200px;
            width: 100%;
            text-align: center;
        }

        /* Header Styles */
        .header {
            margin-bottom: 30px;
            position: relative;
        }

        .title {
            font-size: 3.5em;
            background: linear-gradient(45deg, var(--primary-gold), var(--secondary-gold));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            text-shadow: 0 0 30px rgba(255, 215, 0, 0.3);
            margin-bottom: 10px;
            animation: titleGlow 3s ease-in-out infinite alternate;
        }

        .wallet {
            background: rgba(255, 255, 255, 0.1);
            padding: 20px 40px;
            border-radius: 25px;
            font-size: 1.8em;
            font-weight: bold;
            margin: 20px 0;
            display: inline-block;
            backdrop-filter: blur(10px);
            border: 2px solid var(--primary-gold);
            box-shadow: 0 0 20px rgba(255, 215, 0, 0.2);
            transition: all 0.3s ease;
        }

        .wallet:hover {
            transform: translateY(-2px);
            box-shadow: 0 0 30px rgba(255, 215, 0, 0.3);
        }

        /* Slot Machine Styles */
        .slot-machine {
            background: rgba(0, 0, 0, 0.4);
            border-radius: 25px;
            padding: 40px;
            margin: 20px 0;
            border: 3px solid var(--primary-gold);
            box-shadow: 
                0 10px 30px rgba(0, 0, 0, 0.5),
                inset 0 0 50px rgba(255, 215, 0, 0.1);
            position: relative;
            overflow: hidden;
        }

        .slot-machine::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(45deg, transparent, rgba(255, 215, 0, 0.1), transparent);
            transform: rotate(45deg);
            animation: shine 6s ease-in-out infinite;
        }

        .slots-container {
            perspective: 1000px;
            margin: 40px 0;
        }

        .slots {
            display: flex;
            justify-content: center;
            gap: 20px;
            transform-style: preserve-3d;
        }

        .slot-column {
            position: relative;
            width: 140px;
            height: 420px;
            background: rgba(0, 0, 0, 0.6);
            border-radius: 15px;
            border: 3px solid var(--primary-gold);
            overflow: hidden;
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.6);
        }

        .slot-reel {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            display: flex;
            flex-direction: column;
            transition: transform 0.1s linear;
        }

        .slot-item {
            width: 100%;
            height: 140px;
            background: var(--slot-bg);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 4em;
            border-bottom: 2px solid #ddd;
            box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.1);
        }

        .slot-item:last-child {
            border-bottom: none;
        }

        .win-line {
            position: absolute;
            left: 0;
            width: 100%;
            height: 4px;
            background: var(--win-color);
            box-shadow: 0 0 20px var(--win-color);
            z-index: 10;
            opacity: 0;
        }

        .win-line.top { top: 33%; }
        .win-line.middle { top: 50%; }
        .win-line.bottom { top: 67%; }

        /* Controls */
        .controls {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin: 30px 0;
            flex-wrap: wrap;
        }

        .btn {
            background: linear-gradient(45deg, #ff6b6b, var(--primary-gold));
            border: none;
            padding: 20px 40px;
            font-size: 1.3em;
            font-weight: bold;
            color: white;
            border-radius: 50px;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 5px 15px rgba(255, 107, 107, 0.4);
            position: relative;
            overflow: hidden;
        }

        .btn::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.2), transparent);
            transition: left 0.5s;
        }

        .btn:hover::before {
            left: 100%;
        }

        .btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 25px rgba(255, 107, 107, 0.6);
        }

        .btn:active {
            transform: translateY(-1px);
        }

        .btn:disabled {
            background: #666;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }

        .btn:disabled::before {
            display: none;
        }

        .auto-spin-btn {
            background: linear-gradient(45deg, #4ecdc4, #44a08d);
        }

        /* Results and Messages */
        .result {
            margin: 25px 0;
            font-size: 1.4em;
            min-height: 40px;
            font-weight: bold;
            position: relative;
        }

        .win {
            color: var(--win-color);
            text-shadow: 0 0 10px rgba(78, 205, 196, 0.5);
            animation: textPulse 2s ease-in-out infinite;
        }

        .jackpot {
            color: var(--jackpot-color);
            text-shadow: 0 0 15px rgba(255, 107, 107, 0.7);
            animation: jackpotText 1s ease-in-out infinite;
        }

        /* Symbols Grid */
        .symbols {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 15px;
            margin: 25px 0;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
        }

        .symbol {
            display: flex;
            flex-direction: column;
            align-items: center;
            background: rgba(255, 255, 255, 0.1);
            padding: 20px;
            border-radius: 15px;
            backdrop-filter: blur(5px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            transition: all 0.3s ease;
        }

        .symbol:hover {
            transform: translateY(-5px);
            background: rgba(255, 255, 255, 0.15);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
        }

        .symbol-icon {
            font-size: 2.5em;
            margin-bottom: 10px;
            filter: drop-shadow(0 0 5px rgba(255, 255, 255, 0.3));
        }

        .symbol-text {
            font-size: 0.9em;
            color: #ccc;
            font-weight: bold;
        }

        /* Payouts */
        .payouts {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 15px;
            margin: 30px 0;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
        }

        .payout {
            background: rgba(255, 215, 0, 0.15);
            padding: 20px;
            border-radius: 15px;
            border: 2px solid var(--primary-gold);
            backdrop-filter: blur(5px);
            transition: all 0.3s ease;
        }

        .payout:hover {
            transform: scale(1.05);
            background: rgba(255, 215, 0, 0.25);
        }

        /* Rules */
        .rules {
            background: rgba(255, 255, 255, 0.1);
            padding: 30px;
            border-radius: 20px;
            margin-top: 30px;
            text-align: left;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .rules h3 {
            color: var(--primary-gold);
            margin-bottom: 20px;
            text-align: center;
            font-size: 1.8em;
        }

        .rules p {
            margin: 10px 0;
            font-size: 1.1em;
            line-height: 1.5;
        }

        /* Confetti */
        .confetti {
            position: fixed;
            width: 15px;
            height: 15px;
            background: var(--primary-gold);
            opacity: 0;
            pointer-events: none;
        }

        /* Mobile Styles */
        @media (max-width: 768px) {
            body {
                padding: 10px;
            }

            .title {
                font-size: 2.5em;
            }

            .wallet {
                padding: 15px 25px;
                font-size: 1.4em;
            }

            .slot-machine {
                padding: 20px;
            }

            .slot-column {
                width: 100px;
                height: 300px;
            }

            .slot-item {
                height: 100px;
                font-size: 3em;
            }

            .controls {
                flex-direction: column;
                gap: 15px;
            }

            .btn {
                padding: 15px 30px;
                font-size: 1.1em;
                width: 100%;
                max-width: 300px;
            }

            .symbols {
                grid-template-columns: repeat(3, 1fr);
            }

            .payouts {
                grid-template-columns: 1fr;
            }

            .rules {
                padding: 20px;
            }
        }

        @media (max-width: 480px) {
            .title {
                font-size: 2em;
            }

            .slot-column {
                width: 80px;
                height: 240px;
            }

            .slot-item {
                height: 80px;
                font-size: 2.5em;
            }

            .symbols {
                grid-template-columns: repeat(2, 1fr);
            }
        }

        /* Animations */
        @keyframes titleGlow {
            0% { text-shadow: 0 0 30px rgba(255, 215, 0, 0.3); }
            100% { text-shadow: 0 0 50px rgba(255, 215, 0, 0.6), 0 0 70px rgba(255, 215, 0, 0.4); }
        }

        @keyframes shine {
            0% { transform: rotate(45deg) translateX(-100%); }
            100% { transform: rotate(45deg) translateX(100%); }
        }

        @keyframes textPulse {
            0%, 100% { opacity: 1; transform: scale(1); }
            50% { opacity: 0.8; transform: scale(1.05); }
        }

        @keyframes jackpotText {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.1); }
        }

        @keyframes slotWin {
            0% { transform: translateY(0); }
            25% { transform: translateY(-10px); }
            50% { transform: translateY(0); }
            75% { transform: translateY(-5px); }
            100% { transform: translateY(0); }
        }

        @keyframes confettiFall {
            0% { transform: translateY(-100px) rotate(0deg); opacity: 1; }
            100% { transform: translateY(1000px) rotate(360deg); opacity: 0; }
        }

        .slot-win-animation {
            animation: slotWin 0.5s ease-in-out 3;
        }

        .win-line-active {
            animation: winLinePulse 0.5s ease-in-out 3;
        }

        @keyframes winLinePulse {
            0%, 100% { opacity: 0.7; }
            50% { opacity: 1; }
        }
    </style>
</head>
<body>
    <!-- Confetti Container -->
    <div id="confetti-container"></div>

    <div class="container">
        <div class="header">
            <h1 class="title">GOLDEN SLOTS</h1>
            <div class="wallet">
                💰 Баланс: <span id="balance">1000</span> монет
            </div>
        </div>

        <div class="slot-machine">
            <div class="slots-container">
                <div class="slots">
                    <div class="slot-column" id="slot1">
                        <div class="win-line top"></div>
                        <div class="win-line middle"></div>
                        <div class="win-line bottom"></div>
                        <div class="slot-reel" id="reel1"></div>
                    </div>
                    <div class="slot-column" id="slot2">
                        <div class="win-line top"></div>
                        <div class="win-line middle"></div>
                        <div class="win-line bottom"></div>
                        <div class="slot-reel" id="reel2"></div>
                    </div>
                    <div class="slot-column" id="slot3">
                        <div class="win-line top"></div>
                        <div class="win-line middle"></div>
                        <div class="win-line bottom"></div>
                        <div class="slot-reel" id="reel3"></div>
                    </div>
                </div>
            </div>
            
            <div class="controls">
                <button class="btn spin-btn" id="spinBtn">
                    🎮 Крутить за 10 монет!
                </button>
                <button class="btn auto-spin-btn" id="autoSpinBtn">
                    ⚡ Авто-спин (x5)
                </button>
            </div>
            
            <div class="result" id="result"></div>

            <div class="payouts">
                <div class="payout">
                    <strong>🍒🍒🍒 = +1000 монет</strong>
                </div>
                <div class="payout">
                    <strong>⭐⭐⭐ = +100 монет</strong>
                </div>
                <div class="payout">
                    <strong>3 одинаковых = +50 монет</strong>
                </div>
            </div>
        </div>

        <div class="symbols">
            <div class="symbol">
                <div class="symbol-icon">🍒</div>
                <div class="symbol-text">Вишня</div>
            </div>
            <div class="symbol">
                <div class="symbol-icon">🍋</div>
                <div class="symbol-text">Лимон</div>
            </div>
            <div class="symbol">
                <div class="symbol-icon">🍊</div>
                <div class="symbol-text">Апельсин</div>
            </div>
            <div class="symbol">
                <div class="symbol-icon">⭐</div>
                <div class="symbol-text">Звезда</div>
            </div>
            <div class="symbol">
                <div class="symbol-icon">🔔</div>
                <div class="symbol-text">Колокольчик</div>
            </div>
        </div>

        <div class="rules">
            <h3>📋 Правила игры и выплаты</h3>
            <p>• 🎮 Один спин стоит <strong>10 монет</strong></p>
            <p>• 🍒 <strong>Три вишни</strong> = <strong>+1000 монет</strong> (ДЖЕКПОТ!)</p>
            <p>• ⭐ <strong>Три звезды</strong> = <strong>+100 монет</strong></p>
            <p>• 🎯 <strong>Любые другие 3 одинаковых символа</strong> = <strong>+50 монет</strong></p>
            <p>• 💰 <strong>Начальный баланс: 1,000 монет</strong></p>
            <p>• 🍀 <strong>Удачи и приятной игры!</strong></p>
        </div>
    </div>

    <script>
        class SlotMachine {
            constructor() {
                this.balance = 1000;
                this.spinCost = 10;
                this.payouts = {
                    '🍒': 1000,
                    '⭐': 100,
                    'default': 50
                };
                this.symbols = ['🍒', '🍋', '🍊', '⭐', '🔔'];
                this.isSpinning = false;
                this.autoSpinCount = 0;
                this.reels = [];
                
                this.initializeElements();
                this.setupEventListeners();
                this.initializeReels();
                this.updateBalance();
            }

            initializeElements() {
                this.balanceElement = document.getElementById('balance');
                this.reel1 = document.getElementById('reel1');
                this.reel2 = document.getElementById('reel2');
                this.reel3 = document.getElementById('reel3');
                this.spinBtn = document.getElementById('spinBtn');
                this.autoSpinBtn = document.getElementById('autoSpinBtn');
                this.resultElement = document.getElementById('result');
                this.confettiContainer = document.getElementById('confetti-container');
                
                this.reels = [this.reel1, this.reel2, this.reel3];
            }

            setupEventListeners() {
                this.spinBtn.addEventListener('click', () => this.spin());
                this.autoSpinBtn.addEventListener('click', () => this.autoSpin());
            }

            initializeReels() {
                // Заполняем барабаны символами
                this.reels.forEach(reel => {
                    for (let i = 0; i < 20; i++) {
                        const symbol = this.getRandomSymbol();
                        const slotItem = document.createElement('div');
                        slotItem.className = 'slot-item';
                        slotItem.textContent = symbol;
                        reel.appendChild(slotItem);
                    }
                });
            }

            getRandomSymbol() {
                return this.symbols[Math.floor(Math.random() * this.symbols.length)];
            }

            async spin() {
                if (this.isSpinning || this.balance < this.spinCost) {
                    if (this.balance < this.spinCost) {
                        this.showMessage('❌ Недостаточно монет!', 'error');
                    }
                    return;
                }

                this.isSpinning = true;
                this.spinBtn.disabled = true;
                this.autoSpinBtn.disabled = true;
                this.resultElement.textContent = '';
                this.hideWinLines();
                
                // Списание стоимости спина
                this.balance -= this.spinCost;
                this.updateBalance();

                // Анимация вращения барабанов
                await this.animateReels();

                // Проверяем выигрыш
                const results = this.getResults();
                this.checkWin(results);

                this.isSpinning = false;
                this.spinBtn.disabled = false;
                this.autoSpinBtn.disabled = false;

                // Если это авто-спин, продолжаем
                if (this.autoSpinCount > 0) {
                    this.autoSpinCount--;
                    if (this.autoSpinCount > 0 && this.balance >= this.spinCost) {
                        setTimeout(() => this.spin(), 1000);
                    }
                }
            }

            async animateReels() {
                const spinDuration = 2000; // 2 секунды вращения
                const startTime = Date.now();
                
                // Задаем разные скорости для каждого барабана для реалистичности
                const speeds = [0.8, 1.0, 1.2];
                const delays = [0, 100, 200]; // Задержка старта для каждого барабана

                const spinPromises = this.reels.map((reel, index) => {
                    return new Promise(resolve => {
                        setTimeout(() => {
                            const startPosition = 0;
                            let lastTime = Date.now();
                            
                            const spin = () => {
                                const currentTime = Date.now();
                                const elapsed = currentTime - startTime - delays[index];
                                const progress = Math.min(elapsed / spinDuration, 1);
                                
                                if (progress < 1) {
                                    // Ускоряем в начале и замедляем в конце
                                    const easeOut = 1 - Math.pow(1 - progress, 3);
                                    const distance = easeOut * 1000 * speeds[index];
                                    reel.style.transform = `translateY(-${distance}px)`;
                                    requestAnimationFrame(spin);
                                } else {
                                    // Финальная позиция
                                    this.setFinalPosition(reel, index);
                                    resolve();
                                }
                            };
                            
                            spin();
                        }, delays[index]);
                    });
                });

                await Promise.all(spinPromises);
            }

            setFinalPosition(reel, reelIndex) {
                // Генерируем финальные символы
                const finalSymbols = [
                    this.getRandomSymbol(),
                    this.getRandomSymbol(),
                    this.getRandomSymbol()
                ];
                
                // Обновляем символы в центре барабана
                const centerIndex = 10; // Центральный индекс
                const slotItems = reel.getElementsByClassName('slot-item');
                
                // Устанавливаем финальные символы в видимой области
                for (let i = -1; i <= 1; i++) {
                    const index = (centerIndex + i + slotItems.length) % slotItems.length;
                    slotItems[index].textContent = finalSymbols[i + 1];
                }
                
                // Позиционируем барабан чтобы центральный символ был по центру
                const itemHeight = 140;
                const centerPosition = centerIndex * itemHeight;
                reel.style.transform = `translateY(-${centerPosition}px)`;
            }

            getResults() {
                const results = [];
                this.reels.forEach(reel => {
                    const slotItems = reel.getElementsByClassName('slot-item');
                    const centerIndex = 10;
                    results.push(slotItems[centerIndex].textContent);
                });
                return results;
            }

            checkWin(results) {
                const [a, b, c] = results;
                
                if (a === b && b === c) {
                    let winAmount = this.payouts.default;
                    
                    if (a === '🍒') {
                        winAmount = this.payouts['🍒'];
                    } else if (a === '⭐') {
                        winAmount = this.payouts['⭐'];
                    }
                    
                    this.balance += winAmount;
                    this.showWinMessage(winAmount, a);
                    this.celebrateWin(winAmount);
                    this.showWinLines();
                } else {
                    this.showMessage('😞 Повезёт в следующий раз!', 'lose');
                }
            }

            showWinLines() {
                const winLines = document.querySelectorAll('.win-line');
                winLines.forEach(line => {
                    line.classList.add('win-line-active');
                });
                
                setTimeout(() => {
                    winLines.forEach(line => {
                        line.classList.remove('win-line-active');
                    });
                }, 1500);
            }

            hideWinLines() {
                const winLines = document.querySelectorAll('.win-line');
                winLines.forEach(line => {
                    line.classList.remove('win-line-active');
                });
            }

            showWinMessage(amount, symbol) {
                let message = '';
                let className = 'win';
                
                if (amount === 1000) {
                    message = `🎉 МЕГА ДЖЕКПОТ! Три вишни! +${amount} монет! 🎉`;
                    className = 'jackpot';
                    this.createConfetti(100);
                } else if (amount === 100) {
                    message = `⭐ Отлично! Три звезды! +${amount} монет! ⭐`;
                    this.createConfetti(30);
                } else {
                    message = `🎯 Победа! Три ${symbol}! +${amount} монет!`;
                    this.createConfetti(15);
                }
                
                this.resultElement.innerHTML = `<span class="${className}">${message}</span>`;
                this.updateBalance();
            }

            showMessage(message, type) {
                this.resultElement.textContent = message;
                if (type === 'error') {
                    this.resultElement.style.color = '#ff6b6b';
                } else if (type === 'lose') {
                    this.resultElement.style.color = '#ccc';
                } else {
                    this.resultElement.style.color = '#ffd700';
                }
            }

            celebrateWin(amount) {
                // Анимация барабанов
                this.reels.forEach(reel => {
                    reel.parentElement.classList.add('slot-win-animation');
                    setTimeout(() => {
                        reel.parentElement.classList.remove('slot-win-animation');
                    }, 1500);
                });

                // Анимация баланса для больших выигрышей
                if (amount >= 100) {
                    this.balanceElement.classList.add('jackpot');
                    setTimeout(() => {
                        this.balanceElement.classList.remove('jackpot');
                    }, 2000);
                }
            }

            createConfetti(count) {
                const colors = ['#ffd700', '#ff6b6b', '#4ecdc4', '#45b7d1', '#96ceb4', '#feca57'];
                
                for (let i = 0; i < count; i++) {
                    const confetti = document.createElement('div');
                    confetti.className = 'confetti';
                    confetti.style.left = Math.random() * 100 + 'vw';
                    confetti.style.background = colors[Math.floor(Math.random() * colors.length)];
                    confetti.style.width = Math.random() * 10 + 5 + 'px';
                    confetti.style.height = Math.random() * 10 + 5 + 'px';
                    confetti.style.borderRadius = Math.random() > 0.5 ? '50%' : '0';
                    
                    this.confettiContainer.appendChild(confetti);
                    
                    const animation = confetti.animate([
                        { 
                            transform: `translateY(-100px) rotate(0deg)`,
                            opacity: 1
                        },
                        { 
                            transform: `translateY(1000px) rotate(${Math.random() * 720 - 360}deg)`,
                            opacity: 0
                        }
                    ], {
                        duration: Math.random() * 3000 + 2000,
                        easing: 'cubic-bezier(0.1, 0.8, 0.3, 1)'
                    });
                    
                    animation.onfinish = () => {
                        confetti.remove();
                    };
                }
            }

            autoSpin() {
                if (this.balance < this.spinCost * 5) {
                    this.showMessage('❌ Недостаточно монет для 5 спинов!', 'error');
                    return;
                }
                this.autoSpinCount = 5;
                this.spin();
            }

            updateBalance() {
                this.balanceElement.textContent = this.balance;
                
                // Меняем цвет баланса при низком балансе
                if (this.balance < 50) {
                    this.balanceElement.style.color = '#ff6b6b';
                } else if (this.balance < 200) {
                    this.balanceElement.style.color = '#ffa500';
                } else {
                    this.balanceElement.style.color = '#ffd700';
                }
            }
        }

        // Инициализация игры при загрузке страницы
        document.addEventListener('DOMContentLoaded', () => {
            new SlotMachine();
        });

        // Предотвращение масштабирования на мобильных устройствах при двойном тапе
        let lastTouchEnd = 0;
        document.addEventListener('touchend', function (event) {
            const now = (new Date()).getTime();
            if (now - lastTouchEnd <= 300) {
                event.preventDefault();
            }
            lastTouchEnd = now;
        }, false);
    </script>
</body>
</html>
