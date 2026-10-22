<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Forge & Conquer - Huyền Thoại Thợ Rèn</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@700;90…amily=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');

        * {
            font-family: 'Plus Jakarta Sans', sans-serif;
            user-select: none;
            -webkit-user-select: none;
        }

        .font-cinzel {
            font-family: 'Cinzel', serif;
        }

        body {
            background: #0a0b0e;
            color: #d1d5db;
            min-height: 100vh;
            overflow-x: hidden;
            background-image: 
                radial-gradient(circle at 50% 0%, rgba(220, 38, 38, 0.15), transparent 70%),
                radial-gradient(circle at 20% 80%, rgba(217, 119, 6, 0.1), transparent 50%);
        }

        /* Dark Fantasy Card Styling */
        .rpg-panel {
            background: rgba(18, 20, 29, 0.85);
            backdrop-filter: blur(16px);
            border: 1px solid rgba(217, 119, 6, 0.2);
            border-radius: 16px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5), inset 0 1px 1px rgba(255, 255, 255, 0.05);
        }

        .rpg-panel-glow {
            border-color: rgba(245, 158, 11, 0.5);
            box-shadow: 0 0 25px rgba(245, 158, 11, 0.25);
        }

        /* Main Navigation Buttons */
        .main-nav-btn {
            position: relative;
            background: linear-gradient(180deg, #1f2430 0%, #11141c 100%);
            border: 2px solid rgba(255, 255, 255, 0.1);
            color: #9ca3af;
            transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .main-nav-btn:hover {
            border-color: rgba(245, 158, 11, 0.4);
            color: #f3f4f6;
            transform: translateY(-2px);
        }

        .main-nav-btn.active-forge {
            background: linear-gradient(180deg, #b45309 0%, #78350f 100%);
            border-color: #f59e0b;
            color: #ffffff;
            box-shadow: 0 0 20px rgba(245, 158, 11, 0.4);
        }

        .main-nav-btn.active-play {
            background: linear-gradient(180deg, #dc2626 0%, #991b1b 100%);
            border-color: #ef4444;
            color: #ffffff;
            box-shadow: 0 0 20px rgba(239, 68, 68, 0.4);
        }

        /* Rarity Borders & Glows */
        .rarity-common { color: #9ca3af; border-color: #4b5563; }
        .rarity-rare { color: #60a5fa; border-color: #2563eb; }
        .rarity-epic { color: #c084fc; border-color: #9333ea; }
        .rarity-legendary { color: #fbbf24; border-color: #d97706; }

        /* Floating Damage Text */
@keyframes floatUpDamage {
            0% { opacity: 1; transform: translateY(0) scale(0.8); }
            40% { transform: translateY(-30px) scale(1.3); }
            100% { opacity: 0; transform: translateY(-60px) scale(0.9); }
        }

        .damage-popup {
            position: absolute;
            font-weight: 900;
            pointer-events: none;
            animation: floatUpDamage 0.8s ease-out forwards;
            z-index: 50;
            text-shadow: 0 3px 6px rgba(0,0,0,0.9);
        }

        /* Slash & Shake Effects */
        .shake-impact {
            animation: shakeImpactAnim 0.2s cubic-bezier(.36,.07,.19,.97) both;
        }

        @keyframes shakeImpactAnim {
            10%, 90% { transform: translate3d(-3px, 0, 0); }
            20%, 80% { transform: translate3d(5px, 0, 0); }
            30%, 50%, 70% { transform: translate3d(-7px, 0, 0); }
            40%, 60% { transform: translate3d(7px, 0, 0); }
        }

        /* Ember animation particles in background */
        @keyframes emberRise {
            0% { transform: translateY(0) scale(1); opacity: 0.8; }
            100% { transform: translateY(-100vh) scale(0.2); opacity: 0; }
        }

        .ember {
            position: absolute;
            width: 4px;
            height: 4px;
            background: #f97316;
            border-radius: 50%;
            box-shadow: 0 0 8px #f97316;
            pointer-events: none;
            animation: emberRise linear infinite;
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: rgba(10, 11, 14, 0.8); }
        ::-webkit-scrollbar-thumb { background: rgba(245, 158, 11, 0.3); border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: rgba(245, 158, 11, 0.6); }
    </style>
</head>
<body class="p-3 sm:p-5 flex flex-col items-center justify-between relative overflow-x-hidden">

    <!-- Embers Background Effect -->
    <div id="emberContainer" class="fixed inset-0 pointer-events-none z-0"></div>

    <!-- TOP BAR: PLAYER STATS & RESOURCES -->
    <header class="w-full max-w-6xl rpg-panel p-4 mb-4 z-10 border-amber-600/30">
        <div class="flex flex-wrap items-center justify-between gap-4">
            
            <!-- Player Avatar & Stats -->
            <div class="flex items-center gap-3">
                <div class="w-14 h-14 rounded-xl bg-gradient-to-br from-amber-600 via-orange-600 to-red-700 flex items-center justify-center text-white text-2xl shadow-lg shadow-orange-900/40 border border-amber-400/30 relative">
                    <i class="fa-solid fa-shield-cat"></i>
                    <span id="playerLevel" class="absolute -bottom-2 -right-2 bg-red-600 text-white text-[10px] font-extrabold px-2 py-0.5 rounded-full border border-red-400">Lv.1</span>
                </div>
                <div>
<h1 class="text-lg font-extrabold text-amber-400 tracking-wide font-cinzel">THỢ RÈN CHIẾN BING</h1>
                    <div class="flex items-center gap-3 text-xs text-gray-300 mt-1">
                        <span class="text-red-400 font-bold"><i class="fa-solid fa-burst text-xs"></i> <span id="statAtk">10</span> ATK</span>
                        <span class="text-blue-400 font-bold"><i class="fa-solid fa-shield text-xs"></i> <span id="statDef">5</span> DEF</span>
                        <span class="text-emerald-400 font-bold"><i class="fa-solid fa-heart text-xs"></i> <span id="statHp">150</span> HP</span>
                    </div>
                </div>
            </div>

            <!-- Currency & Resources Counter -->
            <div class="flex flex-wrap items-center gap-2 sm:gap-4 text-xs font-bold">
                <div class="bg-black/50 px-3 py-2 rounded-xl border border-amber-500/30 flex items-center gap-2" title="Vàng">
                    <i class="fa-solid fa-coins text-yellow-400 text-base"></i>
                    <div>
                        <span class="text-[9px] text-gray-400 block font-normal">VÀNG</span>
                        <span id="resGold" class="text-white text-sm">0</span>
                    </div>
                </div>
                <div class="bg-black/50 px-3 py-2 rounded-xl border border-amber-800/40 flex items-center gap-2" title="Gỗ Linh Thạch">
                    <i class="fa-solid fa-tree text-amber-600 text-base"></i>
                    <div>
                        <span class="text-[9px] text-gray-400 block font-normal">GỖ</span>
                        <span id="resWood" class="text-white text-sm">0</span>
                    </div>
                </div>
                <div class="bg-black/50 px-3 py-2 rounded-xl border border-slate-600/40 flex items-center gap-2" title="Sắt Cổ Đại">
                    <i class="fa-solid fa-cubes text-slate-300 text-base"></i>
                    <div>
                        <span class="text-[9px] text-gray-400 block font-normal">SẮT</span>
                        <span id="resIron" class="text-white text-sm">0</span>
                    </div>
                </div>
                <div class="bg-black/50 px-3 py-2 rounded-xl border border-purple-500/40 flex items-center gap-2" title="Đá Linh Hồn">
                    <i class="fa-solid fa-gem text-purple-400 text-base"></i>
                    <div>
                        <span class="text-[9px] text-gray-400 block font-normal">ĐÁ HỒN</span>
                        <span id="resSoul" class="text-white text-sm">0</span>
                    </div>
                </div>
            </div>

        </div>
    </header>

    <!-- CLEAR DUAL MAIN NAVIGATION BAR -->
    <nav class="w-full max-w-6xl grid grid-cols-2 gap-3 sm:gap-6 mb-5 z-10">
<button id="mainBtnForge" onclick="showMainScreen('forge')" class="main-nav-btn active-forge py-4 px-4 sm:px-8 rounded-2xl font-black text-sm sm:text-lg tracking-wider uppercase flex items-center justify-center gap-3 transition">
            <i class="fa-solid fa-hammer text-xl sm:text-2xl text-amber-400"></i>
            <span>🔨 RÈN VŨ KHÍ</span>
        </button>

        <button id="mainBtnPlay" onclick="showMainScreen('play')" class="main-nav-btn py-4 px-4 sm:px-8 rounded-2xl font-black text-sm sm:text-lg tracking-wider uppercase flex items-center justify-center gap-3 transition">
            <i class="fa-solid fa-dragon text-xl sm:text-2xl text-red-500"></i>
            <span>⚔️ PLAY / CHIẾN ĐẤU</span>
        </button>
    </nav>

    <!-- SCREEN 1: PAGE RÈN VŨ KHÍ & KHO TRANG BỊ -->
    <main id="pageForge" class="w-full max-w-6xl z-10 grid grid-cols-1 lg:grid-cols-12 gap-5 mb-5">
        
        <!-- Left Column: Resource Mining & Upgrades -->
        <div class="lg:col-span-4 space-y-4">
            <!-- Manual Mining Card -->
            <div class="rpg-panel p-5">
                <h2 class="text-sm font-extrabold text-amber-400 uppercase tracking-wider mb-3 flex items-center gap-2 font-cinzel">
                    <i cl
