<!DOCTYPE html>
<html lang="es" class="h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Disoluciones Químicas - Lab Interactivo 9º</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Fonts Google: Nunito & Poppins -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;700;800;900&family=Poppins:wght@600;700;800&display=swap" rel="stylesheet">
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Canvas Confetti Library -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        brand: {
                            purple: '#7C3AED',
                            pink: '#EC4899',
                            mint: '#10B981',
                            cyan: '#06B6D4',
                            yellow: '#F59E0B',
                            dark: '#0F172A',
                            card: '#1E293B'
                        }
                    },
                    fontFamily: {
                        sans: ['Nunito', 'sans-serif'],
                        heading: ['Poppins', 'sans-serif']
                    }
                }
            }
        }
    </script>

    <style>
        body {
            font-family: 'Nunito', sans-serif;
            background-color: #0F172A;
            color: #F8FAFC;
        }

        /* 3D Flip Cards */
        .flip-card {
            background-color: transparent;
            perspective: 1000px;
        }
        .flip-card-inner {
            position: relative;
            width: 100%;
            height: 100%;
            text-align: center;
            transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
            transform-style: preserve-3d;
        }
        .flip-card.flipped .flip-card-inner {
            transform: rotateY(180deg);
        }
        .flip-card-front, .flip-card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            -webkit-backface-visibility: hidden;
            backface-visibility: hidden;
            border-radius: 1.25rem;
        }
        .flip-card-back {
            transform: rotateY(180deg);
        }

        /* Glow & Custom Scrollbars */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #1E293B;
        }
        ::-webkit-scrollbar-thumb {
            background: #4C1D95;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #7C3AED;
        }

        .glow-purple {
            box-shadow: 0 0 25px -5px rgba(124, 58, 237, 0.5);
        }
        .glow-pink {
            box-shadow: 0 0 25px -5px rgba(236, 72, 153, 0.5);
        }
        .glow-mint {
            box-shadow: 0 0 25px -5px rgba(16, 185, 129, 0.5);
        }

        /* Liquid Tube Wave Animation */
        @keyframes wave {
            0% { transform: translateX(0) scaleY(1); }
            50% { transform: translateX(-25%) scaleY(1.05); }
            100% { transform: translateX(-50%) scaleY(1); }
        }
        .wave-anim {
            animation: wave 3s infinite linear;
        }

        /* Pulse Chat */
        @keyframes chatPulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.02); }
        }
        .chat-bubble-enter {
            animation: chatPulse 0.3s ease-out;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between selection:bg-brand-pink selection:text-white">

    <!-- HEADER / NAVIGATION -->
    <header class="sticky top-0 z-40 bg-slate-900/90 backdrop-blur-md border-b border-slate-800 px-4 py-3">
        <div class="max-w-6xl mx-auto flex flex-wrap justify-between items-center gap-4">
            <!-- App Logo -->
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 rounded-2xl bg-gradient-to-tr from-brand-purple to-brand-pink flex items-center justify-center text-white text-xl font-bold shadow-lg shadow-purple-500/30">
                    <i class="fa-solid font-bold fa-flask"></i>
                </div>
                <div>
                    <h1 class="font-heading font-extrabold text-lg md:text-xl bg-gradient-to-r from-purple-400 via-pink-400 to-cyan-400 bg-clip-text text-transparent">
                        ChemLab 9º
                    </h1>
                    <p class="text-xs text-slate-400 font-medium">Disoluciones Químicas</p>
                </div>
            </div>

            <!-- Dashboard Stats & Sound Toggle -->
            <div class="flex items-center space-x-4">
                <!-- Sound Toggle -->
                <button id="soundToggleBtn" onclick="toggleSound()" class="p-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-300 hover:text-yellow-400 transition-all border border-slate-700" title="Activar/Desactivar Sonido">
                    <i id="soundIcon" class="fa-solid fa-volume-high text-lg"></i>
                </button>

                <!-- XP Badge -->
                <div class="flex items-center space-x-2 bg-slate-800/80 px-3 py-1.5 rounded-xl border border-amber-500/30 text-amber-400 font-bold text-sm">
                    <i class="fa-solid fa-bolt text-yellow-400 animate-pulse"></i>
                    <span id="xpScoreDisplay">0 XP</span>
                </div>

                <!-- Lives Badge -->
                <div class="flex items-center space-x-1.5 bg-slate-800/80 px-3 py-1.5 rounded-xl border border-rose-500/30 text-rose-400 font-bold text-sm">
                    <i class="fa-solid fa-heart text-rose-500"></i>
                    <span id="livesDisplay">3</span>
                </div>
            </div>
        </div>

        <!-- Global Progress Bar -->
        <div class="max-w-6xl mx-auto mt-3 bg-slate-800 h-2 rounded-full overflow-hidden">
            <div id="globalProgressBar" class="h-full bg-gradient-to-r from-brand-purple via-brand-pink to-brand-mint w-1/4 transition-all duration-500"></div>
        </div>
    </header>

    <!-- NAVIGATION TABS -->
    <nav class="bg-slate-900/50 border-b border-slate-800/80 sticky top-[61px] z-30 backdrop-blur-sm">
        <div class="max-w-6xl mx-auto flex justify-between md:justify-center space-x-1 md:space-x-4 px-2 py-2 overflow-x-auto">
            <button onclick="switchTab('cap1')" id="nav-cap1" class="nav-tab active-tab flex items-center space-x-2 px-4 py-2 rounded-xl font-bold text-sm transition-all whitespace-nowrap bg-brand-purple text-white shadow-lg shadow-purple-500/20">
                <i class="fa-solid fa-comments"></i>
                <span>1. Chat Química</span>
            </button>
            <button onclick="switchTab('cap2')" id="nav-cap2" class="nav-tab flex items-center space-x-2 px-4 py-2 rounded-xl font-bold text-sm transition-all whitespace-nowrap text-slate-400 hover:text-slate-200 hover:bg-slate-800">
                <i class="fa-solid fa-calculator"></i>
                <span>2. Fórmulas & Lab</span>
            </button>
            <button onclick="switchTab('cap3')" id="nav-cap3" class="nav-tab flex items-center space-x-2 px-4 py-2 rounded-xl font-bold text-sm transition-all whitespace-nowrap text-slate-400 hover:text-slate-200 hover:bg-slate-800">
                <i class="fa-solid fa-icons"></i>
                <span>3. Pista de Baile</span>
            </button>
            <button onclick="switchTab('cap4')" id="nav-cap4" class="nav-tab flex items-center space-x-2 px-4 py-2 rounded-xl font-bold text-sm transition-all whitespace-nowrap text-slate-400 hover:text-slate-200 hover:bg-slate-800">
                <i class="fa-solid fa-gamepad"></i>
                <span>4. Misión Quiz</span>
            </button>
        </div>
    </nav>

    <!-- MAIN CONTENT CONTAINERS -->
    <main class="max-w-6xl mx-auto w-full px-4 py-6 flex-grow">

        <!-- CAPÍTULO 1: CHAT DE QUÍMICA & TIPOS -->
        <section id="cap1" class="tab-content space-y-8">
            <!-- Header banner -->
            <div class="bg-gradient-to-r from-purple-900/60 to-slate-900 border border-purple-500/30 p-6 rounded-3xl relative overflow-hidden">
                <div class="absolute -right-10 -bottom-10 text-slate-800/40 text-9xl font-black select-none pointer-events-none">01</div>
                <span class="bg-brand-purple/30 text-purple-300 border border-purple-400/30 text-xs font-bold px-3 py-1 rounded-full uppercase tracking-wider">Capítulo 1</span>
                <h2 class="text-2xl md:text-3xl font-heading font-extrabold text-white mt-2">¿Qué es realmente una Disolución? 🤔</h2>
                <p class="text-slate-300 mt-1 max-w-2xl text-sm md:text-base">Acompaña a Sofía y al Profe Carlos en su chat diario para descubrir los secretos de las mezclas homogéneas.</p>
            </div>

            <!-- CHAT INTERACTIVO -->
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-6 items-start">
                <div class="lg:col-span-7 bg-slate-900 border border-slate-800 rounded-3xl p-4 md:p-6 shadow-xl flex flex-col h-[520px]">
                    <!-- Chat Header -->
                    <div class="flex items-center space-x-3 pb-4 border-b border-slate-800">
                        <div class="relative">
                            <div class="w-10 h-10 rounded-full bg-pink-500 flex items-center justify-center text-white font-bold text-sm">
                                👩‍🔬
                            </div>
                            <span class="absolute bottom-0 right-0 w-3 h-3 bg-emerald-500 border-2 border-slate-900 rounded-full"></span>
                        </div>
                        <div>
                            <h3 class="font-bold text-white text-sm">Profe Carlos & Sofía</h3>
                            <p class="text-xs text-emerald-400 font-semibold">En línea • Chat de Química</p>
                        </div>
                    </div>

                    <!-- Chat Message Area -->
                    <div id="chatBox" class="flex-grow overflow-y-auto py-4 space-y-3 pr-2 scroll-smooth">
                        <!-- Messages dynamically added or static start -->
                        <div class="flex items-start space-x-2">
                            <span class="text-xl">👨‍🏫</span>
                            <div class="bg-slate-800 text-slate-200 p-3 rounded-2xl rounded-tl-none max-w-[85%] text-sm">
                                ¡Hola Sofía! 👋 ¿Alguna vez te has preguntado qué pasa cuando disuelves azúcar en el té?
                            </div>
                        </div>

                        <div class="flex items-start justify-end space-x-2">
                            <div class="bg-brand-purple text-white p-3 rounded-2xl rounded-tr-none max-w-[85%] text-sm">
                                ¡Hola Profe! Pues... desaparece el azúcar, ¿no? 🤨 ¿O sigue ahí escondido?
                            </div>
                            <span class="text-xl">👧</span>
                        </div>

                        <div class="flex items-start space-x-2">
                            <span class="text-xl">👨‍🏫</span>
                            <div class="bg-slate-800 text-slate-200 p-3 rounded-2xl rounded-tl-none max-w-[85%] text-sm">
                                ¡Sigue ahí! Se forma una <b>Disolución</b> (Mezcla homogénea de 1 sola fase).<br><br>
                                • <b>Soluto:</b> Lo que se disuelve en menor cantidad (el azúcar).<br>
                                • <b>Disolvente (o Solvente):</b> Lo que disuelve en mayor cantidad (el agua del té).
                            </div>
                        </div>
                    </div>

                    <!-- Chat Controls / Action Buttons -->
                    <div class="pt-3 border-t border-slate-800 flex flex-wrap gap-2">
                        <button onclick="sendChatMessage('pregunta1')" class="bg-slate-800 hover:bg-purple-600/30 hover:border-purple-500 border border-slate-700 text-slate-300 hover:text-white px-3 py-2 rounded-xl text-xs font-bold transition-all text-left">
                            ❓ ¿El agua siempre es el disolvente?
                        </button>
                        <button onclick="sendChatMessage('pregunta2')" class="bg-slate-800 hover:bg-purple-600/30 hover:border-purple-500 border border-slate-700 text-slate-300 hover:text-white px-3 py-2 rounded-xl text-xs font-bold transition-all text-left">
                            ☕ ¿Qué pasa si le echo 10 cucharadas de azúcar?
                        </button>
                    </div>
                </div>

                <!-- SIMULACIÓN DE ESTADOS (Saturado / Sobresaturado) -->
                <div class="lg:col-span-5 bg-slate-900 border border-slate-800 rounded-3xl p-6 shadow-xl space-y-4">
                    <h3 class="font-heading font-bold text-lg text-white flex items-center space-x-2">
                        <i class="fa-solid fa-flask-vial text-brand-pink"></i>
                        <span>Estados de Solubilidad</span>
                    </h3>
                    <p class="text-xs text-slate-400">Prueba cómo cambia la solución al agregar soluto:</p>

                    <!-- Flask Visualizer -->
                    <div class="relative w-full h-48 bg-slate-950 rounded-2xl border border-slate-800 flex items-center justify-center overflow-hidden">
                        <!-- Liquid body -->
                        <div id="stateLiquid" class="absolute bottom-0 w-full h-2/3 bg-cyan-500/40 border-t-2 border-cyan-400 transition-all duration-500"></div>
                        
                        <!-- Undissolved Solute (Crystals at bottom) -->
                        <div id="crystalsBottom" class="absolute bottom-1 flex justify-center space-x-1 opacity-0 transition-opacity duration-500">
                            <span class="text-lg">🧊</span><span class="text-sm">💎</span><span class="text-lg">🧊</span><span class="text-sm">💎</span>
                        </div>

                        <!-- Badge Overlay -->
                        <div id="stateBadge" class="relative z-10 px-4 py-2 rounded-xl bg-slate-900/90 border border-cyan-400 text-cyan-300 font-bold text-sm shadow-lg text-center">
                            Insaturada (No saturada) 🟢<br>
                            <span class="text-[10px] text-slate-400 font-normal">Aún cabe más soluto</span>
                        </div>
                    </div>

                    <!-- State Switcher Buttons -->
                    <div class="grid grid-cols-3 gap-2">
                        <button onclick="setSolubilityState('insaturada')" class="p-2 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl text-center transition-all">
                            <div class="text-xs font-bold text-emerald-400">Insaturada</div>
                            <div class="text-[10px] text-slate-400">Poco soluto</div>
                        </button>
                        <button onclick="setSolubilityState('saturada')" class="p-2 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl text-center transition-all">
                            <div class="text-xs font-bold text-yellow-400">Saturada</div>
                            <div class="text-[10px] text-slate-400">Límite exacto</div>
                        </button>
                        <button onclick="setSolubilityState('sobresaturada')" class="p-2 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl text-center transition-all">
                            <div class="text-xs font-bold text-rose-400">Sobresaturada</div>
                            <div class="text-[10px] text-slate-400">Exceso + Precipitado</div>
                        </button>
                    </div>
                </div>
            </div>

            <!-- LAS 6 COMBINACIONES DE DISOLUCIONES -->
            <div class="space-y-4 pt-4">
                <div class="flex items-center space-x-3">
                    <div class="p-2 rounded-xl bg-brand-pink/20 text-brand-pink">
                        <i class="fa-solid fa-shapes text-xl"></i>
                    </div>
                    <div>
                        <h3 class="font-heading font-extrabold text-xl text-white">Las 6 Combinaciones Posibles</h3>
                        <p class="text-xs text-slate-400">¡Las disoluciones no son solo líquidas! Toca cada tarjeta para ver un ejemplo de la vida real.</p>
                    </div>
                </div>

                <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-6 gap-3">
                    <!-- Gas - Gas -->
                    <div class="bg-slate-900 border border-slate-800 hover:border-purple-500/50 p-4 rounded-2xl text-center transition-all hover:-translate-y-1 group">
                        <div class="text-3xl mb-2 group-hover:scale-125 transition-transform">💨</div>
                        <div class="text-xs font-bold text-purple-400">Gas en Gas</div>
                        <div class="text-xs text-slate-200 font-semibold mt-1">El Aire</div>
                        <div class="text-[10px] text-slate-500">Nitrógeno + Oxígeno</div>
                    </div>
                    <!-- Gas - Líquido -->
                    <div class="bg-slate-900 border border-slate-800 hover:border-purple-500/50 p-4 rounded-2xl text-center transition-all hover:-translate-y-1 group">
                        <div class="text-3xl mb-2 group-hover:scale-125 transition-transform">🥤</div>
                        <div class="text-xs font-bold text-purple-400">Gas en Líquido</div>
                        <div class="text-xs text-slate-200 font-semibold mt-1">Gaseosa</div>
                        <div class="text-[10px] text-slate-500">CO2 en Agua</div>
                    </div>
                    <!-- Sólido - Líquido -->
                    <div class="bg-slate-900 border border-slate-800 hover:border-purple-500/50 p-4 rounded-2xl text-center transition-all hover:-translate-y-1 group">
                        <div class="text-3xl mb-2 group-hover:scale-125 transition-transform">🌊</div>
                        <div class="text-xs font-bold text-purple-400">Sólido en Líquido</div>
                        <div class="text-xs text-slate-200 font-semibold mt-1">Agua de Mar</div>
                        <div class="text-[10px] text-slate-500">Sal en Agua</div>
                    </div>
                    <!-- Líquido - Líquido -->
                    <div class="bg-slate-900 border border-slate-800 hover:border-purple-500/50 p-4 rounded-2xl text-center transition-all hover:-translate-y-1 group">
                        <div class="text-3xl mb-2 group-hover:scale-125 transition-transform">🍋</div>
                        <div class="text-xs font-bold text-purple-400">Líquido en Líquido</div>
                        <div class="text-xs text-slate-200 font-semibold mt-1">Limonada</div>
                        <div class="text-[10px] text-slate-500">Jugo de Limón + Agua</div>
                    </div>
                    <!-- Gas - Sólido -->
                    <div class="bg-slate-900 border border-slate-800 hover:border-purple-500/50 p-4 rounded-2xl text-center transition-all hover:-translate-y-1 group">
                        <div class="text-3xl mb-2 group-hover:scale-125 transition-transform">🧽</div>
                        <div class="text-xs font-bold text-purple-400">Gas en Sólido</div>
                        <div class="text-xs text-slate-200 font-semibold mt-1">Piedra Pómez</div>
                        <div class="text-[10px] text-slate-500">Aire atrapado en roca</div>
                    </div>
                    <!-- Sólido - Sólido -->
                    <div class="bg-slate-900 border border-slate-800 hover:border-purple-500/50 p-4 rounded-2xl text-center transition-all hover:-translate-y-1 group">
                        <div class="text-3xl mb-2 group-hover:scale-125 transition-transform">💍</div>
                        <div class="text-xs font-bold text-purple-400">Sólido en Sólido</div>
                        <div class="text-xs text-slate-200 font-semibold mt-1">Bronce / Oro</div>
                        <div class="text-[10px] text-slate-500">Aleaciones metálicas</div>
                    </div>
                </div>
            </div>
        </section>

        <!-- CAPÍTULO 2: FÓRMULAS 3D Y CALCULADORA EN VIVO -->
        <section id="cap2" class="tab-content hidden space-y-8">
            <div class="bg-gradient-to-r from-pink-900/60 to-slate-900 border border-pink-500/30 p-6 rounded-3xl relative overflow-hidden">
                <div class="absolute -right-10 -bottom-10 text-slate-800/40 text-9xl font-black select-none pointer-events-none">02</div>
                <span class="bg-brand-pink/30 text-pink-300 border border-pink-400/30 text-xs font-bold px-3 py-1 rounded-full uppercase tracking-wider">Capítulo 2</span>
                <h2 class="text-2xl md:text-3xl font-heading font-extrabold text-white mt-2">Laboratorio Virtual de Concentración 🧪</h2>
                <p class="text-slate-300 mt-1 max-w-2xl text-sm md:text-base">Gira las tarjetas 3D para dominar las fórmulas y experimenta con la probeta animada en vivo.</p>
            </div>

            <!-- FLIPCARDS 3D -->
            <div>
                <h3 class="text-lg font-bold text-white mb-4 flex items-center space-x-2">
                    <i class="fa-solid fa-rotate text-brand-pink"></i>
                    <span>Tarjetas 3D de Concentración Porcentual (Haz clic para girar)</span>
                </h3>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    
                    <!-- Card 1: % p/p -->
                    <div class="flip-card h-52 cursor-pointer" onclick="toggleFlip(this)">
                        <div class="flip-card-inner">
                            <div class="flip-card-front bg-slate-900 border-2 border-brand-purple p-6 flex flex-col justify-between items-center shadow-lg">
                                <span class="bg-purple-500/20 text-purple-300 text-xs px-2.5 py-1 rounded-full font-bold">Masa / Masa</span>
                                <div class="text-center">
                                    <h4 class="text-2xl font-black text-white font-heading">% p/p</h4>
                                    <p class="text-xs text-slate-400 mt-1">Porcentaje peso a peso</p>
                                </div>
                                <span class="text-xs text-purple-400 font-bold flex items-center space-x-1">
                                    <span>Toca para ver fórmula</span> <i class="fa-solid fa-arrow-right text-[10px]"></i>
                                </span>
                            </div>
                            <div class="flip-card-back bg-gradient-to-br from-brand-purple to-slate-900 border-2 border-purple-400 p-6 flex flex-col justify-center items-center text-center shadow-xl">
                                <h5 class="font-bold text-white mb-2 text-sm">Fórmula % p/p</h5>
                                <div class="bg-slate-950/80 p-3 rounded-xl border border-purple-500/40 text-xs text-purple-200 font-mono mb-2">
                                    (g soluto / g disolución) × 100
                                </div>
                                <p class="text-[11px] text-slate-300">¡Recuerda! g disolución = g soluto + g disolvente</p>
                            </div>
                        </div>
                    </div>

                    <!-- Card 2: % v/v -->
                    <div class="flip-card h-52 cursor-pointer" onclick="toggleFlip(this)">
                        <div class="flip-card-inner">
                            <div class="flip-card-front bg-slate-900 border-2 border-brand-pink p-6 flex flex-col justify-between items-center shadow-lg">
                                <span class="bg-pink-500/20 text-pink-300 text-xs px-2.5 py-1 rounded-full font-bold">Volumen / Volumen</span>
                                <div class="text-center">
                                    <h4 class="text-2xl font-black text-white font-heading">% v/v</h4>
                                    <p class="text-xs text-slate-400 mt-1">Porcentaje volumen a volumen</p>
                                </div>
                                <span class="text-xs text-pink-400 font-bold flex items-center space-x-1">
                                    <span>Toca para ver fórmula</span> <i class="fa-solid fa-arrow-right text-[10px]"></i>
                                </span>
                            </div>
                            <div class="flip-card-back bg-gradient-to-br from-brand-pink to-slate-900 border-2 border-pink-400 p-6 flex flex-col justify-center items-center text-center shadow-xl">
                                <h5 class="font-bold text-white mb-2 text-sm">Fórmula % v/v</h5>
                                <div class="bg-slate-950/80 p-3 rounded-xl border border-pink-500/40 text-xs text-pink-200 font-mono mb-2">
                                    (mL soluto / mL disolución) × 100
                                </div>
                                <p class="text-[11px] text-slate-300">Muy usado en bebidas, perfumes y desinfectantes.</p>
                            </div>
                        </div>
                    </div>

                    <!-- Card 3: % p/v -->
                    <div class="flip-card h-52 cursor-pointer" onclick="toggleFlip(this)">
                        <div class="flip-card-inner">
                            <div class="flip-card-front bg-slate-900 border-2 border-brand-mint p-6 flex flex-col justify-between items-center shadow-lg">
                                <span class="bg-emerald-500/20 text-emerald-300 text-xs px-2.5 py-1 rounded-full font-bold">Masa / Volumen</span>
                                <div class="text-center">
                                    <h4 class="text-2xl font-black text-white font-heading">% p/v</h4>
                                    <p class="text-xs text-slate-400 mt-1">Porcentaje peso a volumen</p>
                                </div>
                                <span class="text-xs text-emerald-400 font-bold flex items-center space-x-1">
                                    <span>Toca para ver fórmula</span> <i class="fa-solid fa-arrow-right text-[10px]"></i>
                                </span>
                            </div>
                            <div class="flip-card-back bg-gradient-to-br from-brand-mint to-slate-900 border-2 border-emerald-400 p-6 flex flex-col justify-center items-center text-center shadow-xl">
                                <h5 class="font-bold text-white mb-2 text-sm">Fórmula % p/v</h5>
                                <div class="bg-slate-950/80 p-3 rounded-xl border border-emerald-500/40 text-xs text-emerald-200 font-mono mb-2">
                                    (g soluto / mL disolución) × 100
                                </div>
                                <p class="text-[11px] text-slate-300">Combina sólidos disueltos en volúmenes de líquidos.</p>
                            </div>
                        </div>
                    </div>

                </div>
            </div>

            <!-- CALCULADORA INTERACTIVA & PROBETA ANIMADA -->
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 shadow-2xl">
                <h3 class="font-heading font-extrabold text-xl text-white mb-6 flex items-center space-x-3">
                    <i class="fa-solid fa-flask text-brand-mint text-2xl"></i>
                    <span>Simulador de Mezcla en Probeta Live</span>
                </h3>

                <div class="grid grid-cols-1 md:grid-cols-12 gap-8 items-center">
                    <!-- Sliders & Inputs Controls -->
                    <div class="md:col-span-7 space-y-6">
                        <!-- Solute Control -->
                        <div class="bg-slate-950 p-4 rounded-2xl border border-slate-800 space-y-2">
                            <div class="flex justify-between text-sm">
                                <label class="font-bold text-brand-pink flex items-center space-x-2">
                                    <span>🧂 Gramos de Soluto (g):</span>
                                </label>
                                <span id="soluteValDisplay" class="font-mono font-bold text-white bg-slate-800 px-2.5 py-0.5 rounded-lg border border-slate-700">10 g</span>
                            </div>
                            <input id="soluteRange" type="range" min="1" max="100" value="10" oninput="updateLabCalculator()" class="w-full accent-pink-500 cursor-pointer">
                        </div>

                        <!-- Solvent Control -->
                        <div class="bg-slate-950 p-4 rounded-2xl border border-slate-800 space-y-2">
                            <div class="flex justify-between text-sm">
                                <label class="font-bold text-cyan-400 flex items-center space-x-2">
                                    <span>💧 Gramos/mL de Disolvente:</span>
                                </label>
                                <span id="solventValDisplay" class="font-mono font-bold text-white bg-slate-800 px-2.5 py-0.5 rounded-lg border border-slate-700">90 g</span>
                            </div>
                            <input id="solventRange" type="range" min="10" max="300" value="90" oninput="updateLabCalculator()" class="w-full accent-cyan-400 cursor-pointer">
                        </div>

                        <!-- Live Calculation Output Cards -->
                        <div class="grid grid-cols-2 gap-3 pt-2">
                            <div class="bg-slate-800/80 p-3 rounded-xl border border-slate-700 text-center">
                                <div class="text-[11px] text-slate-400">Masa Disolución Total</div>
                                <div id="totalMassDisplay" class="text-lg font-black text-amber-400 font-mono">100 g</div>
                            </div>
                            <div class="bg-slate-800/80 p-3 rounded-xl border border-slate-700 text-center">
                                <div class="text-[11px] text-slate-400">Concentración % p/p</div>
                                <div id="calcResultDisplay" class="text-xl font-black text-brand-mint font-mono">10.0 %</div>
                            </div>
                        </div>
                    </div>

                    <!-- Animated Test Tube Visualization -->
                    <div class="md:col-span-5 flex flex-col items-center justify-center">
                        <div class="relative w-28 h-64 border-4 border-slate-600 border-t-0 rounded-b-full bg-slate-950/80 overflow-hidden flex flex-col justify-end shadow-inner">
                            
                            <!-- Graduation Lines -->
                            <div class="absolute inset-y-0 left-2 w-3 flex flex-col justify-between py-4 text-[9px] font-mono text-slate-500 z-20 pointer-events-none select-none">
                                <span>MAX</span>
                                <span>75%</span>
                                <span>50%</span>
                                <span>25%</span>
                            </div>

                            <!-- Fluid Level with Wave animation -->
                            <div id="tubeFluid" class="w-full bg-pink-500 transition-all duration-300 relative" style="height: 35%;">
                                <div class="absolute top-0 inset-x-0 h-2 bg-white/30 rounded-full"></div>
                            </div>
                        </div>
                        <p class="text-xs text-slate-400 mt-3 text-center">El color se intensifica a mayor concentración (% p/p)</p>
                    </div>
                </div>
            </div>
        </section>

        <!-- CAPÍTULO 3: LA PISTA DE BAILE (SIMULADOR) -->
        <section id="cap3" class="tab-content hidden space-y-8">
            <div class="bg-gradient-to-r from-emerald-900/60 to-slate-900 border border-emerald-500/30 p-6 rounded-3xl relative overflow-hidden">
                <div class="absolute -right-10 -bottom-10 text-slate-800/40 text-9xl font-black select-none pointer-events-none">03</div>
                <span class="bg-brand-mint/30 text-emerald-300 border border-emerald-400/30 text-xs font-bold px-3 py-1 rounded-full uppercase tracking-wider">Capítulo 3</span>
                <h2 class="text-2xl md:text-3xl font-heading font-extrabold text-white mt-2">La Analogía de la Pista de Baile 💃🕺</h2>
                <p class="text-slate-300 mt-1 max-w-2xl text-sm md:text-base">Entiende la concentración jugando: los bailarines son el <b>Soluto</b> y el tamaño del club es el <b>Disolvente</b>.</p>
            </div>

            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 shadow-2xl space-y-6">
                <!-- Controls Header -->
                <div class="flex flex-wrap items-center justify-between gap-4 bg-slate-950 p-4 rounded-2xl border border-slate-800">
                    <div class="flex items-center space-x-3">
                        <button onclick="changeDancers(-2)" class="w-10 h-10 rounded-xl bg-slate-800 hover:bg-slate-700 text-white font-bold text-lg transition-all">-</button>
                        <div class="text-center">
                            <span class="text-xs text-slate-400 block">Bailarines (Soluto)</span>
                            <span id="dancerCountDisplay" class="font-bold text-brand-pink text-lg">6</span>
                        </div>
                        <button onclick="changeDancers(2)" class="w-10 h-10 rounded-xl bg-slate-800 hover:bg-slate-700 text-white font-bold text-lg transition-all">+</button>
                    </div>

                    <!-- Status Display -->
                    <div class="text-center md:text-right">
                        <span class="text-xs text-slate-400 block">Estado de la Fiesta:</span>
                        <span id="danceStatusBadge" class="inline-block px-3 py-1 rounded-xl font-bold text-sm bg-emerald-500/20 text-emerald-400 border border-emerald-500/30">
                            Diluida 🟢 (Hay mucho espacio para bailar)
                        </span>
                    </div>

                    <div class="flex items-center space-x-3">
                        <button onclick="changeDanceFloorSize(-20)" class="w-10 h-10 rounded-xl bg-slate-800 hover:bg-slate-700 text-white font-bold text-lg transition-all">-</button>
                        <div class="text-center">
                            <span class="text-xs text-slate-400 block">Pista (Solvente)</span>
                            <span id="floorSizeDisplay" class="font-bold text-cyan-400 text-lg">100 m²</span>
                        </div>
                        <button onclick="changeDanceFloorSize(20)" class="w-10 h-10 rounded-xl bg-slate-800 hover:bg-slate-700 text-white font-bold text-lg transition-all">+</button>
                    </div>
                </div>

                <!-- Dance Floor Canvas Container -->
                <div class="relative w-full h-80 bg-slate-950 rounded-2xl border-2 border-slate-800 overflow-hidden flex items-center justify-center p-4">
                    <div id="danceFloorArea" class="transition-all duration-300 border-2 border-dashed border-purple-500/50 rounded-2xl flex flex-wrap items-center justify-center gap-3 p-4 overflow-hidden relative bg-purple-950/20" style="width: 80%; height: 80%;">
                        <!-- Emojis populated by JS -->
                    </div>
                </div>
            </div>
        </section>

        <!-- CAPÍTULO 4: MISIÓN FINAL & QUIZ GAMIFICADO -->
        <section id="cap4" class="tab-content hidden space-y-8">
            <div class="bg-gradient-to-r from-amber-900/60 to-slate-900 border border-amber-500/30 p-6 rounded-3xl relative overflow-hidden">
                <div class="absolute -right-10 -bottom-10 text-slate-800/40 text-9xl font-black select-none pointer-events-none">04</div>
                <span class="bg-brand-yellow/30 text-amber-300 border border-amber-400/30 text-xs font-bold px-3 py-1 rounded-full uppercase tracking-wider">Capítulo 4</span>
                <h2 class="text-2xl md:text-3xl font-heading font-extrabold text-white mt-2">Misión Final: El Desafío del Químico 🏆</h2>
                <p class="text-slate-300 mt-1 max-w-2xl text-sm md:text-base">Demuestra tus superpoderes en química. ¡Responde correctamente para ganar XP y subir de nivel!</p>
            </div>

            <!-- Quiz Card -->
            <div id="quizContainer" class="bg-slate-900 border border-slate-800 rounded-3xl p-6 md:p-8 shadow-2xl relative">
                <!-- Question Header -->
                <div class="flex justify-between items-center mb-6">
                    <span id="quizQuestionNum" class="text-xs font-bold text-brand-purple bg-purple-500/20 px-3 py-1 rounded-full border border-purple-500/30">Pregunta 1 de 4</span>
                    <span id="quizHintBtn" onclick="toggleQuizHint()" class="text-xs font-bold text-amber-400 hover:underline cursor-pointer flex items-center space-x-1">
                        <i class="fa-solid fa-lightbulb"></i>
                        <span>¿Pista?</span>
                    </span>
                </div>

                <!-- Hint Alert -->
                <div id="quizHintBox" class="hidden mb-4 p-3 bg-amber-500/10 border border-amber-500/30 rounded-xl text-amber-300 text-xs">
                    💡 <b>Pista:</b> Recuerda la fórmula: % p/p = (Gramos de Soluto / Gramos de Disolución Total) × 100.
                </div>

                <!-- Question Title -->
                <h3 id="quizQuestionText" class="text-lg md:text-xl font-bold text-white mb-6">
                    Se disuelven 6 gramos de sal en agua hasta completar 80 gramos de disolución total. ¿Cuál es el porcentaje peso a peso (% p/p)?
                </h3>

                <!-- Options Grid -->
                <div id="quizOptionsGrid" class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <!-- Dynamic Buttons -->
                </div>

                <!-- Feedback Box -->
                <div id="quizFeedback" class="hidden mt-6 p-4 rounded-2xl text-sm font-bold border"></div>

                <!-- Next Button -->
                <div class="mt-6 flex justify-end">
                    <button id="nextQuestionBtn" onclick="nextQuizQuestion()" class="hidden bg-brand-purple hover:bg-purple-600 text-white font-bold px-6 py-3 rounded-xl transition-all shadow-lg shadow-purple-500/30">
                        Siguiente Pregunta <i class="fa-solid fa-arrow-right ml-2"></i>
                    </button>
                </div>
            </div>
        </section>

    </main>

    <!-- QUICK GLOSSARY FLOATING BUTTON & MODAL -->
    <div class="fixed bottom-5 right-5 z-50">
        <button onclick="openGlossary()" class="bg-gradient-to-r from-brand-purple to-brand-pink text-white font-bold p-4 rounded-full shadow-2xl hover:scale-110 transition-all flex items-center space-x-2 border border-white/20">
            <i class="fa-solid fa-book-bookmark text-lg"></i>
            <span class="hidden md:inline text-xs">Glosario Rápido</span>
        </button>
    </div>

    <!-- GLOSSARY MODAL -->
    <div id="glossaryModal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
        <div class="bg-slate-900 border border-slate-800 rounded-3xl max-w-md w-full p-6 space-y-4 shadow-2xl relative">
            <button onclick="closeGlossary()" class="absolute top-4 right-4 text-slate-400 hover:text-white text-xl">
                <i class="fa-solid fa-xmark"></i>
            </button>
            <h3 class="font-heading font-extrabold text-xl text-white flex items-center space-x-2">
                <i class="fa-solid fa-book-open text-brand-pink"></i>
                <span>Glosario Rápido de 9º</span>
            </h3>
            <div class="space-y-3 text-xs text-slate-300 max-h-80 overflow-y-auto pr-2">
                <div class="bg-slate-800 p-3 rounded-xl">
                    <b class="text-brand-purple block mb-1">Disolución:</b> Mezcla homogénea compuesta por soluto y disolvente.
                </div>
                <div class="bg-slate-800 p-3 rounded-xl">
                    <b class="text-brand-pink block mb-1">Soluto:</b> Sustancia que se disuelve (menor cantidad).
                </div>
                <div class="bg-slate-800 p-3 rounded-xl">
                    <b class="text-brand-mint block mb-1">Disolvente:</b> Sustancia que disuelve al soluto (mayor cantidad).
                </div>
                <div class="bg-slate-800 p-3 rounded-xl">
                    <b class="text-amber-400 block mb-1">Concentración:</b> Relación entre la cantidad de soluto y la cantidad total de solución.
                </div>
            </div>
        </div>
    </div>

    <!-- FOOTER -->
    <footer class="bg-slate-900 border-t border-slate-800 text-center py-4 text-xs text-slate-500 mt-auto">
        Diseñado para estudiantes de 9º Grado • Química Interactiva 🚀
    </footer>

    <!-- JAVASCRIPT LOGIC -->
    <script>
        /* ===================================================
           SOUND ENGINE (Web Audio API - No External Files)
        =================================================== */
        let soundEnabled = true;
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

        function playSound(type) {
            if (!soundEnabled) return;
            try {
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);

                if (type === 'click') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(400, audioCtx.currentTime);
                    gain.gain.setValueAtTime(0.05, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.05);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.05);
                } else if (type === 'correct') {
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(523.25, audioCtx.currentTime); // C5
                    osc.frequency.setValueAtTime(659.25, audioCtx.currentTime + 0.1); // E5
                    gain.gain.setValueAtTime(0.08, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.25);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.25);
                } else if (type === 'wrong') {
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(180, audioCtx.currentTime);
                    osc.frequency.setValueAtTime(130, audioCtx.currentTime + 0.1);
                    gain.gain.setValueAtTime(0.08, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.25);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.25);
                }
            } catch (e) {
                console.log('Audio init required');
            }
        }

        function toggleSound() {
            soundEnabled = !soundEnabled;
            const icon = document.getElementById('soundIcon');
            if (soundEnabled) {
                icon.className = 'fa-solid fa-volume-high text-lg text-yellow-400';
            } else {
                icon.className = 'fa-solid fa-volume-xmark text-lg text-slate-500';
            }
        }

        /* ===================================================
           GLOBAL STATE & TAB NAVIGATION
        =================================================== */
        let userXP = 0;
        let userLives = 3;

        function addXP(points) {
            userXP += points;
            document.getElementById('xpScoreDisplay').innerText = `${userXP} XP`;
        }

        function switchTab(tabId) {
            playSound('click');
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            document.getElementById(tabId).classList.remove('hidden');

            document.querySelectorAll('.nav-tab').forEach(el => {
                el.className = 'nav-tab flex items-center space-x-2 px-4 py-2 rounded-xl font-bold text-sm transition-all whitespace-nowrap text-slate-400 hover:text-slate-200 hover:bg-slate-800';
            });

            const activeNav = document.getElementById(`nav-${tabId}`);
            activeNav.className = 'nav-tab active-tab flex items-center space-x-2 px-4 py-2 rounded-xl font-bold text-sm transition-all whitespace-nowrap bg-brand-purple text-white shadow-lg shadow-purple-500/20';

            // Progress Bar Update
            const bar = document.getElementById('globalProgressBar');
            if (tabId === 'cap1') bar.style.width = '25%';
            if (tabId === 'cap2') bar.style.width = '50%';
            if (tabId === 'cap3') bar.style.width = '75%';
            if (tabId === 'cap4') bar.style.width = '100%';
        }

        /* ===================================================
           CAPÍTULO 1: CHAT & ESTADOS
        =================================================== */
        function sendChatMessage(type) {
            playSound('click');
            const chatBox = document.getElementById('chatBox');

            if (type === 'pregunta1') {
                chatBox.innerHTML += `
                    <div class="flex items-start justify-end space-x-2 chat-bubble-enter">
                        <div class="bg-brand-purple text-white p-3 rounded-2xl rounded-tr-none max-w-[85%] text-sm">
                            ¿El agua siempre es el disolvente en todas las mezclas? 🤔
                        </div>
                        <span class="text-xl">👧</span>
                    </div>
                    <div class="flex items-start space-x-2 chat-bubble-enter">
                        <span class="text-xl">👨‍🏫</span>
                        <div class="bg-slate-800 text-slate-200 p-3 rounded-2xl rounded-tl-none max-w-[85%] text-sm">
                            ¡Buena pregunta! El agua es el <b>Disolvente Universal</b> en soluciones acuosas, ¡pero también hay disoluciones de gases o metales sólidos como el bronce! 🪙
                        </div>
                    </div>
                `;
            } else if (type === 'pregunta2') {
                chatBox.innerHTML += `
                    <div class="flex items-start justify-end space-x-2 chat-bubble-enter">
                        <div class="bg-brand-purple text-white p-3 rounded-2xl rounded-tr-none max-w-[85%] text-sm">
                            ¿Qué pasa si le echo 10 cucharadas de azúcar a mi té frío? ☕
                        </div>
                        <span class="text-xl">👧</span>
                    </div>
                    <div class="flex items-start space-x-2 chat-bubble-enter">
                        <span class="text-xl">👨‍🏫</span>
                        <div class="bg-slate-800 text-slate-200 p-3 rounded-2xl rounded-tl-none max-w-[85%] text-sm">
                            ¡Llegarás al punto de <b>Saturación</b>! El agua ya no podrá disolver más y el azúcar sobrante quedará en el fondo como precipitado. 🧊
                        </div>
                    </div>
                `;
            }
            chatBox.scrollTop = chatBox.scrollHeight;
        }

        function setSolubilityState(state) {
            playSound('click');
            const liquid = document.getElementById('stateLiquid');
            const crystals = document.getElementById('crystalsBottom');
            const badge = document.getElementById('stateBadge');

            if (state === 'insaturada') {
                liquid.style.height = '40%';
                liquid.className = 'absolute bottom-0 w-full bg-cyan-500/30 border-t-2 border-cyan-400 transition-all duration-500';
                crystals.classList.add('opacity-0');
                badge.innerHTML = `Insaturada (No saturada) 🟢<br><span class="text-[10px] text-slate-400 font-normal">Poco soluto, aún se disuelve fácilmente</span>`;
            } else if (state === 'saturada') {
                liquid.style.height = '65%';
                liquid.className = 'absolute bottom-0 w-full bg-cyan-500/60 border-t-2 border-cyan-300 transition-all duration-500';
                crystals.classList.add('opacity-0');
                badge.innerHTML = `Saturada 🟡<br><span class="text-[10px] text-slate-400 font-normal">Cantidad máxima exacta de soluto disuelto</span>`;
            } else if (state === 'sobresaturada') {
                liquid.style.height = '85%';
                liquid.className = 'absolute bottom-0 w-full bg-cyan-600/90 border-t-2 border-cyan-200 transition-all duration-500';
                crystals.classList.remove('opacity-0');
                badge.innerHTML = `Sobresaturada 🔴<br><span class="text-[10px] text-slate-400 font-normal">Exceso de soluto: no se disuelve más y precipita</span>`;
            }
        }

        /* ===================================================
           CAPÍTULO 2: CARDS 3D Y CALCULADORA
        =================================================== */
        function toggleFlip(card) {
            playSound('click');
            card.classList.toggle('flipped');
        }

        function updateLabCalculator() {
            const solute = parseFloat(document.getElementById('soluteRange').value);
            const solvent = parseFloat(document.getElementById('solventRange').value);

            document.getElementById('soluteValDisplay').innerText = `${solute} g`;
            document.getElementById('solventValDisplay').innerText = `${solvent} g`;

            const totalSolution = solute + solvent;
            const percentage = (solute / totalSolution) * 100;

            document.getElementById('totalMassDisplay').innerText = `${totalSolution.toFixed(1)} g`;
            document.getElementById('calcResultDisplay').innerText = `${percentage.toFixed(1)} %`;

            // Animate liquid height and color opacity based on concentration
            const fluid = document.getElementById('tubeFluid');
            const heightPercent = Math.min(Math.max((totalSolution / 400) * 100, 15), 95);
            fluid.style.height = `${heightPercent}%`;

            // Higher concentration -> deeper pink/red opacity
            const opacity = Math.min(0.2 + (percentage / 50), 1.0);
            fluid.style.backgroundColor = `rgba(236, 72, 153, ${opacity})`;
        }

        /* ===================================================
           CAPÍTULO 3: LA PISTA DE BAILE
        =================================================== */
        let dancersCount = 6;
        let floorSize = 100;

        function changeDancers(delta) {
            playSound('click');
            dancersCount = Math.max(2, Math.min(30, dancersCount + delta));
            renderDanceFloor();
        }

        function changeDanceFloorSize(delta) {
            playSound('click');
            floorSize = Math.max(40, Math.min(180, floorSize + delta));
            renderDanceFloor();
        }

        function renderDanceFloor() {
            document.getElementById('dancerCountDisplay').innerText = dancersCount;
            document.getElementById('floorSizeDisplay').innerText = `${floorSize} m²`;

            const floorArea = document.getElementById('danceFloorArea');
            floorArea.style.width = `${Math.min(100, (floorSize / 180) * 100)}%`;
            floorArea.style.height = `${Math.min(100, (floorSize / 180) * 100)}%`;

            // Density calculation
            const density = dancersCount / floorSize;
            const badge = document.getElementById('danceStatusBadge');

            if (density < 0.08) {
                badge.className = 'inline-block px-3 py-1 rounded-xl font-bold text-sm bg-emerald-500/20 text-emerald-400 border border-emerald-500/30';
                badge.innerHTML = 'Diluida 🟢 (Mucha pista, pocos bailarines)';
            } else if (density < 0.16) {
                badge.className = 'inline-block px-3 py-1 rounded-xl font-bold text-sm bg-yellow-500/20 text-yellow-400 border border-yellow-500/30';
                badge.innerHTML = 'Concentrada 🟡 (Fiesta animada y equilibrada)';
            } else if (density < 0.24) {
                badge.className = 'inline-block px-3 py-1 rounded-xl font-bold text-sm bg-orange-500/20 text-orange-400 border border-orange-500/30';
                badge.innerHTML = 'Saturada 🟠 (Pista completamente llena)';
            } else {
                badge.className = 'inline-block px-3 py-1 rounded-xl font-bold text-sm bg-rose-500/20 text-rose-400 border border-rose-500/30';
                badge.innerHTML = 'Sobresaturada 🔴 (¡No cabe un alma más!)';
            }

            // Populate Emojis
            const emojis = ['💃', '🕺', '👯‍♂️', '👯‍♀️', '🕺✨', '💃🎉'];
            let html = '';
            for (let i = 0; i < dancersCount; i++) {
                const em = emojis[i % emojis.length];
                html += `<span class="text-2xl animate-bounce hover:scale-150 transition-transform cursor-pointer select-none">${em}</span>`;
            }
            floorArea.innerHTML = html;
        }

        /* ===================================================
           CAPÍTULO 4: QUIZ GAMIFICADO
        =================================================== */
        const quizData = [
            {
                q: "Se disuelven 6g de sal en agua hasta formar 80g de disolución total. ¿Cuál es el % p/p?",
                options: ["13.3 %", "7.5 %", "6.0 %", "12.0 %"],
                correct: 1,
                explanation: "¡Correcto! % p/p = (6g / 80g) × 100 = 7.5%"
            },
            {
                q: "¿Qué componente de una disolución se encuentra siempre en menor cantidad?",
                options: ["Disolvente", "Soluto", "Precipitado", "Agua"],
                correct: 1,
                explanation: "¡Exacto! El soluto es el componente en menor proporción."
            },
            {
                q: "El aire que respiramos (Nitrógeno + Oxígeno) es un ejemplo de disolución:",
                options: ["Líquido - Gas", "Sólido - Gas", "Gas - Gas", "Sólido - Sólido"],
                correct: 2,
                explanation: "¡Excelente! Ambos componentes son gases."
            },
            {
                q: "Si agregas tanto soluto que ya no se puede disolver más y queda en el fondo, la solución es:",
                options: ["Insaturada", "Diluida", "Sobresaturada", "Saturada"],
                correct: 2,
                explanation: "¡Muy bien! El exceso no disuelto indica sobresaturación."
            }
        ];

        let currentQuizIndex = 0;

        function loadQuizQuestion() {
            const data = quizData[currentQuizIndex];
            document.getElementById('quizQuestionNum').innerText = `Pregunta ${currentQuizIndex + 1} de ${quizData.length}`;
            document.getElementById('quizQuestionText').innerText = data.q;
            document.getElementById('quizHintBox').classList.add('hidden');
            document.getElementById('quizFeedback').classList.add('hidden');
            document.getElementById('nextQuestionBtn').classList.add('hidden');

            const grid = document.getElementById('quizOptionsGrid');
            grid.innerHTML = '';

            data.options.forEach((opt, idx) => {
                const btn = document.createElement('button');
                btn.className = 'w-full p-4 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-2xl text-left font-bold text-white transition-all text-sm flex items-center justify-between';
                btn.innerHTML = `<span>${opt}</span> <i class="fa-regular fa-circle text-slate-500"></i>`;
                btn.onclick = () => answerQuiz(idx, btn);
                grid.appendChild(btn);
            });
        }

        function toggleQuizHint() {
            playSound('click');
            document.getElementById('quizHintBox').classList.toggle('hidden');
        }

        function answerQuiz(optionIdx, btnElement) {
            const data = quizData[currentQuizIndex];
            const feedback = document.getElementById('quizFeedback');
            const allButtons = document.querySelectorAll('#quizOptionsGrid button');

            // Disable all buttons
            allButtons.forEach(b => b.disabled = true);

            if (optionIdx === data.correct) {
                playSound('correct');
                addXP(50);
                btnElement.className = 'w-full p-4 bg-emerald-600/30 border-2 border-emerald-500 rounded-2xl text-left font-bold text-emerald-300 transition-all text-sm flex items-center justify-between';
                btnElement.querySelector('i').className = 'fa-solid fa-circle-check text-emerald-400';

                feedback.className = 'mt-6 p-4 rounded-2xl text-sm font-bold border bg-emerald-500/10 border-emerald-500/30 text-emerald-300';
                feedback.innerText = data.explanation;
            } else {
                playSound('wrong');
                userLives = Math.max(0, userLives - 1);
                document.getElementById('livesDisplay').innerText = userLives;

                btnElement.className = 'w-full p-4 bg-rose-600/30 border-2 border-rose-500 rounded-2xl text-left font-bold text-rose-300 transition-all text-sm flex items-center justify-between';
                btnElement.querySelector('i').className = 'fa-solid fa-circle-xmark text-rose-400';

                feedback.className = 'mt-6 p-4 rounded-2xl text-sm font-bold border bg-rose-500/10 border-rose-500/30 text-rose-300';
                feedback.innerText = `Incorrecto. ${data.explanation}`;
            }

            feedback.classList.remove('hidden');
            document.getElementById('nextQuestionBtn').classList.remove('hidden');
        }

        function nextQuizQuestion() {
            playSound('click');
            currentQuizIndex++;
            if (currentQuizIndex < quizData.length) {
                loadQuizQuestion();
            } else {
                // Finale Celebration
                if (typeof confetti === 'function') {
                    confetti({
                        particleCount: 100,
                        spread: 70,
                        origin: { y: 0.6 }
                    });
                }
                const container = document.getElementById('quizContainer');
                container.innerHTML = `
                    <div class="text-center py-8 space-y-4">
                        <div class="text-6xl">🏆</div>
                        <h3 class="text-2xl font-black text-white font-heading">¡Misión Cumplida!</h3>
                        <p class="text-slate-300 text-sm">Completaste todos los desafíos de Química de 9º Grado.</p>
                        <div class="inline-block bg-purple-500/20 border border-purple-500/40 px-6 py-3 rounded-2xl text-purple-300 font-bold">
                            Total XP Ganado: ${userXP} XP ⚡
                        </div>
                    </div>
                `;
            }
        }

        /* ===================================================
           GLOSSARY MODAL
        =================================================== */
        function openGlossary() {
            playSound('click');
            document.getElementById('glossaryModal').classList.remove('hidden');
        }

        function closeGlossary() {
            playSound('click');
            document.getElementById('glossaryModal').classList.add('hidden');
        }

        /* INITIALIZATION */
        window.onload = function() {
            updateLabCalculator();
            renderDanceFloor();
            loadQuizQuestion();
        }
    </script>
</body>
</html>
