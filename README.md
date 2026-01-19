<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover"/>
    <title>GKR CRYPTO</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://unpkg.com/@tonconnect/ui@latest/dist/tonconnect-ui.min.js"></script>
    <style>
        :root {
            --tg-theme-bg-color: #0f0c29;
            --tg-theme-text-color: #ffffff;
            --tg-theme-button-color: #007bff;
            --tg-theme-button-text-color: #ffffff;
            --accent: #00ffff;
            --gold: #ffd700;
            --dark: #1a1a2e;
        }
        body {
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, var(--tg-theme-bg-color), #302b63, var(--dark));
            color: var(--tg-theme-text-color);
            height: 100vh;
            overflow: hidden;
            display: flex;
            flex-direction: column;
            align-items: center;
            touch-action: manipulation;
        }
        .main-panel {
            flex: 1;
            width: 100%;
            max-width: 480px;
            padding: env(safe-area-inset-top) 16px 120px;
            text-align: center;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        h1 { font-size: 2.8rem; margin: 16px 0 8px; text-shadow: 0 0 20px var(--accent); letter-spacing: 1px; }
        .stats { margin: 12px 0; font-size: 1.4rem; }
        .level { color: #ffcc00; font-weight: 600; }
        .balance { font-size: 4.5rem; font-weight: 800; color: var(--gold); text-shadow: 0 4px 20px rgba(255,215,0,0.6); margin: 8px 0; line-height: 1; }
        .pph { font-size: 1.6rem; color: var(--accent); opacity: 0.9; }
        .energy-container { margin: 24px 0; padding: 0 16px; width: 100%; max-width: 360px; }
        .energy-bar { height: 28px; background: rgba(255,255,255,0.08); border-radius: 14px; overflow: hidden; border: 2px solid var(--accent); box-shadow: 0 0 15px rgba(0,255,255,0.3); }
        .energy-fill { height: 100%; width: 0%; background: linear-gradient(90deg, var(--accent), #0099ff); transition: width 0.4s ease-out; }
        .energy-text { font-size: 1.1rem; margin-top: 6px; opacity: 0.8; }
        .logo-wrapper { position: relative; width: 260px; height: 260px; margin: 32px auto; cursor: pointer; user-select: none; touch-action: manipulation; }
        #logo { width: 100%; height: 100%; object-fit: contain; transition: transform 0.12s ease; filter: drop-shadow(0 0 40px var(--accent)); }
        #logo:active { transform: scale(1.15); }
        .particle { position: absolute; font-size: 2.8rem; font-weight: bold; color: var(--gold); pointer-events: none; animation: particleFloat 1.3s ease-out forwards; transform: translate(-50%, -50%); }
        @keyframes particleFloat { 0% { opacity:1; transform: translate(-50%, -50%) scale(1); } 100% { opacity:0; transform: translate(-50%, -220%) scale(1.6); } }
        .claim-btn, .action-btn { margin: 24px auto; padding: 16px 48px; font-size: 1.4rem; font-weight: 600; border: none; border-radius: 50px; color: white; cursor: pointer; box-shadow: 0 8px 20px rgba(0,123,255,0.4); transition: all 0.2s; }
        .claim-btn { background: linear-gradient(45deg, #ff6600, #ff9933); }
        .action-btn { background: var(--tg-theme-button-color); }
        .claim-btn:hover, .action-btn:hover { transform: scale(1.05); }
        .claim-btn:active, .action-btn:active { transform: scale(0.96); }
        #connectBtn { min-width: 220px; }
        #walletStatus { font-size: 1.1rem; opacity: 0.85; margin-top: 12px; word-break: break-all; }
        .section { display: none; margin: 32px auto; padding: 24px; background: rgba(0,255,255,0.08); border-radius: 16px; border: 1px solid var(--accent); max-width: 90%; width: 100%; box-sizing: border-box; }
        .invite-link { background: rgba(255,255,255,0.1); padding: 12px; border-radius: 12px; word-break: break-all; font-size: 1.1rem; margin: 16px 0; }
        .btn-row { display: flex; justify-content: center; gap: 16px; flex-wrap: wrap; margin-top: 16px; }
        .copy-btn, .share-btn { padding: 12px 32px; background: var(--tg-theme-button-color); color: white; border: none; border-radius: 50px; cursor: pointer; font-size: 1.1rem; min-width: 140px; }
        nav { position: fixed; bottom: 0; left: 0; right: 0; height: calc(env(safe-area-inset-bottom, 0px) + 70px); background: rgba(26,26,46,0.92); backdrop-filter: blur(10px); display: flex; justify-content: space-around; align-items: center; padding-bottom: env(safe-area-inset-bottom); border-top: 1px solid rgba(0,255,255,0.2); z-index: 100; }
        nav button { background: none; border: none; color: white; font-size: 0.95rem; cursor: pointer; padding: 8px 10px; display: flex; flex-direction: column; align-items: center; gap: 4px; transition: color 0.2s; }
        nav button.active { color: var(--accent); text-shadow: 0 0 10px var(--accent); }
        nav button::before { content: attr(data-icon); font-size: 1.7rem; }
    </style>
</head>
<body>

    <div class="main-panel" id="mainContent">
        <h1>GKR CRYPTO</h1>

        <div class="stats">
            <div class="level">CEO Level: <span id="level">1</span></div>
            <div class="balance" id="balance">0 GKR</div>
            <div class="pph">Profit / hour: <span id="pph">0</span> GKR</div>
        </div>

        <div class="energy-container">
            <div class="energy-bar">
                <div class="energy-fill" id="energyFill"></div>
            </div>
            <div class="energy-text">Energy: <span id="energyText">1000 / 1000</span></div>
        </div>

        <div class="logo-wrapper" id="tapArea">
            <img id="logo" src="https://i.imgur.com/YOUR_GKR_LOGO.png" alt="GKR Logo">
            <div id="particles"></div>
        </div>

        <button id="offlineClaim" class="claim-btn" style="display:none;">Claim Offline Earnings</button>

        <button id="connectBtn" class="action-btn">Connect TON Wallet</button>
        <div id="walletStatus">Wallet: Not connected</div>
    </div>

    <!-- Friends Section -->
    <div id="friendsSection" class="section">
        <h2>Invite Friends & Earn</h2>
        <p>دوستوں کو دعوت دیں اور ہر کامیاب ریفرل پر بونس حاصل کریں!</p>
        <div class="invite-link" id="refLink">Loading invite link...</div>
        <div class="btn-row">
            <button class="copy-btn" onclick="copyRefLink()">Copy Link</button>
            <button class="share-btn" onclick="shareRefLink()">Share on Telegram</button>
        </div>
    </div>

    <nav>
        <button id="homeBtn" class="active" data-icon="🏠">Home</button>
        <button id="mineBtn" data-icon="⛏️">Mine</button>
        <button id="friendsBtn" data-icon="👥">Friends</button>
        <button id="earnBtn" data-icon="💰">Earn</button>
        <button id="airdropBtn" data-icon="🎁">Airdrop</button>
    </nav>

    <script>
        // ────────────────────────────────────────────────
        // Telegram Mini App Initialization
        // ────────────────────────────────────────────────
        const tg = window.Telegram?.WebApp;
        if (tg) {
            tg.ready();
            tg.expand();
            tg.disableVerticalSwipes?.(); // Prevent swipe to close (important for tap games)
            tg.HapticFeedback.impactOccurred('medium');
            document.documentElement.style.setProperty('--tg-theme-bg-color', tg.backgroundColor || '#0f0c29');
        }

        // ────────────────────────────────────────────────
        // Game Constants & Data
        // ────────────────────────────────────────────────
        const MAX_ENERGY = 1000;
        const ENERGY_REGEN_PER_SEC = 1;
        const OFFLINE_MAX_HOURS = 3;

        let data = JSON.parse(localStorage.getItem('gkrProData')) || {
            balance: 0,
            pph: 0,           // profit per hour – ابھی 0، بعد میں اپ گریڈ کر سکتے ہو
            energy: MAX_ENERGY,
            maxEnergy: MAX_ENERGY,
            perTap: 1,
            level: 1,
            lastActive: Date.now()
        };

        const els = {
            mainContent: document.getElementById('mainContent'),
            balance: document.getElementById('balance'),
            pph: document.getElementById('pph'),
            level: document.getElementById('level'),
            energyFill: document.getElementById('energyFill'),
            energyText: document.getElementById('energyText'),
            tapArea: document.getElementById('tapArea'),
            particles: document.getElementById('particles'),
            offlineClaim: document.getElementById('offlineClaim'),
            connectBtn: document.getElementById('connectBtn'),
            walletStatus: document.getElementById('walletStatus'),
            friendsSection: document.getElementById('friendsSection'),
            refLink: document.getElementById('refLink')
        };

        // ────────────────────────────────────────────────
        // Referral Link (https + safe)
        // ────────────────────────────────────────────────
        let refLink = "https://t.me/gkr_crypto_bot";
        const user = tg?.initDataUnsafe?.user;
        if (user?.id) {
            refLink += `?start=ref_${user.id}`;
        }

        if (els.refLink) {
            els.refLink.textContent = refLink;
        }

        function copyRefLink() {
            navigator.clipboard.writeText(refLink).then(() => {
                alert('لنک کاپی ہو گیا! دوستوں کو بھیجیں۔');
                tg?.HapticFeedback.notificationOccurred('success');
            }).catch(() => {
                alert('کاپی نہ ہوا، لنک کو لمبا پریس کرکے کاپی کریں۔');
            });
        }

        function shareRefLink() {
            if (tg) {
                tg.openTelegramLink(refLink);
            } else {
                alert('اس لنک کو شیئر کریں: ' + refLink);
            }
        }

        // ────────────────────────────────────────────────
        // Core Functions
        // ────────────────────────────────────────────────
        function saveData() {
            localStorage.setItem('gkrProData', JSON.stringify(data));
        }

        function updateUI() {
            els.balance.textContent = Math.floor(data.balance).toLocaleString() + ' GKR';
            els.pph.textContent = data.pph.toLocaleString();
            els.level.textContent = data.level;
            const pct = (data.energy / data.maxEnergy) * 100;
            els.energyFill.style.width = `${pct}%`;
            els.energyText.textContent = `${Math.floor(data.energy)} / ${data.maxEnergy}`;
            saveData();
        }

        function createParticle() {
            const p = document.createElement('div');
            p.className = 'particle';
            p.textContent = `+${data.perTap}`;
            p.style.left = `${Math.random() * 100}%`;
            p.style.top = `${Math.random() * 100}%`;
            els.particles.appendChild(p);
            setTimeout(() => p.remove(), 1300);
        }

        function tap() {
            if (data.energy < 1) return;
            data.energy -= 1;
            data.balance += data.perTap;
            data.level = Math.floor(data.balance / 5000) + 1;
            createParticle();
            tg?.HapticFeedback.impactOccurred('light');
            updateUI();
        }

        // ────────────────────────────────────────────────
        // Tap Event (better for mobile)
        // ────────────────────────────────────────────────
        els.tapArea.addEventListener('touchstart', e => {
            e.preventDefault();
            tap();
        }, { passive: false });

        // ────────────────────────────────────────────────
        // Game Loop (interval for better battery/performance)
        // ────────────────────────────────────────────────
        let lastTime = performance.now();
        setInterval(() => {
            const now = performance.now();
            const delta = (now - lastTime) / 1000;
            lastTime = now;

            data.balance += (data.pph * delta) / 3600;
            if (data.energy < data.maxEnergy) {
                data.energy = Math.min(data.maxEnergy, data.energy + ENERGY_REGEN_PER_SEC * delta);
            }
            updateUI();
        }, 800); // ~1.25 fps update – smooth enough

        // ────────────────────────────────────────────────
        // Offline Earnings
        // ────────────────────────────────────────────────
        window.addEventListener('load', () => {
            const offlineMs = Date.now() - data.lastActive;
            const offlineHours = offlineMs / 3600000;

            if (offlineHours > 0.1) {
                const earned = Math.min(offlineHours, OFFLINE_MAX_HOURS) * data.pph;
                data.balance += earned;
                els.offlineClaim.textContent = `Claim ${Math.floor(earned).toLocaleString()} GKR (Offline)`;
                els.offlineClaim.style.display = 'block';
            }
            data.lastActive = Date.now();
            updateUI();
        });

        els.offlineClaim.addEventListener('click', () => {
            els.offlineClaim.style.display = 'none';
            tg?.HapticFeedback.notificationOccurred('success');
            alert('آف لائن کمائی کامیابی سے کلیم ہو گئی!');
            updateUI();
        });

        // ────────────────────────────────────────────────
        // TON Connect (use your own manifest in production!)
        // ────────────────────────────────────────────────
        const tonConnectUI = new TON_CONNECT_UI.TonConnectUI({
            manifestUrl: 'https://ton-connect.github.io/sdk/tonconnect-manifest.json', // Test – Replace with yours!
            // uiPreferences: { theme: 'DARK' } // optional
        });

        tonConnectUI.onStatusChange(wallet => {
            if (wallet?.connected) {
                const addr = wallet.account.address;
                els.walletStatus.textContent = `Connected: \( {addr.slice(0,6)}... \){addr.slice(-4)}`;
                tg?.HapticFeedback.notificationOccurred('success');
            } else {
                els.walletStatus.textContent = 'Wallet: Not connected';
            }
        });

        els.connectBtn.addEventListener('click', () => {
            tonConnectUI.openModal();
        });

        // ────────────────────────────────────────────────
        // Navigation
        // ────────────────────────────────────────────────
        document.querySelectorAll('nav button').forEach(btn => {
            btn.addEventListener('click', () => {
                document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
                btn.classList.add('active');

                els.mainContent.style.display = 'none';
                els.friendsSection.style.display = 'none';

                if (btn.id === 'homeBtn') {
                    els.mainContent.style.display = 'flex';
                } else if (btn.id === 'friendsBtn') {
                    els.friendsSection.style.display = 'block';
                } else {
                    alert(`${btn.textContent} سیکشن - جلد آرہا ہے!`);
                    els.mainContent.style.display = 'flex';
                }
            });
        });

        // Initial UI update
        updateUI();
    </script>
</body>
</html>
