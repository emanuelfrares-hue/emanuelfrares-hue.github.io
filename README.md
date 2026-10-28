# emanuelfrares-hue.github.io
Trabalho do Evandro
Jogo de luta
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Pixel Street Fighter 16-Bit</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Press Start 2P', monospace;
            background-color: #0b0c10;
            color: #ffffff;
            touch-action: manipulation;
            user-select: none;
            -webkit-user-select: none;
            overflow-x: hidden;
        }

        /* Canvas Crisp Pixelation Rendering */
        canvas {
            image-rendering: -moz-crisp-edges;
            image-rendering: -webkit-crisp-edges;
            image-rendering: pixelated;
            image-rendering: crisp-edges;
            box-shadow: 0 0 35px rgba(255, 69, 0, 0.3);
        }

        .pixel-border {
            border: 4px solid #fff;
            box-shadow: 4px 4px 0px #000;
        }

        .pixel-shadow {
            text-shadow: 3px 3px 0px #000;
        }

        .health-bar-container {
            background: #222;
            border: 3px solid #f59e0b;
            box-shadow: inset 0 0 10px #000, 3px 3px 0 #000;
        }

        /* Cooldown Bar Animation Pulse */
        .cd-ready {
            box-shadow: 0 0 10px #10b981;
            animation: pulse-ready 1.2s infinite alternate;
        }

        @keyframes pulse-ready {
            0% { border-color: #10b981; }
            100% { border-color: #6ee7b7; }
        }

        .touch-btn {
            background: rgba(30, 41, 59, 0.85);
            border: 2px solid #475569;
            active:bg-amber-500;
        }
        .touch-btn:active {
            background: #d97706;
            transform: scale(0.95);
        }
    </style>
</head>
<body class="flex flex-col items-center justify-start min-h-screen p-2 md:p-4 bg-slate-950">

    <!-- Header / Title Banner -->
    <header class="w-full max-w-4xl text-center mb-2">
        <h1 class="text-xl md:text-3xl text-amber-500 tracking-wider pixel-shadow font-extrabold italic uppercase drop-shadow">
            PIXEL FIGHTER <span class="text-red-500">16-BIT</span>
        </h1>
        <p class="text-[10px] md:text-xs text-slate-400 mt-1">Street Fighter Style Pixel Arena</p>
    </header>

    <!-- Main Game Container -->
    <div class="relative w-full max-w-4xl bg-slate-900 rounded-xl p-2 md:p-4 border-2 border-slate-700 shadow-2xl">
        
        <!-- TOP HUD: Health Bars, Names, Timer, Game Mode Switch -->
        <div class="flex flex-col gap-2 mb-2">
            <!-- Mode Toggle & Controls Info Header -->
            <div class="flex justify-between items-center text-[10px] md:text-xs text-slate-300">
                <button id="modeToggleBtn" class="bg-indigo-600 hover:bg-indigo-500 px-3 py-1 rounded border border-indigo-400 font-bold transition text-white">
                    MODO: <span id="modeText" class="text-amber-300">P1 vs CPU</span>
                </button>
                <div class="text-amber-400 font-bold">ROUND 1</div>
                <button id="soundToggleBtn" class="bg-slate-800 hover:bg-slate-700 px-3 py-1 rounded border border-slate-600 text-white">
                    SOM: <span id="soundText" class="text-green-400">ON</span>
                </button>
            </div>

            <!-- Health Bars -->
            <div class="grid grid-cols-12 gap-2 items-center">
                <!-- Player 1 HP -->
                <div class="col-span-5 flex flex-col">
                    <div class="flex justify-between items-center text-[10px] md:text-xs font-bold mb-1">
                        <span class="text-blue-400 flex items-center gap-1">
                            <span class="inline-block w-2 h-2 bg-blue-500 rounded-full"></span> P1: RYU
                        </span>
                        <span id="p1HpText" class="text-amber-300">100 HP</span>
                    </div>
                    <div class="health-bar-container h-6 w-full relative overflow-hidden rounded-sm">
                        <!-- Trailing Delayed Damage Bar -->
                        <div id="p1HealthDelay" class="absolute top-0 left-0 h-full bg-red-600 transition-all duration-500" style="width: 100%;"></div>
                        <!-- Current Health Bar -->
                        <div id="p1HealthBar" class="absolute top-0 left-0 h-full bg-gradient-to-r from-yellow-400 to-green-500 transition-all duration-100" style="width: 100%;"></div>
                    </div>
                </div>

                <!-- Timer / VS -->
                <div class="col-span-2 text-center">
                    <div id="timer" class="text-xl md:text-3xl text-amber-400 font-bold tracking-tighter bg-slate-950 border-2 border-amber-500 py-1 rounded">
                        99
                    </div>
                </div>

                <!-- Player 2 HP -->
                <div class="col-span-5 flex flex-col">
                    <div class="flex justify-between items-center text-[10px] md:text-xs font-bold mb-1">
                        <span id="p2HpText" class="text-amber-300">100 HP</span>
                        <span class="text-red-400 flex items-center gap-1">
                            KEN <span id="p2Tag" class="text-[9px] bg-red-900/60 text-red-200 px-1 rounded">(CPU)</span>
                            <span class="inline-block w-2 h-2 bg-red-500 rounded-full"></span>
                        </span>
                    </div>
                    <div class="health-bar-container h-6 w-full relative overflow-hidden rounded-sm dir-rtl">
                        <!-- Trailing Delayed Damage Bar -->
                        <div id="p2HealthDelay" class="absolute top-0 right-0 h-full bg-red-600 transition-all duration-500" style="width: 100%;"></div>
                        <!-- Current Health Bar -->
                        <div id="p2HealthBar" class="absolute top-0 right-0 h-full bg-gradient-to-l from-yellow-400 to-green-500 transition-all duration-100" style="width: 100%;"></div>
                    </div>
                </div>
            </div>
        </div>

        <!-- CANVAS GAME ARENA -->
        <div class="relative w-full aspect-[16/9] max-h-[500px] overflow-hidden rounded-lg border-4 border-slate-800 bg-black">
            <canvas id="gameCanvas" width="800" height="450" class="w-full h-full block"></canvas>
            
            <!-- OVERLAY ANNOUNCEMENT BANNER (ROUND 1 - FIGHT / KO) -->
            <div id="overlayBanner" class="absolute inset-0 flex flex-col items-center justify-center bg-black/60 z-20 transition-opacity duration-300 pointer-events-none">
                <h2 id="overlayTitle" class="text-3xl md:text-5xl text-amber-400 pixel-shadow tracking-widest text-center animate-bounce">
                    ROUND 1
                </h2>
                <p id="overlaySubtitle" class="text-sm md:text-xl text-red-500 mt-2 pixel-shadow font-bold">
                    LUTE! (FIGHT!)
                </p>
                <button id="restartBtn" class="mt-6 px-6 py-3 bg-red-600 hover:bg-red-500 text-white font-bold text-xs md:text-sm rounded border-2 border-amber-400 shadow-lg pointer-events-auto hidden transition transform hover:scale-105">
                    REINICIAR PARTIDA
                </button>
            </div>
        </div>

        <!-- COOLDOWN & CONTROLS HUD INDICATORS -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-3 mt-3 text-[10px]">
            <!-- Player 1 Cooldowns & Keybinds -->
            <div class="bg-slate-950 p-2.5 rounded-lg border border-slate-800">
                <div class="text-blue-400 font-bold mb-2 flex justify-between">
                    <span>P1 CONTROLES (RYU)</span>
                    <span class="text-slate-400 text-[9px]">A/D (Mover) | W (Pular)</span>
                </div>
                <div class="grid grid-cols-4 gap-1.5 text-center">
                    <!-- Soco -->
                    <div id="cd-p1-punch" class="bg-slate-900 border border-slate-700 p-1 rounded relative overflow-hidden">
                        <div class="text-slate-300 font-bold">SOCO [F]</div>
                        <div class="text-[8px] text-amber-400">Dano: 8</div>
                        <div class="cd-fill absolute bottom-0 left-0 h-1 bg-blue-500 w-full transition-all"></div>
                    </div>
                    <!-- Chute -->
                    <div id="cd-p1-kick" class="bg-slate-900 border border-slate-700 p-1 rounded relative overflow-hidden">
                        <div class="text-slate-300 font-bold">CHUTE [G]</div>
                        <div class="text-[8px] text-amber-400">Dano: 14</div>
                        <div class="cd-fill absolute bottom-0 left-0 h-1 bg-blue-500 w-full transition-all"></div>
                    </div>
                    <!-- Poder -->
                    <div id="cd-p1-power" class="bg-slate-900 border border-slate-700 p-1 rounded relative overflow-hidden">
                        <div class="text-slate-300 font-bold">PODER [H]</div>
                        <div class="text-[8px] text-amber-400">Dano: 22</div>
                        <div class="cd-fill absolute bottom-0 left-0 h-1 bg-amber-500 w-full transition-all"></div>
                    </div>
                    <!-- Defesa -->
                    <div id="cd-p1-defend" class="bg-slate-900 border border-slate-700 p-1 rounded relative overflow-hidden">
                        <div class="text-slate-300 font-bold">DEFESA [S]</div>
                        <div class="text-[8px] text-green-400">-85% Dano</div>
                        <div class="cd-fill absolute bottom-0 left-0 h-1 bg-green-500 w-full transition-all"></div>
                    </div>
                </div>
            </div>

            <!-- Player 2 Cooldowns & Keybinds -->
            <div class="bg-slate-950 p-2.5 rounded-lg border border-slate-800">
                <div class="text-red-400 font-bold mb-2 flex justify-between">
                    <span>P2 CONTROLES (KEN)</span>
                    <span class="text-slate-400 text-[9px]">Setas (Mover) | Cima (Pular)</span>
                </div>
                <div class="grid grid-cols-4 gap-1.5 text-center">
                    <!-- Soco -->
                    <div id="cd-p2-punch" class="bg-slate-900 border border-slate-700 p-1 rounded relative overflow-hidden">
                        <div class="text-slate-300 font-bold">SOCO [J]</div>
                        <div class="text-[8px] text-amber-400">Dano: 8</div>
                        <div class="cd-fill absolute bottom-0 left-0 h-1 bg-red-500 w-full transition-all"></div>
                    </div>
                    <!-- Chute -->
                    <div id="cd-p2-kick" class="bg-slate-900 border border-slate-700 p-1 rounded relative overflow-hidden">
                        <div class="text-slate-300 font-bold">CHUTE [K]</div>
                        <div class="text-[8px] text-amber-400">Dano: 14</div>
                        <div class="cd-fill absolute bottom-0 left-0 h-1 bg-red-500 w-full transition-all"></div>
                    </div>
                    <!-- Poder -->
                    <div id="cd-p2-power" class="bg-slate-900 border border-slate-700 p-1 rounded relative overflow-hidden">
                        <div class="text-slate-300 font-bold">PODER [L]</div>
                        <div class="text-[8px] text-amber-400">Dano: 22</div>
                        <div class="cd-fill absolute bottom-0 left-0 h-1 bg-amber-500 w-full transition-all"></div>
                    </div>
                    <!-- Defesa -->
                    <div id="cd-p2-defend" class="bg-slate-900 border border-slate-700 p-1 rounded relative overflow-hidden">
                        <div class="text-slate-300 font-bold">DEFESA [Baixo]</div>
                        <div class="text-[8px] text-green-400">-85% Dano</div>
                        <div class="cd-fill absolute bottom-0 left-0 h-1 bg-green-500 w-full transition-all"></div>
                    </div>
                </div>
            </div>
        </div>

        <!-- TOUCH CONTROLS OVERLAY FOR MOBILE -->
        <div class="md:hidden mt-3 pt-2 border-t border-slate-800">
            <div class="text-[9px] text-center text-slate-400 mb-2">CONTROLES TOUCH (P1)</div>
            <div class="flex justify-between items-center px-1">
                <!-- DPAD Movement -->
                <div class="grid grid-cols-3 gap-1 w-32 h-24">
                    <div></div>
                    <button id="touchUp" class="touch-btn rounded p-2 text-center text-xs font-bold text-white">▲</button>
                    <div></div>
                    <button id="touchLeft" class="touch-btn rounded p-2 text-center text-xs font-bold text-white">◄</button>
                    <button id="touchDown" class="touch-btn rounded p-2 text-center text-xs font-bold text-white">▼</button>
                    <button id="touchRight" class="touch-btn rounded p-2 text-center text-xs font-bold text-white">►</button>
                </div>
                <!-- Action Buttons -->
                <div class="grid grid-cols-2 gap-1.5 w-36">
                    <button id="touchPunch" class="touch-btn text-xs font-bold py-2 bg-blue-700 text-white rounded">SOCO</button>
                    <button id="touchKick" class="touch-btn text-xs font-bold py-2 bg-emerald-700 text-white rounded">CHUTE</button>
                    <button id="touchPower" class="touch-btn text-xs font-bold py-2 bg-amber-600 text-white rounded">PODER</button>
                    <button id="touchDefend" class="touch-btn text-xs font-bold py-2 bg-purple-700 text-white rounded">DEFESA</button>
                </div>
            </div>
        </div>
    </div>

    <!-- AUDIO ENGINE & GAME SCRIPT -->
    <script>
        // ====================================================================
        // AUDIO SYNTHESIZER (RETRO 16-BIT SFX USING WEB AUDIO API)
        // ====================================================================
        class SoundFX {
            constructor() {
                this.ctx = null;
                this.enabled = true;
            }

            init() {
                if (!this.ctx) {
                    const AudioContext = window.AudioContext || window.webkitAudioContext;
                    if (AudioContext) this.ctx = new AudioContext();
                }
            }

            playSynth(type, freqStart, freqEnd, duration, volume = 0.1) {
                if (!this.enabled) return;
                this.init();
                if (!this.ctx) return;

                try {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();

                    osc.type = type;
                    osc.frequency.setValueAtTime(freqStart, this.ctx.currentTime);
                    if (freqEnd !== freqStart) {
                        osc.frequency.exponentialRampToValueAtTime(Math.max(freqEnd, 10), this.ctx.currentTime + duration);
                    }

                    gain.gain.setValueAtTime(volume, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + duration);

                    osc.connect(gain);
                    gain.connect(this.ctx.destination);

                    osc.start();
                    osc.stop(this.ctx.currentTime + duration);
                } catch(e){}
            }

            playNoise(duration, volume = 0.1) {
                if (!this.enabled) return;
                this.init();
                if (!this.ctx) return;

                try {
                    const bufferSize = this.ctx.sampleRate * duration;
                    const buffer = this.ctx.createBuffer(1, bufferSize, this.ctx.sampleRate);
                    const data = buffer.getChannelData(0);
                    for (let i = 0; i < bufferSize; i++) {
                        data[i] = Math.random() * 2 - 1;
                    }

                    const noise = this.ctx.createBufferSource();
                    noise.buffer = buffer;

                    const gain = this.ctx.createGain();
                    gain.gain.setValueAtTime(volume, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + duration);

                    noise.connect(gain);
                    gain.connect(this.ctx.destination);

                    noise.start();
                } catch(e){}
            }

            punch() { this.playNoise(0.1, 0.15); this.playSynth('square', 180, 40, 0.1, 0.1); }
            kick() { this.playNoise(0.15, 0.2); this.playSynth('triangle', 220, 30, 0.15, 0.15); }
            power() { this.playSynth('sawtooth', 200, 600, 0.35, 0.12); }
            hit() { this.playNoise(0.2, 0.25); this.playSynth('sawtooth', 120, 30, 0.2, 0.2); }
            block() { this.playSynth('square', 600, 800, 0.08, 0.1); }
            jump() { this.playSynth('square', 150, 300, 0.12, 0.08); }
            ko() {
                this.playSynth('sawtooth', 400, 100, 0.6, 0.2);
                setTimeout(() => this.playSynth('square', 200, 50, 0.8, 0.2), 200);
            }
        }

        const sfx = new SoundFX();

        // ====================================================================
        // GAME CONFIG & CONSTANTS
        // ====================================================================
        const CANVAS_WIDTH = 800;
        const CANVAS_HEIGHT = 450;
        const GROUND_Y = 380;
        const GRAVITY = 0.65;

        // Skill Cooldowns (in milliseconds)
        const COOLDOWNS = {
            PUNCH: 300,
            KICK: 600,
            POWER: 2500,
            DEFEND: 1000
        };

        // Skill Damage Values
        const DAMAGE = {
            PUNCH: 8,
            KICK: 14,
            POWER: 22
        };

        // Key Mappings
        const KEYS = {
            P1: { left: 'KeyA', right: 'KeyD', jump: 'KeyW', defend: 'KeyS', punch: 'KeyF', kick: 'KeyG', power: 'KeyH' },
            P2: { left: 'ArrowLeft', right: 'ArrowRight', jump: 'ArrowUp', defend: 'ArrowDown', punch: 'KeyJ', kick: 'KeyK', power: 'KeyL' }
        };

        // ====================================================================
        // PIXEL ART GRAPHICS RENDERER (PROCEDURAL RETRO SPRITES)
        // ====================================================================
        function drawPixelRect(ctx, x, y, width, height, color) {
            ctx.fillStyle = color;
            ctx.fillRect(Math.round(x), Math.round(y), Math.round(width), Math.round(height));
        }

        // Draw Fighter Sprite with state-based pixel components
        function drawFighterSprite(ctx, x, y, width, height, facingRight, state, colorTheme, frameTick) {
            ctx.save();
            ctx.translate(x + width / 2, y + height);
            if (!facingRight) ctx.scale(-1, 1);

            const scale = 3; // Pixel scale grid
            const p = (px) => px * scale;

            // Palette setup
            const giColor = colorTheme === 'red' ? '#dc2626' : '#2563eb'; // Red vs Blue Gi
            const giDark = colorTheme === 'red' ? '#991b1b' : '#1e40af';
            const skinColor = '#fca5a5';
            const skinShadow = '#f87171';
            const hairColor = colorTheme === 'red' ? '#451a03' : '#fbbf24'; // Ryu Dark Brown / Ken Blonde
            const beltColor = '#0f172a'; // Black belt

            // Stance bobbing animation
            let bobY = Math.sin(frameTick * 0.15) * 2;
            let legOffset = 0;

            if (state === 'WALK') {
                legOffset = Math.sin(frameTick * 0.3) * 6;
            } else if (state === 'JUMP') {
                bobY = -10;
            } else if (state === 'HIT') {
                bobY = 4;
            }

            // Shadow on ground
            ctx.fillStyle = 'rgba(0, 0, 0, 0.35)';
            ctx.beginPath();
            ctx.ellipse(0, 0, p(10), p(3), 0, 0, Math.PI * 2);
            ctx.fill();

            // Offset origin for sprite drawing relative to bottom center
            ctx.translate(-p(8), -p(32) + bobY);

            // --- LEGS & FEET ---
            if (state === 'KICK') {
                // Standing leg
                drawPixelRect(ctx, p(4), p(20), p(4), p(12), giDark);
                // Extended High Kick Leg
                drawPixelRect(ctx, p(8), p(12), p(14), p(5), giColor);
                drawPixelRect(ctx, p(20), p(11), p(4), p(6), skinColor); // Foot
            } else {
                // Left Leg
                drawPixelRect(ctx, p(2) - legOffset, p(20), p(4), p(10), giColor);
                drawPixelRect(ctx, p(1) - legOffset, p(29), p(5), p(3), skinShadow); // Barefoot
                // Right Leg
                drawPixelRect(ctx, p(9) + legOffset, p(20), p(4), p(10), giDark);
                drawPixelRect(ctx, p(9) + legOffset, p(29), p(5), p(3), skinColor);
            }

            // --- TORSO & GI ---
            drawPixelRect(ctx, p(4), p(10), p(8), p(11), giColor);
            drawPixelRect(ctx, p(5), p(12), p(6), p(8), '#fff'); // Inner V-neck chest
            drawPixelRect(ctx, p(6), p(13), p(4), p(6), skinColor);

            // Black Belt
            drawPixelRect(ctx, p(3), p(19), p(10), p(2), beltColor);
            drawPixelRect(ctx, p(10), p(20), p(2), p(5), beltColor); // Belt tail

            // --- ARMS & ATTACKS ---
            if (state === 'PUNCH') {
                // Back arm
                drawPixelRect(ctx, p(2), p(11), p(3), p(6), giDark);
                // Extended Punch Arm
                drawPixelRect(ctx, p(10), p(10), p(12), p(4), skinColor);
                drawPixelRect(ctx, p(18), p(9), p(5), p(6), skinShadow); // Fist
            } else if (state === 'POWER') {
                // Both arms extended for Hadoken pose
                drawPixelRect(ctx, p(8), p(10), p(10), p(5), giColor);
                drawPixelRect(ctx, p(16), p(9), p(6), p(7), skinColor);
                // Charge Energy Aura Pixels
                ctx.fillStyle = '#60a5fa';
                ctx.fillRect(p(21), p(7), p(3), p(11));
                ctx.fillRect(p(23), p(9), p(3), p(7));
            } else if (state === 'DEFEND') {
                // Crossed Guard Arms
                drawPixelRect(ctx, p(8), p(9), p(5), p(10), giDark);
                drawPixelRect(ctx, p(10), p(8), p(5), p(10), skinColor);
            } else if (state !== 'KICK') {
                // Idle Arms
                drawPixelRect(ctx, p(2), p(11), p(3), p(7), giDark); // Back arm
                drawPixelRect(ctx, p(11), p(11), p(3), p(7), skinColor); // Front arm
            }

            // --- HEAD & FACE ---
            if (state === 'HIT') {
                // Leaning back hit face
                drawPixelRect(ctx, p(3), p(1), p(8), p(9), skinColor);
                drawPixelRect(ctx, p(2), p(0), p(9), p(4), hairColor);
            } else {
                // Normal Head
                drawPixelRect(ctx, p(4), p(2), p(8), p(8), skinColor);
                // Headband / Hair
                drawPixelRect(ctx, p(3), p(1), p(10), p(4), hairColor);
                drawPixelRect(ctx, p(3), p(5), p(10), p(2), colorTheme === 'red' ? '#ef4444' : '#10b981'); // Headband
                // Eyes & Brows
                drawPixelRect(ctx, p(9), p(5), p(2), p(1), '#000');
            }

            ctx.restore();
        }

        // ====================================================================
        // GAME ENGINE CLASS
        // ====================================================================
        class Game {
            constructor() {
                this.canvas = document.getElementById('gameCanvas');
                this.ctx = this.canvas.getContext('2d');

                this.isCpuMode = true;
                this.gameState = 'START'; // START, FIGHTING, KO
                this.timer = 99;
                this.timerInterval = null;
                this.frameTick = 0;

                this.screenShakeTime = 0;
                this.screenShakeIntensity = 0;

                this.projectiles = [];
                this.particles = [];
                this.floatingTexts = [];

                this.keysPressed = {};

                // Players Initialization
                this.p1 = this.createPlayer(1, 150, 'red');
                this.p2 = this.createPlayer(2, 600, 'blue');

                this.initEventListeners();
                this.updateHudCooldowns();
                this.startRoundIntro();

                // Start Main Animation Loop
                requestAnimationFrame(this.loop.bind(this));
            }

            createPlayer(id, x, colorTheme) {
                return {
                    id: id,
                    x: x,
                    y: GROUND_Y - 96,
                    vx: 0,
                    vy: 0,
                    width: 48,
                    height: 96,
                    hp: 100,
                    facingRight: id === 1,
                    isGrounded: true,
                    isBlocking: false,
                    state: 'IDLE', // IDLE, WALK, JUMP, PUNCH, KICK, POWER, DEFEND, HIT, KO
                    stateTimer: 0,
                    colorTheme: colorTheme,
                    cooldowns: {
                        punch: 0,
                        kick: 0,
                        power: 0,
                        defend: 0
                    }
                };
            }

            initEventListeners() {
                // Key Listeners
                window.addEventListener('keydown', (e) => {
                    this.keysPressed[e.code] = true;
                    // Prevent page scrolling on arrow keys & space
                    if (['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight', 'Space'].includes(e.code)) {
                        e.preventDefault();
                    }
                });

                window.addEventListener('keyup', (e) => {
                    this.keysPressed[e.code] = false;
                });

                // Mode Toggle Button
                document.getElementById('modeToggleBtn').addEventListener('click', () => {
                    this.isCpuMode = !this.isCpuMode;
                    document.getElementById('modeText').innerText = this.isCpuMode ? 'P1 vs CPU' : '1P vs 2P';
                    document.getElementById('p2Tag').innerText = this.isCpuMode ? '(CPU)' : '(P2)';
                    this.resetMatch();
                });

                // Sound Toggle Button
                document.getElementById('soundToggleBtn').addEventListener('click', () => {
                    sfx.enabled = !sfx.enabled;
                    document.getElementById('soundText').innerText = sfx.enabled ? 'ON' : 'OFF';
                    document.getElementById('soundText').className = sfx.enabled ? 'text-green-400' : 'text-red-400';
                });

                // Restart Button
                document.getElementById('restartBtn').addEventListener('click', () => {
                    this.resetMatch();
                });

                // Mobile Touch Controls
                this.setupTouchControls();
            }

            setupTouchControls() {
                const bindTouch = (id, key) => {
                    const btn = document.getElementById(id);
                    if (!btn) return;
                    btn.addEventListener('touchstart', (e) => { e.preventDefault(); this.keysPressed[key] = true; });
                    btn.addEventListener('touchend', (e) => { e.preventDefault(); this.keysPressed[key] = false; });
                };

                bindTouch('touchLeft', KEYS.P1.left);
                bindTouch('touchRight', KEYS.P1.right);
                bindTouch('touchUp', KEYS.P1.jump);
                bindTouch('touchDown', KEYS.P1.defend);
                bindTouch('touchPunch', KEYS.P1.punch);
                bindTouch('touchKick', KEYS.P1.kick);
                bindTouch('touchPower', KEYS.P1.power);
                bindTouch('touchDefend', KEYS.P1.defend);
            }

            triggerShake(intensity = 8, time = 12) {
                this.screenShakeIntensity = intensity;
                this.screenShakeTime = time;
            }

            startRoundIntro() {
                this.gameState = 'START';
                const banner = document.getElementById('overlayBanner');
                const title = document.getElementById('overlayTitle');
                const subtitle = document.getElementById('overlaySubtitle');
                const restartBtn = document.getElementById('restartBtn');

                banner.classList.remove('opacity-0', 'pointer-events-none');
                restartBtn.classList.add('hidden');
                title.innerText = 'ROUND 1';
                subtitle.innerText = 'LUTE! (FIGHT!)';
                subtitle.className = 'text-sm md:text-xl text-amber-400 mt-2 pixel-shadow font-bold';

                setTimeout(() => {
                    banner.classList.add('opacity-0', 'pointer-events-none');
                    this.gameState = 'FIGHTING';
                    this.startTimer();
                }, 1800);
            }

            startTimer() {
                clearInterval(this.timerInterval);
                this.timer = 99;
                document.getElementById('timer').innerText = this.timer;
                this.timerInterval = setInterval(() => {
                    if (this.gameState === 'FIGHTING') {
                        this.timer--;
                        document.getElementById('timer').innerText = this.timer;
                        if (this.timer <= 0) {
                            this.handleTimeOut();
                        }
                    }
                }, 1000);
            }

            resetMatch() {
                clearInterval(this.timerInterval);
                this.p1 = this.createPlayer(1, 150, 'red');
                this.p2 = this.createPlayer(2, 600, 'blue');
                this.projectiles = [];
                this.particles = [];
                this.floatingTexts = [];
                this.updateHealthUI();
                this.startRoundIntro();
            }

            // ================================================================
            // CPU AI BEHAVIOR ENGINE
            // ================================================================
            updateCpuAI() {
                if (!this.isCpuMode || this.gameState !== 'FIGHTING') return;

                const p2 = this.p2;
                const p1 = this.p1;
                const dist = Math.abs(p2.x - p1.x);

                // Clear AI simulated keys
                KEYS.P2_SIM = KEYS.P2_SIM || {};

                const now = Date.now();

                // React to P1 Power Projectile by jumping or defending
                const incomingProjectile = this.projectiles.find(p => p.owner === 1 && Math.abs(p.x - p2.x) < 180);
                if (incomingProjectile) {
                    if (Math.random() < 0.6 && p2.cooldowns.defend <= 0) {
                        this.triggerPlayerAction(p2, 'DEFEND');
                        return;
                    } else if (p2.isGrounded) {
                        p2.vy = -14;
                        p2.isGrounded = false;
                        sfx.jump();
                    }
                }

                // AI Distance Management & Attacks
                if (dist > 120) {
                    // Walk towards P1
                    p2.vx = p2.x > p1.x ? -3 : 3;
                    p2.state = 'WALK';

                    // Randomly cast Hadoken/Power from distance
                    if (dist > 200 && p2.cooldowns.power <= 0 && Math.random() < 0.03) {
                        this.triggerPlayerAction(p2, 'POWER');
                    }
                } else {
                    // Close range battle decisions
                    p2.vx = 0;
                    if (Math.random() < 0.08 && p2.cooldowns.punch <= 0) {
                        this.triggerPlayerAction(p2, 'PUNCH');
                    } else if (Math.random() < 0.06 && p2.cooldowns.kick <= 0) {
                        this.triggerPlayerAction(p2, 'KICK');
                    } else if (Math.random() < 0.04 && p2.cooldowns.defend <= 0) {
                        this.triggerPlayerAction(p2, 'DEFEND');
                    } else {
                        p2.state = 'IDLE';
                    }
                }
            }

            // ================================================================
            // INPUT & PLAYER ACTIONS
            // ================================================================
            processInputs() {
                if (this.gameState !== 'FIGHTING') return;

                // --- PLAYER 1 INPUTS ---
                this.handlePlayerInput(this.p1, KEYS.P1);

                // --- PLAYER 2 INPUTS (If Human Mode) ---
                if (!this.isCpuMode) {
                    this.handlePlayerInput(this.p2, KEYS.P2);
                } else {
                    this.updateCpuAI();
                }
            }

            handlePlayerInput(player, keys) {
                if (player.state === 'HIT' || player.state === 'KO') return;

                const now = Date.now();

                // Movement
                player.vx = 0;
                if (this.keysPressed[keys.left]) {
                    player.vx = -3.5;
                    if (player.state === 'IDLE') player.state = 'WALK';
                }
                if (this.keysPressed[keys.right]) {
                    player.vx = 3.5;
                    if (player.state === 'IDLE') player.state = 'WALK';
                }

                // Jump
                if (this.keysPressed[keys.jump] && player.isGrounded) {
                    player.vy = -14;
                    player.isGrounded = false;
                    player.state = 'JUMP';
                    sfx.jump();
                }

                // Defend/Block
                if (this.keysPressed[keys.defend] && player.cooldowns.defend <= 0) {
                    this.triggerPlayerAction(player, 'DEFEND');
                    return;
                }

                // Attacks (Check Cooldowns)
                if (this.keysPressed[keys.punch] && player.cooldowns.punch <= 0) {
                    this.triggerPlayerAction(player, 'PUNCH');
                } else if (this.keysPressed[keys.kick] && player.cooldowns.kick <= 0) {
                    this.triggerPlayerAction(player, 'KICK');
                } else if (this.keysPressed[keys.power] && player.cooldowns.power <= 0) {
                    this.triggerPlayerAction(player, 'POWER');
                }
            }

            triggerPlayerAction(player, action) {
                const now = Date.now();

                if (action === 'PUNCH') {
                    player.state = 'PUNCH';
                    player.stateTimer = 12; // frame lock
                    player.cooldowns.punch = now + COOLDOWNS.PUNCH;
                    sfx.punch();
                    this.checkMeleeHit(player, DAMAGE.PUNCH, 55);
                } else if (action === 'KICK') {
                    player.state = 'KICK';
                    player.stateTimer = 16;
                    player.cooldowns.kick = now + COOLDOWNS.KICK;
                    sfx.kick();
                    this.checkMeleeHit(player, DAMAGE.KICK, 70);
                } else if (action === 'POWER') {
                    player.state = 'POWER';
                    player.stateTimer = 22;
                    player.cooldowns.power = now + COOLDOWNS.POWER;
                    sfx.power();
                    // Spawn Energy Ball Projectile
                    const projX = player.facingRight ? player.x + player.width + 10 : player.x - 20;
                    this.projectiles.push({
                        x: projX,
                        y: player.y + 30,
                        vx: player.facingRight ? 7 : -7,
                        owner: player.id,
                        damage: DAMAGE.POWER,
                        radius: 16
                    });
                } else if (action === 'DEFEND') {
                    player.state = 'DEFEND';
                    player.isBlocking = true;
                    player.stateTimer = 18;
                    player.cooldowns.defend = now + COOLDOWNS.DEFEND;
                    sfx.block();
                }
            }

            // ================================================================
            // COMBAT & COLLISION DETECTION
            // ================================================================
            checkMeleeHit(attacker, baseDamage, range) {
                const defender = attacker.id === 1 ? this.p2 : this.p1;
                const distance = Math.abs((attacker.x + attacker.width / 2) - (defender.x + defender.width / 2));

                // Check direction and reach distance
                const isFacingDefender = (attacker.facingRight && defender.x > attacker.x) ||
                                         (!attacker.facingRight && defender.x < attacker.x);

                if (isFacingDefender && distance <= range) {
                    this.applyDamage(defender, baseDamage, attacker.facingRight ? 1 : -1);
                }
            }

            applyDamage(target, damage, direction) {
                let actualDamage = damage;

                // Defense reduction (85% reduction)
                if (target.state === 'DEFEND' || target.isBlocking) {
                    actualDamage = Math.max(1, Math.round(damage * 0.15));
                    sfx.block();
                    this.addFloatingText(`BLOQUEIO! -${actualDamage}`, target.x + 20, target.y - 10, '#6ee7b7');
                    this.spawnImpactSparks(target.x + 20, target.y + 30, '#10b981');
                } else {
                    target.state = 'HIT';
                    target.stateTimer = 14;
                    sfx.hit();
                    this.triggerShake(10, 10);
                    this.addFloatingText(`-${actualDamage}`, target.x + 20, target.y - 10, '#ef4444');
                    this.spawnImpactSparks(target.x + 20, target.y + 30, '#f59e0b');

                    // Knockback push
                    target.vx += direction * 5;
                }

                target.hp = Math.max(0, target.hp - actualDamage);
                this.updateHealthUI();

                // Check KO
                if (target.hp <= 0) {
                    target.state = 'KO';
                    this.handleKO(target.id === 1 ? 2 : 1);
                }
            }

            spawnImpactSparks(x, y, color) {
                for (let i = 0; i < 12; i++) {
                    this.particles.push({
                        x: x,
                        y: y,
                        vx: (Math.random() - 0.5) * 8,
                        vy: (Math.random() - 0.5) * 8,
                        size: Math.random() * 5 + 3,
                        color: color,
                        life: 15
                    });
                }
            }

            addFloatingText(text, x, y, color) {
                this.floatingTexts.push({
                    text: text,
                    x: x,
                    y: y,
                    vy: -1.5,
                    opacity: 1.0,
                    color: color
                });
            }

            updateHealthUI() {
                const p1Pct = (this.p1.hp / 100) * 100;
                const p2Pct = (this.p2.hp / 100) * 100;

                document.getElementById('p1HealthBar').style.width = `${p1Pct}%`;
                document.getElementById('p2HealthBar').style.width = `${p2Pct}%`;
                document.getElementById('p1HpText').innerText = `${this.p1.hp} HP`;
                document.getElementById('p2HpText').innerText = `${this.p2.hp} HP`;

                // Delayed animated health catchup
                setTimeout(() => {
                    const delay1 = document.getElementById('p1HealthDelay');
                    const delay2 = document.getElementById('p2HealthDelay');
                    if (delay1) delay1.style.width = `${p1Pct}%`;
                    if (delay2) delay2.style.width = `${p2Pct}%`;
                }, 300);
            }

            updateHudCooldowns() {
                const now = Date.now();
                const updateBar = (elementId, ready) => {
                    const el = document.getElementById(elementId);
                    if (!el) return;
                    if (ready) {
                        el.classList.add('cd-ready');
                    } else {
                        el.classList.remove('cd-ready');
                    }
                };

                updateBar('cd-p1-punch', now >= this.p1.cooldowns.punch);
                updateBar('cd-p1-kick', now >= this.p1.cooldowns.kick);
                updateBar('cd-p1-power', now >= this.p1.cooldowns.power);
                updateBar('cd-p1-defend', now >= this.p1.cooldowns.defend);

                updateBar('cd-p2-punch', now >= this.p2.cooldowns.punch);
                updateBar('cd-p2-kick', now >= this.p2.cooldowns.kick);
                updateBar('cd-p2-power', now >= this.p2.cooldowns.power);
                updateBar('cd-p2-defend', now >= this.p2.cooldowns.defend);
            }

            handleKO(winnerId) {
                this.gameState = 'KO';
                sfx.ko();
                clearInterval(this.timerInterval);

                const banner = document.getElementById('overlayBanner');
                const title = document.getElementById('overlayTitle');
                const subtitle = document.getElementById('overlaySubtitle');
                const restartBtn = document.getElementById('restartBtn');

                banner.classList.remove('opacity-0', 'pointer-events-none');
                title.innerText = 'K.O.!';
                subtitle.innerText = winnerId === 1 ? 'JOGADOR 1 (RYU) VENCEU!' : (this.isCpuMode ? 'CPU (KEN) VENCEU!' : 'JOGADOR 2 (KEN) VENCEU!');
                subtitle.className = 'text-base md:text-2xl text-amber-300 mt-2 pixel-shadow font-bold';
                restartBtn.classList.remove('hidden');
            }

            handleTimeOut() {
                this.gameState = 'KO';
                clearInterval(this.timerInterval);

                const banner = document.getElementById('overlayBanner');
                const title = document.getElementById('overlayTitle');
                const subtitle = document.getElementById('overlaySubtitle');
                const restartBtn = document.getElementById('restartBtn');

                banner.classList.remove('opacity-0', 'pointer-events-none');
                title.innerText = 'TEMPO!';

                if (this.p1.hp > this.p2.hp) {
                    subtitle.innerText = 'JOGADOR 1 VENCEU POR VIDA!';
                } else if (this.p2.hp > this.p1.hp) {
                    subtitle.innerText = 'JOGADOR 2 VENCEU POR VIDA!';
                } else {
                    subtitle.innerText = 'EMPATE!';
                }

                restartBtn.classList.remove('hidden');
            }

            // ================================================================
            // PHYSICS & GAME STATE UPDATE
            // ================================================================
            update() {
                this.frameTick++;
                this.updateHudCooldowns();
                this.processInputs();

                [this.p1, this.p2].forEach(p => {
                    // Apply Gravity & Movement
                    p.x += p.vx;
                    p.y += p.vy;
                    p.vy += GRAVITY;

                    // Ground Collision Check
                    if (p.y >= GROUND_Y - p.height) {
                        p.y = GROUND_Y - p.height;
                        p.vy = 0;
                        p.isGrounded = true;
                        if (p.state === 'JUMP') p.state = 'IDLE';
                    }

                    // Screen Bounds
                    if (p.x < 10) p.x = 10;
                    if (p.x > CANVAS_WIDTH - p.width - 10) p.x = CANVAS_WIDTH - p.width - 10;

                    // Attack / Lock Timers
                    if (p.stateTimer > 0) {
                        p.stateTimer--;
                        if (p.stateTimer === 0 && p.state !== 'KO') {
                            p.state = 'IDLE';
                            p.isBlocking = false;
                        }
                    }

                    // Friction
                    p.vx *= 0.8;
                });

                // Dynamically face each other
                if (this.p1.state !== 'KO' && this.p2.state !== 'KO') {
                    if (this.p1.x < this.p2.x) {
                        this.p1.facingRight = true;
                        this.p2.facingRight = false;
                    } else {
                        this.p1.facingRight = false;
                        this.p2.facingRight = true;
                    }
                }

                // Player Collision Pushback
                const dist = Math.abs(this.p1.x - this.p2.x);
                if (dist < 35) {
                    if (this.p1.x < this.p2.x) {
                        this.p1.x -= 2;
                        this.p2.x += 2;
                    } else {
                        this.p1.x += 2;
                        this.p2.x -= 2;
                    }
                }

                // Projectiles Update
                for (let i = this.projectiles.length - 1; i >= 0; i--) {
                    const proj = this.projectiles[i];
                    proj.x += proj.vx;

                    // Particle trail
                    this.particles.push({
                        x: proj.x,
                        y: proj.y + (Math.random() - 0.5) * 10,
                        vx: -proj.vx * 0.2,
                        vy: (Math.random() - 0.5) * 2,
                        size: Math.random() * 4 + 2,
                        color: proj.owner === 1 ? '#60a5fa' : '#f87171',
                        life: 10
                    });

                    // Target check
                    const target = proj.owner === 1 ? this.p2 : this.p1;
                    const hitDist = Math.hypot(proj.x - (target.x + target.width / 2), proj.y - (target.y + target.height / 2));

                    if (hitDist < proj.radius + 25) {
                        this.applyDamage(target, proj.damage, proj.vx > 0 ? 1 : -1);
                        this.projectiles.splice(i, 1);
                        continue;
                    }

                    // Boundary Check
                    if (proj.x < -20 || proj.x > CANVAS_WIDTH + 20) {
                        this.projectiles.splice(i, 1);
                    }
                }

                // Particles Update
                for (let i = this.particles.length - 1; i >= 0; i--) {
                    const part = this.particles[i];
                    part.x += part.vx;
                    part.y += part.vy;
                    part.life--;
                    if (part.life <= 0) this.particles.splice(i, 1);
                }

                // Floating Damage Numbers Update
                for (let i = this.floatingTexts.length - 1; i >= 0; i--) {
                    const txt = this.floatingTexts[i];
                    txt.y += txt.vy;
                    txt.opacity -= 0.025;
                    if (txt.opacity <= 0) this.floatingTexts.splice(i, 1);
                }

                // Screen Shake Decay
                if (this.screenShakeTime > 0) {
                    this.screenShakeTime--;
                }
            }

            // ================================================================
            // RENDERING CANVAS ENGINE
            // ================================================================
            drawStageBackground() {
                const ctx = this.ctx;

                // Retro Gradient Sky
                const skyGradient = ctx.createLinearGradient(0, 0, 0, GROUND_Y);
                skyGradient.addColorStop(0, '#0f172a');
                skyGradient.addColorStop(0.6, '#311b92');
                skyGradient.addColorStop(1, '#6b21a8');
                ctx.fillStyle = skyGradient;
                ctx.fillRect(0, 0, CANVAS_WIDTH, GROUND_Y);

                // Pixel Moon
                drawPixelRect(ctx, 650, 40, 45, 45, '#fef08a');
                drawPixelRect(ctx, 640, 50, 10, 25, 'rgba(15, 23, 42, 0.4)');

                // City Skyline Silhouettes
                ctx.fillStyle = '#1e1b4b';
                for (let i = 0; i < CANVAS_WIDTH; i += 40) {
                    const h = 60 + Math.sin(i * 0.05) * 35;
                    ctx.fillRect(i, GROUND_Y - h, 35, h);
                    // Glowing Retro Windows
                    if (i % 80 === 0) {
                        ctx.fillStyle = '#fde047';
                        ctx.fillRect(i + 8, GROUND_Y - h + 15, 6, 8);
                        ctx.fillRect(i + 20, GROUND_Y - h + 30, 6, 8);
                        ctx.fillStyle = '#1e1b4b';
                    }
                }

                // Stage Floor Ground
                ctx.fillStyle = '#1f2937';
                ctx.fillRect(0, GROUND_Y, CANVAS_WIDTH, CANVAS_HEIGHT - GROUND_Y);

                // Red Line Border on Ground
                drawPixelRect(ctx, 0, GROUND_Y, CANVAS_WIDTH, 6, '#dc2626');

                // Floor Grid Lines for 3D Depth
                ctx.fillStyle = '#374151';
                for (let x = 0; x < CANVAS_WIDTH; x += 30) {
                    ctx.fillRect(x, GROUND_Y + 15, 15, 2);
                    ctx.fillRect(x + 10, GROUND_Y + 35, 15, 2);
                }
            }

            render() {
                const ctx = this.ctx;

                // Apply Screen Shake Camera Effect
                ctx.save();
                if (this.screenShakeTime > 0) {
                    const dx = (Math.random() - 0.5) * this.screenShakeIntensity;
                    const dy = (Math.random() - 0.5) * this.screenShakeIntensity;
                    ctx.translate(dx, dy);
                }

                // Clear & Draw Stage
                ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT);
                this.drawStageBackground();

                // Draw Players
                drawFighterSprite(ctx, this.p1.x, this.p1.y, this.p1.width, this.p1.height, this.p1.facingRight, this.p1.state, this.p1.colorTheme, this.frameTick);
                drawFighterSprite(ctx, this.p2.x, this.p2.y, this.p2.width, this.p2.height, this.p2.facingRight, this.p2.state, this.p2.colorTheme, this.frameTick);

                // Draw Hadoken Projectiles
                this.projectiles.forEach(p => {
                    ctx.fillStyle = p.owner === 1 ? '#3b82f6' : '#ef4444';
                    ctx.beginPath();
                    ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
                    ctx.fill();

                    // Energy Ball Inner Core Pixel
                    ctx.fillStyle = '#ffffff';
                    ctx.fillRect(p.x - 6, p.y - 6, 12, 12);
                });

                // Draw Pixel Particles
                this.particles.forEach(part => {
                    drawPixelRect(ctx, part.x, part.y, part.size, part.size, part.color);
                });

                // Draw Dynamic Damage Text Numbers
                this.floatingTexts.forEach(txt => {
                    ctx.save();
                    ctx.globalAlpha = Math.max(0, txt.opacity);
                    ctx.font = '14px "Press Start 2P"';
                    ctx.fillStyle = txt.color;
                    ctx.fillText(txt.text, txt.x, txt.y);
                    ctx.restore();
                });

                ctx.restore();
            }

            loop() {
                this.update();
                this.render();
                requestAnimationFrame(this.loop.bind(this));
            }
        }

        // Initialize Game Instance on Window Load
        window.onload = () => {
            new Game();
        };
    </script>
</body>
</html>
