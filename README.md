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

        /* 
    <div ="payout">
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
                    
