<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Forge & Conquer 3D - Evolution & Leviathan Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@700;900&family=Inter:wght@400;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; background: #030712; color: #f8fafc; overflow-x: hidden; }
        .font-cinzel { font-family: 'Cinzel', serif; }
        .shake { animation: shake 0.3s cubic-bezier(.36,.07,.19,.97) both; }
        @keyframes shake {
            10%, 90% { transform: translate3d(-4px, 0, 0); }
            20%, 80% { transform: translate3d(8px, 0, 0); }
            30%, 50%, 70% { transform: translate3d(-12px, 0, 0); }
            40%, 60% { transform: translate3d(12px, 0, 0); }
        }
        .damage-pop {
            position: absolute; animation: floatUp 0.85s cubic-bezier(0.25, 1, 0.5, 1) forwards;
            pointer-events: none; font-weight: 900; text-shadow: 0 0 15px rgba(239, 68, 68, 0.9); z-index: 50;
        }
        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0) scale(0.6); }
            40% { opacity: 1; transform: translateY(-45px) scale(1.4); }
            100% { opacity: 0; transform: translateY(-90px) scale(1); }
        }
        .tab-btn-active {
            background: linear-gradient(135deg, #b91c1c 0%, #7f1d1d 100%);
            border-color: #f87171; box-shadow: 0 0 25px rgba(248, 113, 113, 0.6); color: #ffffff;
        }
        .rarity-Common { border-color: #64748b; }
        .rarity-Rare { border-color: #3b82f6; box-shadow: 0 0 12px rgba(59, 130, 246, 0.5); }
        .rarity-Epic { border-color: #a855f7; box-shadow: 0 0 16px rgba(168, 85, 247, 0.6); }
        .rarity-Legendary { border-color: #eab308; box-shadow: 0 0 20px rgba(234, 179, 8, 0.7); }
        .rarity-Mythic { border-color: #f43f5e; box-shadow: 0 0 25px rgba(244, 63, 94, 0.8); }
        .rarity-text-Common { color: #cbd5e1; }
        .rarity-text-Rare { color: #93c5fd; }
        .rarity-text-Epic { color: #d8b4fe; }
        .rarity-text-Legendary { color: #fde047; }
        .rarity-text-Mythic { color: #fda4af; }
        .glass-panel {
            background: linear-gradient(135deg, rgba(30, 41, 59, 0.95) 0%, rgba(15, 23, 42, 0.98) 100%);
            backdrop-filter: blur(16px); border: 1px solid rgba(245, 158, 11, 0.4); box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
        }
        .menu-bg { background: radial-gradient(circle at center, #1e293b 0%, #020617 75%); }
        .gothic-border {
            border: 2px solid rgba(245, 158, 11, 0.7);
            box-shadow: 0 0 35px rgba(245, 158, 11, 0.3), inset 0 0 20px rgba(245, 158, 11, 0.2);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col items-center justify-between p-2 sm:p-6 bg-[#030712]">

    <!-- MENU BẮT ĐẦU -->
    <div id="startMenu" class="fixed inset-0 z-50 menu-bg flex flex-col items-center justify-center p-6 transition-opacity duration-700">
        <div class="absolute inset-0 bg-[radial-gradient(ellipse_at_top,_var(--tw-gradient-stops))] from-amber-500/20 via-transparent to-slate-950 pointer-events-none"></div>
        <div class="z-10 text-center max-w-xl mx-auto space-y-6 gothic-border p-8 rounded-3xl bg-slate-900/95 backdrop-blur-xl">
            <div class="inline-block p-4 bg-amber-900/60 border border-amber-400 rounded-3xl shadow-[0_0_35px_rgba(245,158,11,0.5)] mb-2 animate-bounce">
                <span class="text-5xl">⚔️</span>
            </div>
            <h1 class="font-cinzel text-3xl sm:text-5xl font-black text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 via-amber-300 to-rose-500 tracking-wider">
                FORGE & CONQUER 3D
            </h1>
            <p class="text-slate-300 text-xs sm:text-sm max-w-md mx-auto italic">
                Cập nhật: Nhân vật đối mặt Boss, Hiệu ứng Skill 3D, Boss Leviathan & Tiến hóa NewGame+!
            </p>
            <div class="pt-4 space-y-3 w-full max-w-sm mx-auto">
                <button onclick="startAction('battle')" class="w-full py-3.5 bg-gradient-to-r from-red-600 to-rose-600 hover:from-red-500 hover:to-rose-500 font-cinzel font-bold text-base rounded-2xl text-white shadow-[0_0_20px_rgba(239,68,68,0.6)] active:scale-95 transition tracking-wider border border-red-400 flex items-center justify-center gap-3">
                    <span>⚔️</span> ĐẤU TRƯỜNG BOSS 3D
                </button>
                <button onclick="startAction('stats')" class="w-full py-3.5 bg-gradient-to-r from-purple-600 to-indigo-600 hover:from-purple-500 hover:to-indigo-500 font-cinzel font-bold text-base rounded-2xl text-white shadow-[0_0_20px_rgba(168,85,247,0.6)] active:scale-95 transition tracking-wider border border-purple-400 flex items-center justify-center gap-3">
                    <span>⚡</span> NÂNG CẤP & KỸ NĂNG
                </button>
                <button onclick="startAction('forge')" class="w-full py-3.5 bg-gradient-to-r from-amber-600 to-amber-500 hover:from-amber-500 hover:to-amber-400 font-cinzel font-bold text-base rounded-2xl text-white shadow-[0_0_20px_rgba(217,119,6,0.6)] active:scale-95 transition tracking-wider border border-amber-400 flex items-center justify-center gap-3">
                    <span>🔥</span> PHÒNG RÈN BẢO KIẾM
                </button>
                <button onclick="startAction('gather')" class="w-full py-3.5 bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-500 hover:to-teal-500 font-cinzel font-bold text-base rounded-2xl text-white shadow-[0_0_20px_rgba(16,185,129,0.6)] active:scale-95 transition tracking-wider border border-emerald-400 flex items-center justify-center gap-3">
                    <span>⛏️</span> KHU KHAI THÁC QUẶNG
                </button>
                <div class="pt-2">
                    <button onclick="resetData()" class="w-full py-2 bg-slate-800 hover:bg-slate-700 font-semibold text-xs rounded-xl text-slate-300 border border-slate-600 transition">
                        🔄 Xóa Dữ Liệu Lưu Cũ
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- HEADER GIAO DIỆN -->
    <header class="w-full max-w-5xl glass-panel rounded-2xl p-4 mb-4 shadow-2xl flex flex-wrap justify-between items-center gap-4">
        <div class="flex items-center gap-3">
            <div class="w-12 h-12 bg-red-900 border border-red-500 rounded-xl flex items-center justify-center text-2xl shadow-[0_0_15px_rgba(239,68,68,0.4)] cursor-pointer hover:bg-red-800 transition" onclick="returnToMenu()" title="Về Menu Chính">
                🏠
            </div>
            <div>
                <h1 class="font-cinzel text-xl font-bold text-amber-400 tracking-wider">FORGE & CONQUER 3D</h1>
                <p class="text-xs text-slate-300">Dạng Tiến Hóa Boss: <span id="bossEvoLvl" class="text-cyan-300 font-bold">Dạng 1 (Gốc)</span></p>
            </div>
        </div>
        <div class="flex flex-wrap gap-2.5 text-xs sm:text-sm">
            <div class="bg-slate-900/90 px-3 py-1.5 rounded-xl border border-slate-700 flex items-center gap-1.5 shadow-inner">
                <span>💰 Vàng:</span> <span id="resGold" class="text-yellow-300 font-bold">0</span>
            </div>
            <div class="bg-slate-900/90 px-3 py-1.5 rounded-xl border border-slate-700 flex items-center gap-1.5 shadow-inner">
                <span>🪵 Gỗ:</span> <span id="resWood" class="text-emerald-300 font-bold">0</span>
            </div>
            <div class="bg-slate-900/90 px-3 py-1.5 rounded-xl border border-slate-700 flex items-center gap-1.5 shadow-inner">
                <span>🪨 Sắt:</span> <span id="resIron" class="text-slate-200 font-bold">0</span>
            </div>
            <div class="bg-slate-900/90 px-3 py-1.5 rounded-xl border border-slate-700 flex items-center gap-1.5 shadow-inner">
                <span>🔮 Đá Hồn:</span> <span id="resSoul" class="text-purple-300 font-bold">0</span>
            </div>
        </div>
    </header>

    <!-- TAB NAV -->
    <nav class="w-full max-w-5xl flex gap-2 mb-4">
        <button id="tabBattleBtn" onclick="switchTab('battle')" class="flex-1 py-3 px-2 rounded-xl font-cinzel font-bold text-xs sm:text-sm border border-slate-600 bg-slate-900 transition flex items-center justify-center gap-1 text-slate-300 shadow-lg">
            <span>⚔️</span> ĐẤU TRƯỜNG 3D
        </button>
        <button id="tabStatsBtn" onclick="switchTab('stats')" class="flex-1 py-3 px-2 rounded-xl font-cinzel font-bold text-xs sm:text-sm border border-slate-600 bg-slate-900 transition flex items-center justify-center gap-1 text-slate-300 shadow-lg">
            <span>⚡</span> NÂNG CẤP
        </button>
        <button id="tabForgeBtn" onclick="switchTab('forge')" class="flex-1 py-3 px-2 rounded-xl font-cinzel font-bold text-xs sm:text-sm border border-slate-600 bg-slate-900 transition flex items-center justify-center gap-1 text-slate-300 shadow-lg">
            <span>🔥</span> RÈN KIẾM
        </button>
        <button id="tabGatherBtn" onclick="switchTab('gather')" class="flex-1 py-3 px-2 rounded-xl font-cinzel font-bold text-xs sm:text-sm border border-slate-600 bg-slate-900 transition flex items-center justify-center gap-1 text-slate-300 shadow-lg">
            <span>⛏️</span> KHAI THÁC
        </button>
    </nav>

    <main class="w-full max-w-5xl flex-1 flex flex-col">
        
        <!-- MÀN HÌNH 1: ĐẤU TRƯỜNG 3D -->
        <section id="pageBattle" class="flex-1 flex-col gap-4">
            <div class="glass-panel rounded-2xl p-3 flex flex-wrap items-center justify-between gap-2 shadow-lg">
                <span class="text-xs text-slate-300 font-bold">CHỌN MÀN CHƠI (6 BOSS):</span>
                <div class="flex gap-1.5 flex-wrap" id="dungeonButtons">
                    <button onclick="selectDungeon(0)" class="btn-dungeon border border-emerald-500 bg-emerald-950/70 text-emerald-200 text-xs px-2.5 py-1 rounded-xl font-semibold hover:bg-emerald-900 transition">Màn 1: Wukong</button>
                    <button onclick="selectDungeon(1)" class="btn-dungeon border border-slate-400 bg-slate-800/70 text-slate-200 text-xs px-2.5 py-1 rounded-xl font-semibold hover:bg-slate-700 transition">Màn 2: Titan</button>
                    <button onclick="selectDungeon(2)" class="btn-dungeon border border-cyan-500 bg-cyan-950/70 text-cyan-200 text-xs px-2.5 py-1 rounded-xl font-semibold hover:bg-cyan-900 transition">Màn 3: Leviathan 🌊</button>
                    <button onclick="selectDungeon(3)" class="btn-dungeon border border-red-500 bg-red-950/70 text-red-200 text-xs px-2.5 py-1 rounded-xl font-semibold hover:bg-red-900 transition">Màn 4: Hỏa Long</button>
                    <button onclick="selectDungeon(4)" class="btn-dungeon border border-blue-500 bg-blue-950/70 text-blue-200 text-xs px-2.5 py-1 rounded-xl font-semibold hover:bg-blue-900 transition">Màn 5: Băng Vương</button>
                    <button onclick="selectDungeon(5)" class="btn-dungeon border border-purple-500 bg-purple-950/70 text-purple-200 text-xs px-2.5 py-1 rounded-xl font-semibold hover:bg-purple-900 transition">Màn 6: Ma Vương</button>
                </div>
            </div>

            <div id="battleArena" class="relative glass-panel rounded-2xl p-4 flex-1 flex flex-col items-center justify-between min-h-[460px] overflow-hidden shadow-2xl">
                <div class="text-center z-10 flex items-center justify-center gap-4">
                    <div>
                        <span id="dungeonLevelBadge" class="text-xs font-bold uppercase tracking-widest text-amber-400 bg-amber-950/80 px-2.5 py-1 rounded-full border border-amber-600/50">MÀN 1</span>
                        <h3 id="monsterName" class="text-xl font-bold font-cinzel text-white mt-1">Hầu Vương Tôn Ngộ Không</h3>
                    </div>
                </div>

                <div class="relative my-2 w-full max-w-xl h-72 flex flex-col items-center justify-center">
                    <div id="battle3DCanvas" class="w-full h-full rounded-2xl border border-slate-700 shadow-[inset_0_0_40px_rgba(0,0,0,0.8)] bg-slate-950"></div>
                    
                    <div class="w-full bg-slate-950 rounded-full h-4 p-0.5 border border-slate-700 overflow-hidden shadow-inner mt-2">
                        <div id="monsterHpBar" class="bg-gradient-to-r from-red-600 to-rose-500 h-full rounded-full transition-all duration-200" style="width: 100%;"></div>
                    </div>
                    <div id="monsterHpText" class="text-xs text-red-300 font-bold mt-1">200 / 200 HP</div>
                </div>

                <!-- CONTROL & SKILLS -->
                <div class="w-full max-w-xl flex flex-col gap-2 z-10">
                    <div class="grid grid-cols-3 gap-2" id="skillActionButtons">
                        <button id="btnSkill1" onclick="useSkill(1)" class="py-2 bg-gradient-to-r from-orange-600 to-red-600 hover:from-orange-500 hover:to-red-500 font-bold rounded-xl text-white text-xs border border-orange-400 transition shadow">
                            🔥 Bão Lửa (CD: <span id="cdSkill1">0</span>s)
                        </button>
                        <button id="btnSkill2" onclick="useSkill(2)" class="py-2 bg-gradient-to-r from-cyan-600 to-blue-600 hover:from-cyan-500 hover:to-blue-500 font-bold rounded-xl text-white text-xs border border-cyan-400 transition shadow">
                            ⚡ Cuồng Bạo (CD: <span id="cdSkill2">0</span>s)
                        </button>
                        <button id="btnSkill3" onclick="useSkill(3)" class="py-2 bg-gradient-to-r from-purple-600 to-pink-600 hover:from-purple-500 hover:to-pink-500 font-bold rounded-xl text-white text-xs border border-purple-400 transition shadow">
                            💀 Kết Liễu (CD: <span id="cdSkill3">0</span>s)
                        </button>
                    </div>

                    <div class="flex gap-2">
                        <button onclick="heroAttack()" class="flex-1 py-3 bg-gradient-to-r from-red-600 to-rose-600 hover:from-red-500 hover:to-rose-500 font-bold rounded-xl text-white shadow-lg shadow-red-600/40 active:scale-95 transition flex items-center justify-center gap-2 text-sm">
                            <span>⚔️</span> VUNG KIẾM CHÉM (Space)
                        </button>
                        <button id="btnAutoAttack" onclick="toggleAutoAttack()" class="py-3 px-4 bg-slate-800 border border-slate-600 hover:bg-slate-700 font-semibold rounded-xl text-slate-200 transition text-xs">
                            🤖 Tự Động: OFF
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- MÀN HÌNH 2: NÂNG CẤP CHỈ SỐ & KỸ NĂNG -->
        <section id="pageStats" class="hidden grid-cols-1 md:grid-cols-2 gap-5 flex-1">
            <div class="glass-panel rounded-2xl p-5 flex flex-col gap-4 shadow-2xl">
                <h2 class="text-lg font-bold text-amber-400 border-b border-slate-700 pb-2.5 flex items-center gap-2">
                    <span>⚡ Nâng Cấp Chỉ Số Nhân Vật (Dùng Vàng)</span>
                </h2>
                <div class="space-y-3 flex-1 overflow-y-auto pr-1">
                    <div class="p-3 bg-slate-900/80 rounded-xl border border-slate-700 flex items-center justify-between">
                        <div>
                            <div class="font-bold text-sm text-slate-200">🗡️ Sức Mạnh Tấn Công (ATK)</div>
                            <div class="text-xs text-slate-400">Tăng +8 Sát Thương Gốc (Cấp <span id="statAtkLvl">0</span>)</div>
                        </div>
                        <button onclick="upgradeStat('atk')" class="py-2 px-3 bg-amber-600 hover:bg-amber-500 font-bold text-xs rounded-lg text-white transition border border-amber-400">
                            +8 ATK (<span id="costStatAtk">200</span>G)
                        </button>
                    </div>
                    <div class="p-3 bg-slate-900/80 rounded-xl border border-slate-700 flex items-center justify-between">
                        <div>
                            <div class="font-bold text-sm text-slate-200">🎯 Tỉ Lệ Chí Mạng (CRIT %)</div>
                            <div class="text-xs text-slate-400">Tăng +2% Tỉ lệ bạo kích (Cấp <span id="statCritLvl">0</span>)</div>
                        </div>
                        <button onclick="upgradeStat('crit')" class="py-2 px-3 bg-amber-600 hover:bg-amber-500 font-bold text-xs rounded-lg text-white transition border border-amber-400">
                            +2% CRIT (<span id="costStatCrit">400</span>G)
                        </button>
                    </div>
                    <div class="p-3 bg-slate-900/80 rounded-xl border border-slate-700 flex items-center justify-between">
                        <div>
                            <div class="font-bold text-sm text-slate-200">💰 Thưởng Tiêu Diệt Boss</div>
                            <div class="text-xs text-slate-400">Tăng +15% Vàng thưởng (Cấp <span id="statGoldLvl">0</span>)</div>
                        </div>
                        <button onclick="upgradeStat('gold')" class="py-2 px-3 bg-amber-600 hover:bg-amber-500 font-bold text-xs rounded-lg text-white transition border border-amber-400">
                            +15% Vàng (<span id="costStatGold">300</span>G)
                        </button>
                    </div>
                </div>
            </div>

            <div class="glass-panel rounded-2xl p-5 flex flex-col gap-4 shadow-2xl">
                <h2 class="text-lg font-bold text-purple-400 border-b border-slate-700 pb-2.5 flex items-center gap-2">
                    <span>🔮 Kỹ Năng Đặc Biệt OP (Dùng Đá Hồn)</span>
                </h2>
                <div class="space-y-3 flex-1 overflow-y-auto pr-1">
                    <div class="p-3 bg-slate-900/80 rounded-xl border border-purple-900/60 flex items-center justify-between">
                        <div>
                            <div class="font-bold text-sm text-orange-400">🔥 Trảm Kiếm Bão Lửa</div>
                            <div class="text-xs text-slate-400">Gây x<span id="skill1Mult">4</span> sát thương bão lửa (Cấp <span id="skill1Lvl">1</span>)</div>
                        </div>
                        <button onclick="upgradeSkill(1)" class="py-2 px-3 bg-purple-700 hover:bg-purple-600 font-bold text-xs rounded-lg text-white transition border border-purple-400">
                            Cường Hóa (<span id="costSkill1">10</span> 🔮)
                        </button>
                    </div>
                    <div class="p-3 bg-slate-900/80 rounded-xl border border-purple-900/60 flex items-center justify-between">
                        <div>
                            <div class="font-bold text-sm text-cyan-400">⚡ Cuồng Bạo Vũ Trụ</div>
                            <div class="text-xs text-slate-400">100% Chí Mạng trong <span id="skill2Dur">5</span> giây (Cấp <span id="skill2Lvl">1</span>)</div>
                        </div>
                        <button onclick="upgradeSkill(2)" class="py-2 px-3 bg-purple-700 hover:bg-purple-600 font-bold text-xs rounded-lg text-white transition border border-purple-400">
                            Cường Hóa (<span id="costSkill2">15</span> 🔮)
                        </button>
                    </div>
                    <div class="p-3 bg-slate-900/80 rounded-xl border border-purple-900/60 flex items-center justify-between">
                        <div>
                            <div class="font-bold text-sm text-pink-400">💀 Tiêu Diệt Tức Thì</div>
                            <div class="text-xs text-slate-400">Trảm trực tiếp <span id="skill3Pct">10</span>% Max HP Boss (Cấp <span id="skill3Lvl">1</span>)</div>
                        </div>
                        <button onclick="upgradeSkill(3)" class="py-2 px-3 bg-purple-700 hover:bg-purple-600 font-bold text-xs rounded-lg text-white transition border border-purple-400">
                            Cường Hóa (<span id="costSkill3">25</span> 🔮)
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- MÀN HÌNH 3: RÈN VŨ KHÍ -->
        <section id="pageForge" class="hidden grid-cols-1 md:grid-cols-2 gap-5 flex-1">
            <div class="glass-panel rounded-2xl p-5 flex flex-col gap-4 shadow-2xl">
                <h2 class="text-lg font-bold text-amber-400 border-b border-slate-700 pb-2.5 flex items-center gap-2">
                    <span>⚔️ Bàn Rèn Bảo Kiếm (Chuẩn Kiếm 3D)</span>
                </h2>
                <div class="relative w-full h-56 bg-slate-900/90 rounded-xl border border-slate-700 overflow-hidden flex items-center justify-center shadow-[inset_0_0_30px_rgba(0,0,0,0.5)]">
                    <div id="weapon3DCanvas" class="w-full h-full"></div>
                    <div class="absolute bottom-2 left-3 text-[10px] text-amber-300 font-semibold bg-slate-950/80 px-2 py-0.5 rounded border border-amber-500/40">
                        ✨ Mô Hình Kiếm Chuẩn Xác 3D
                    </div>
                </div>
                <div class="space-y-3">
                    <label class="text-xs text-slate-300 block font-semibold">Chọn Kiếm Cần Rèn:</label>
                    <div id="recipeList" class="grid grid-cols-1 gap-2 max-h-44 overflow-y-auto pr-1"></div>
                    <button id="btnForgeAction" onclick="forgeWeapon()" class="w-full py-3.5 bg-gradient-to-r from-amber-600 to-amber-500 font-bold rounded-xl text-white shadow-lg active:scale-98 transition">
                        ⚒️ RÈN BẢO KIẾM
                    </button>
                </div>
            </div>
            <div class="glass-panel rounded-2xl p-5 flex flex-col gap-3 shadow-2xl">
                <h2 class="text-lg font-bold text-amber-400 border-b border-slate-700 pb-2.5 flex justify-between items-center">
                    <span>🎒 Kho Trang Bị</span> <span class="text-xs text-slate-400" id="inventoryCount">0/12</span>
                </h2>
                <div id="inventoryGrid" class="grid grid-cols-1 gap-2.5 flex-1 max-h-[460px] overflow-y-auto pr-1"></div>
            </div>
        </section>

        <!-- MÀN HÌNH 4: KHAI THÁC -->
        <section id="pageGather" class="hidden flex-1 flex-col items-center justify-center gap-6 glass-panel rounded-2xl p-8 shadow-2xl">
            <div class="text-center space-y-2">
                <span class="text-xs text-emerald-400 font-bold tracking-widest uppercase">MỎ NGUYÊN LIỆU HẮC ÁM</span>
                <h2 class="text-3xl font-bold font-cinzel text-white">KHU KHAI THÁC 3D</h2>
                <p class="text-xs text-slate-300 max-w-md mx-auto">Vung cuốc vào tảng quặng ma thuật để thu thập nguyên liệu!</p>
            </div>
            <div class="relative w-full max-w-md h-64 bg-slate-900/90 rounded-2xl border border-emerald-800/80 flex flex-col items-center justify-center p-6 shadow-inner overflow-hidden cursor-pointer" onclick="performGather()" id="gatherZone">
                <div id="ore3DContainer" class="w-full h-full absolute inset-0"></div>
                <div class="absolute bottom-3 text-xs font-semibold text-emerald-300 bg-slate-950/80 px-3 py-1 rounded-full border border-emerald-500/50 animate-pulse pointer-events-none">
                    CLICK ĐỂ ĐÀO QUẶNG!
                </div>
            </div>
            <button onclick="performGather()" class="w-full max-w-md py-4 bg-gradient-to-r from-emerald-600 to-teal-600 font-cinzel font-bold text-lg rounded-2xl text-white shadow-lg active:scale-95 transition">
                ⛏️ VUNG CUỐC KHAI THÁC
            </button>
        </section>

    </main>

    <footer class="w-full max-w-5xl mt-4 glass-panel rounded-xl p-3 text-xs text-slate-300 flex items-center justify-between shadow-lg">
        <span class="font-semibold text-amber-400">📜 Nhật Ký:</span>
        <span id="gameLog" class="truncate max-w-md text-right text-slate-200">Bắt đầu chinh phục 6 Chúa Tể Hắc Ám!</span>
    </footer>

    <!-- MODAL TIẾN HOÁ NEWGAME+ -->
    <div id="evoModal" class="fixed inset-0 z-50 bg-slate-950/90 flex flex-col items-center justify-center p-6 hidden">
        <div class="max-w-md w-full glass-panel p-6 rounded-3xl text-center space-y-5 border border-cyan-400 shadow-[0_0_40px_rgba(6,182,212,0.5)]">
            <h2 class="text-2xl font-bold font-cinzel text-cyan-300">👑 CHIẾN THẮNG TOÀN BỘ MÀN CHƠI!</h2>
            <p class="text-xs text-slate-300 leading-relaxed">
                Bạn đã tiêu diệt sạch 6 Chúa Tể Hắc Ám bao gồm cả Thủy Tộc Leviathan! Bạn muốn làm gì tiếp theo?
            </p>
            <div class="flex flex-col gap-3 pt-2">
                <button onclick="chooseEvoOption('evolve')" class="w-full py-3 bg-gradient-to-r from-cyan-600 to-blue-600 hover:from-cyan-500 hover:to-blue-500 font-bold rounded-xl text-white text-sm border border-cyan-300 shadow-lg">
                    🧬 TIẾN HOÁ BOSS LÊN DẠNG MỚI (+50% HP & Vàng)
                </button>
                <button onclick="chooseEvoOption('reset')" class="w-full py-3 bg-slate-800 hover:bg-slate-700 font-semibold rounded-xl text-slate-200 text-sm border border-slate-600">
                    🔄 LÀM MỚI TỪ MÀN 1 (Dạng Gốc)
                </button>
            </div>
        </div>
    </div>

    <script>
        let audioCtx = null;
        function initAudio() {
            if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            if (audioCtx.state === 'suspended') audioCtx.resume();
        }

        function playSound(type) {
            try {
                initAudio();
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain); gain.connect(audioCtx.destination);
                if (type === 'hit') {
                    osc.type = 'sawtooth'; osc.frequency.setValueAtTime(140, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(30, audioCtx.currentTime + 0.1);
                    gain.gain.setValueAtTime(0.3, audioCtx.currentTime); gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.1);
                    osc.start(); osc.stop(audioCtx.currentTime + 0.1);
                } else if (type === 'crit') {
                    osc.type = 'square'; osc.frequency.setValueAtTime(400, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(900, audioCtx.currentTime + 0.15);
                    gain.gain.setValueAtTime(0.4, audioCtx.currentTime); gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);
                    osc.start(); osc.stop(audioCtx.currentTime + 0.15);
                } else if (type === 'skill') {
                    osc.type = 'sawtooth'; osc.frequency.setValueAtTime(250, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(1000, audioCtx.currentTime + 0.35);
                    gain.gain.setValueAtTime(0.5, audioCtx.currentTime); gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.35);
                    osc.start(); osc.stop(audioCtx.currentTime + 0.35);
                } else if (type === 'win') {
                    osc.type = 'triangle'; osc.frequency.setValueAtTime(440, audioCtx.currentTime);
                    osc.frequency.setValueAtTime(659.25, audioCtx.currentTime + 0.15);
                    gain.gain.setValueAtTime(0.4, audioCtx.currentTime); gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.5);
                    osc.start(); osc.stop(audioCtx.currentTime + 0.5);
                }
            } catch(e) {}
        }

        let player = {
            level: 1, gold: 2000, wood: 200, iron: 200, soul: 50, inventory: [], equippedId: null,
            bossEvoTier: 1, // Dạng tiến hóa của Boss (Tier 1, 2, 3...)
            stats: { atkLvl: 0, critLvl: 0, goldLvl: 0 },
            skills: {
                s1: { lvl: 1, cd: 0, maxCd: 6 },
                s2: { lvl: 1, cd: 0, maxCd: 10 },
                s3: { lvl: 1, cd: 0, maxCd: 15 }
            }
        };

        let tempBerserkActive = false;

        const recipes = [
            { id: 'sword_wood', name: 'Đao Gỗ Thâm Sơn', icon: '🪵', modelType: 'wood', wood: 10, iron: 0, soul: 0, atk: 15, crit: 5, rarity: 'Common' },
            { id: 'sword_iron', name: 'Trường Kiếm Sắt', icon: '🗡️', modelType: 'iron', wood: 15, iron: 15, soul: 0, atk: 40, crit: 10, rarity: 'Rare' },
            { id: 'axe_steel', name: 'Rìu Chiến Thép', icon: '🪓', modelType: 'axe', wood: 15, iron: 30, soul: 3, atk: 95, crit: 15, rarity: 'Epic' },
            { id: 'sword_emerald', name: 'Bảo Kiếm Lục Bảo', icon: '🗡️', modelType: 'emerald', wood: 20, iron: 40, soul: 6, atk: 180, crit: 20, rarity: 'Epic' },
            { id: 'sword_diamond', name: 'Thánh Kiếm Kim Cương', icon: '💎', modelType: 'diamond', wood: 30, iron: 60, soul: 10, atk: 350, crit: 25, rarity: 'Legendary' },
            { id: 'sword_mythic', name: 'Hủy Diệt Kiếm Vô Cực', icon: '👑', modelType: 'mythic', wood: 50, iron: 100, soul: 20, atk: 750, crit: 35, rarity: 'Mythic' }
        ];

        let selectedRecipeId = 'sword_wood';

        // Danh sách 6 Boss (Bổ sung Leviathan ở Màn 3)
        const baseDungeons = [
            { level: 1, name: "Màn 1: Wukong", monster: "Hầu Vương Tôn Ngộ Không", bossType: 'wukong', maxHp: 200, goldReward: 250, woodDrop: 15, ironDrop: 10, soulDrop: 2 },
            { level: 2, name: "Màn 2: Titan", monster: "Titan Thạch Cổ", bossType: 'titan', maxHp: 850, goldReward: 600, woodDrop: 10, ironDrop: 25, soulDrop: 4 },
            { level: 3, name: "Màn 3: Leviathan", monster: "Hải Vương Leviathan Vực Thẳm", bossType: 'leviathan', maxHp: 2200, goldReward: 1200, woodDrop: 8, ironDrop: 35, soulDrop: 7 },
            { level: 4, name: "Màn 4: Hỏa Long", monster: "Hỏa Long Hoàng Giả", bossType: 'dragon', maxHp: 5500, goldReward: 2800, woodDrop: 5, ironDrop: 60, soulDrop: 12 },
            { level: 5, name: "Màn 5: Băng Vương", monster: "Băng Vương Vực Sâu", bossType: 'frostking', maxHp: 15000, goldReward: 7500, woodDrop: 0, ironDrop: 120, soulDrop: 22 },
            { level: 6, name: "Màn 6: Ma Vương", monster: "Ma Vương Tối Thượng", bossType: 'overlord', maxHp: 50000, goldReward: 30000, woodDrop: 25, ironDrop: 300, soulDrop: 60 }
        ];

        let dungeons = [];

        function recalculateDungeons() {
            const mult = Math.pow(1.5, player.bossEvoTier - 1);
            dungeons = baseDungeons.map(b => ({
                ...b,
                maxHp: Math.floor(b.maxHp * mult),
                goldReward: Math.floor(b.goldReward * mult),
                monster: player.bossEvoTier > 1 ? `${b.monster} (Tiến Hóa T${player.bossEvoTier})` : b.monster
            }));
        }

        let currentDungeonIdx = 0;
        let currentMonsterHp = 200;
        let autoAttackInterval = null;

        let battleScene, battleCamera, battleRenderer, heroGroup, bossGroup, fxParticlesGroup;
        let wpScene, wpCamera, wpRenderer, wpMeshGroup;
        let oreScene, oreCamera, oreRenderer, oreMeshGroup, pickaxeMesh, isMiningAnim = false, miningframe = 0;
        let is3DInitialized = false;

        let isHeroAttacking = false;
        let heroAnimFrame = 0;

        function startAction(action) {
            playSound('forge');
            const menu = document.getElementById("startMenu");
            menu.style.opacity = '0';
            setTimeout(() => {
                menu.style.display = 'none';
                if (!is3DInitialized) {
                    init3DScenes();
                    is3DInitialized = true;
                }
                switchTab(action);
            }, 500);
        }

        function returnToMenu() {
            if (autoAttackInterval) { clearInterval(autoAttackInterval); autoAttackInterval = null; }
            const menu = document.getElementById("startMenu");
            menu.style.display = 'flex';
            setTimeout(() => menu.style.opacity = '1', 10);
        }

        function resetData() {
            if (confirm("Xóa toàn bộ dữ liệu lưu và chơi lại từ đầu?")) {
                localStorage.removeItem("nextGenDarkForgeSave");
                location.reload();
            }
        }

        function init3DScenes() {
            // Battle Arena 3D Scene
            const battleContainer = document.getElementById("battle3DCanvas");
            battleScene = new THREE.Scene();

            // Góc camera ngang giúp 2 nhân vật đối mặt nhau trực diện
            battleCamera = new THREE.PerspectiveCamera(40, battleContainer.clientWidth / battleContainer.clientHeight, 0.1, 1000);
            battleCamera.position.set(0, 1.8, 7.2);
            battleCamera.lookAt(0, 0.4, 0);

            battleRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true, powerPreference: "high-performance" });
            battleRenderer.setSize(battleContainer.clientWidth, battleContainer.clientHeight);
            battleRenderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            battleContainer.appendChild(battleRenderer.domElement);

            battleScene.add(new THREE.DirectionalLight(0xffffff, 3.2).translateX(4).translateY(6).translateZ(4));
            battleScene.add(new THREE.DirectionalLight(0x38bdf8, 2.5).translateX(-4).translateY(-2).translateZ(-2));
            battleScene.add(new THREE.AmbientLight(0xffffff, 0.9));

            // Sàn đấu trường
            const floorGeo = new THREE.CylinderGeometry(4.8, 4.8, 0.3, 32);
            const floorMat = new THREE.MeshStandardMaterial({ color: 0x1e293b, metalness: 0.8, roughness: 0.3 });
            const floor = new THREE.Mesh(floorGeo, floorMat);
            floor.position.y = -1.4;
            battleScene.add(floor);

            // HERO ĐỨNG BÊN TRÁI, QUAY MẶT SANG PHẢI (XOAY Math.PI/2)
            heroGroup = new THREE.Group();
            heroGroup.position.set(-1.8, -0.6, 0);
            heroGroup.rotation.y = Math.PI / 2; 
            battleScene.add(heroGroup);
            createHeroModel();

            // BOSS ĐỨNG BÊN PHẢI, QUAY MẶT SANG TRÁI (XOAY -Math.PI/2)
            bossGroup = new THREE.Group();
            bossGroup.position.set(1.8, -0.5, 0);
            bossGroup.rotation.y = -Math.PI / 2;
            battleScene.add(bossGroup);

            // Nhóm chứa hiệu ứng particle skill 3D
            fxParticlesGroup = new THREE.Group();
            battleScene.add(fxParticlesGroup);

            updateBoss3DModel('wukong');

            // Weapon Forge 3D Scene
            const wpContainer = document.getElementById("weapon3DCanvas");
            wpScene = new THREE.Scene();
            wpCamera = new THREE.PerspectiveCamera(42, wpContainer.clientWidth / wpContainer.clientHeight, 0.1, 1000);
            wpCamera.position.set(0, 0, 5.0);
            wpRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
            wpRenderer.setSize(wpContainer.clientWidth, wpContainer.clientHeight);
            wpContainer.appendChild(wpRenderer.domElement);
            wpScene.add(new THREE.DirectionalLight(0xffffff, 3.5).translateX(5).translateY(5).translateZ(4));
            wpScene.add(new THREE.AmbientLight(0xffffff, 0.9));
            wpMeshGroup = new THREE.Group();
            wpMeshGroup.scale.set(1.2, 1.2, 1.2);
            wpScene.add(wpMeshGroup);

            // Ore 3D Scene
            const oreContainer = document.getElementById("ore3DContainer");
            oreScene = new THREE.Scene();
            oreCamera = new THREE.PerspectiveCamera(45, oreContainer.clientWidth / oreContainer.clientHeight, 0.1, 1000);
            oreCamera.position.set(0, 0, 5.5);
            oreRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
            oreRenderer.setSize(oreContainer.clientWidth, oreContainer.clientHeight);
            oreContainer.appendChild(oreRenderer.domElement);
            oreScene.add(new THREE.DirectionalLight(0xffffff, 3.0).translateX(3).translateY(5).translateZ(4));
            oreScene.add(new THREE.AmbientLight(0xffffff, 0.8));

            oreMeshGroup = new THREE.Group();
            const oreMesh = new THREE.Mesh(new THREE.DodecahedronGeometry(1.5, 1), new THREE.MeshStandardMaterial({ color: 0x047857, metalness: 0.8, roughness: 0.2, flatShading: true }));
            oreMeshGroup.add(oreMesh);
            
            const pickGroup = new THREE.Group();
            const handle = new THREE.Mesh(new THREE.CylinderGeometry(0.06, 0.08, 2.6, 8), new THREE.MeshStandardMaterial({ color: 0x9a3412 }));
            handle.position.y = -0.8; pickGroup.add(handle);
            const pHead = new THREE.Mesh(new THREE.TorusGeometry(0.7, 0.12, 8, 24, Math.PI), new THREE.MeshStandardMaterial({ color: 0xccbdc0, metalness: 0.9 }));
            pHead.rotation.z = Math.PI / 2; pHead.position.y = 0.5; pickGroup.add(pHead);
            pickGroup.position.set(1.8, 1.2, 0.5); pickGroup.rotation.z = Math.PI / 4;
            pickaxeMesh = pickGroup; oreMeshGroup.add(pickaxeMesh);
            oreScene.add(oreMeshGroup);

            animate3D();
            updateWeapon3DModel('wood');

            window.addEventListener('keydown', (e) => {
                if (e.code === 'Space' && document.getElementById('pageBattle').classList.contains('flex')) {
                    e.preventDefault();
                    heroAttack();
                }
            });

            setInterval(updateCooldowns, 1000);
        }

        // TẠO MO-HINHN HERO 3D (TAY CẦM KIẾM ĐƯỢC RÈN)
        function createHeroModel() {
            while(heroGroup.children.length > 0) heroGroup.remove(heroGroup.children[0]);

            const armorMat = new THREE.MeshStandardMaterial({ color: 0x38bdf8, metalness: 0.85, roughness: 0.2 });
            const skinMat = new THREE.MeshStandardMaterial({ color: 0xfde047, roughness: 0.5 });
            const capeMat = new THREE.MeshStandardMaterial({ color: 0xef4444, roughness: 0.6 });

            // Thân
            const torso = new THREE.Mesh(new THREE.BoxGeometry(0.8, 1.1, 0.45), armorMat);
            torso.position.y = 0.3; heroGroup.add(torso);

            // Đầu
            const head = new THREE.Mesh(new THREE.SphereGeometry(0.35, 16, 16), skinMat);
            head.position.y = 1.1; heroGroup.add(head);

            // Áo phang đỏ phía sau
            const cape = new THREE.Mesh(new THREE.BoxGeometry(0.75, 1.2, 0.08), capeMat);
            cape.position.set(0, 0.2, -0.26); heroGroup.add(cape);

            // Tay phải cầm bảo kiếm
            const swordGroup = new THREE.Group();
            swordGroup.name = "heroSword";
            const blade = createWeaponMesh(player.equippedId ? (player.inventory.find(i=>i.id===player.equippedId)?.modelType || 'wood') : 'wood');
            blade.scale.set(0.65, 0.65, 0.65);
            blade.position.y = 0.6;
            swordGroup.add(blade);
            
            swordGroup.position.set(0.5, 0.3, 0.2);
            heroGroup.add(swordGroup);
        }

        // ================= XÂY DỰNG MÔ HÌNH 6 BOSS 3D SẮC NÉT =================
        function createBossMesh(type) {
            const group = new THREE.Group();

            if (type === 'wukong') {
                const furMat = new THREE.MeshStandardMaterial({ color: 0x9a3412, roughness: 0.7 });
                const armorMat = new THREE.MeshStandardMaterial({ color: 0xfbbf24, metalness: 0.9, roughness: 0.2 });
                
                const head = new THREE.Mesh(new THREE.SphereGeometry(0.9, 16, 16), furMat);
                head.position.y = 0.8; group.add(head);

                const crown = new THREE.Mesh(new THREE.CylinderGeometry(0.95, 1.05, 0.25, 16), armorMat);
                crown.position.y = 1.4; group.add(crown);

                const body = new THREE.Mesh(new THREE.BoxGeometry(1.6, 1.8, 1.0), armorMat);
                body.position.y = -0.3; group.add(body);
            } 
            else if (type === 'titan') {
                const rockMat = new THREE.MeshStandardMaterial({ color: 0x334155, roughness: 0.9, metalness: 0.5, flatShading: true });
                const lavaMat = new THREE.MeshStandardMaterial({ color: 0xef4444, emissive: 0xd97706, emissiveIntensity: 1.0 });

                const head = new THREE.Mesh(new THREE.DodecahedronGeometry(1.2, 0), rockMat);
                head.position.y = 1.5; group.add(head);

                const eye = new THREE.Mesh(new THREE.BoxGeometry(1.4, 0.25, 0.5), lavaMat);
                eye.position.set(0, 1.5, 0.7); group.add(eye);

                const body = new THREE.Mesh(new THREE.BoxGeometry(2.6, 2.5, 1.6), rockMat);
                body.position.y = -0.3; group.add(body);
            } 
            else if (type === 'leviathan') {
                // BOSS MỚI: HẢI VƯƠNG LEVIATHAN (RẮN BIỂN KHỔNG LỒ VẢY CYAN)
                const seaMat = new THREE.MeshStandardMaterial({ color: 0x06b6d4, metalness: 0.85, roughness: 0.15, emissive: 0x0891b2, emissiveIntensity: 0.7 });
                const finMat = new THREE.MeshStandardMaterial({ color: 0x38bdf8, metalness: 0.9, roughness: 0.1 });

                // Thân rắn uốn lượn
                const body = new THREE.Mesh(new THREE.CylinderGeometry(0.9, 1.3, 3.2, 12), seaMat);
                body.rotation.z = Math.PI / 6; body.position.y = 0.2; group.add(body);

                // Đầu Leviathan
                const head = new THREE.Mesh(new THREE.ConeGeometry(1.1, 2.2, 6), seaMat);
                head.rotation.x = Math.PI / 2.3; head.position.set(0.6, 1.4, 0.3); group.add(head);

                // Vây lưng phát sáng
                for(let i=0; i<4; i++) {
                    const fin = new THREE.Mesh(new THREE.ConeGeometry(0.3, 1.2, 4), finMat);
                    fin.position.set(-0.2 - (i*0.4), 0.5 + (i*0.4), -0.6);
                    fin.rotation.z = -Math.PI / 4; group.add(fin);
                }
            }
            else if (type === 'dragon') {
                const scaleMat = new THREE.MeshStandardMaterial({ color: 0x991b1b, metalness: 0.8, roughness: 0.2 });
                const goldMat = new THREE.MeshStandardMaterial({ color: 0xfbbf24, metalness: 0.95 });

                const head = new THREE.Mesh(new THREE.ConeGeometry(1.3, 3.2, 6), scaleMat);
                head.rotation.x = Math.PI / 2.1; head.position.y = 0.5; group.add(head);

                for(let i = -1; i <= 1; i += 2) {
                    const horn = new THREE.Mesh(new THREE.ConeGeometry(0.3, 1.8, 5), goldMat);
                    horn.position.set(i * 0.9, 1.6, -0.4);
                    horn.rotation.z = i * -0.4; group.add(horn);
                }
            } 
            else if (type === 'frostking') {
                const iceMat = new THREE.MeshStandardMaterial({ color: 0x38bdf8, roughness: 0.1, metalness: 0.9, transparent: true, opacity: 0.95, emissive: 0x0284c7, emissiveIntensity: 0.8 });
                
                const head = new THREE.Mesh(new THREE.OctahedronGeometry(1.3, 1), iceMat);
                head.position.y = 0.8; group.add(head);

                for(let i=0; i<6; i++) {
                    const angle = (i / 6) * Math.PI * 2;
                    const spike = new THREE.Mesh(new THREE.ConeGeometry(0.2, 1.4, 4), iceMat);
                    spike.position.set(Math.cos(angle)*1.1, 1.8, Math.sin(angle)*1.1);
                    group.add(spike);
                }
            } 
            else if (type === 'overlord') {
                const darkMat = new THREE.MeshStandardMaterial({ color: 0x09090b, roughness: 0.2 });
                const redMat = new THREE.MeshStandardMaterial({ color: 0xef4444, emissive: 0xb91c1c, emissiveIntensity: 0.9 });

                const head = new THREE.Mesh(new THREE.BoxGeometry(1.8, 1.8, 1.6), darkMat);
                head.position.y = 0.5; group.add(head);

                for(let i = -1; i <= 1; i += 2) {
                    const horn = new THREE.Mesh(new THREE.ConeGeometry(0.4, 2.6, 6), redMat);
                    horn.position.set(i * 1.1, 2.0, -0.2);
                    horn.rotation.z = i * -0.4; group.add(horn);
                }
            }
            return group;
        }

        function updateBoss3DModel(type) {
            while(bossGroup.children.length > 0) bossGroup.remove(bossGroup.children[0]);
            bossGroup.add(createBossMesh(type));
        }

        function resize3DCanvases() {
            setTimeout(() => {
                const battleContainer = document.getElementById("battle3DCanvas");
                if (battleContainer && battleRenderer && battleCamera) {
                    battleCamera.aspect = battleContainer.clientWidth / battleContainer.clientHeight;
                    battleCamera.updateProjectionMatrix(); battleRenderer.setSize(battleContainer.clientWidth, battleContainer.clientHeight);
                }
                const wpContainer = document.getElementById("weapon3DCanvas");
                if (wpContainer && wpRenderer && wpCamera) {
                    wpCamera.aspect = wpContainer.clientWidth / wpContainer.clientHeight;
                    wpCamera.updateProjectionMatrix(); wpRenderer.setSize(wpContainer.clientWidth, wpContainer.clientHeight);
                }
                const oreContainer = document.getElementById("ore3DContainer");
                if (oreContainer && oreRenderer && oreCamera) {
                    oreCamera.aspect = oreContainer.clientWidth / oreContainer.clientHeight;
                    oreCamera.updateProjectionMatrix(); oreRenderer.setSize(oreContainer.clientWidth, oreContainer.clientHeight);
                }
            }, 50);
        }

        function createTrueBladeGeometry(width, length, thickness) {
            const shape = new THREE.Shape();
            shape.moveTo(-width / 2, 0); shape.lineTo(width / 2, 0);
            shape.lineTo(width / 2, length * 0.75); shape.lineTo(0, length);
            shape.lineTo(-width / 2, length * 0.75); shape.closePath();
            const extrudeSettings = { steps: 1, depth: thickness, bevelEnabled: true, bevelThickness: thickness / 2, bevelSize: thickness / 3, bevelSegments: 3 };
            const geo = new THREE.ExtrudeGeometry(shape, extrudeSettings);
            geo.center(); return geo;
        }

        function createWeaponMesh(modelType) {
            const group = new THREE.Group();
            if (modelType === 'wood') {
                const blade = new THREE.Mesh(createTrueBladeGeometry(0.35, 2.4, 0.1), new THREE.MeshStandardMaterial({ color: 0x854d0e, roughness: 0.7 }));
                blade.position.y = 0.5; group.add(blade);
            } else if (modelType === 'iron') {
                const blade = new THREE.Mesh(createTrueBladeGeometry(0.38, 2.8, 0.08), new THREE.MeshStandardMaterial({ color: 0xf8fafc, metalness: 0.95 }));
                blade.position.y = 0.6; group.add(blade);
            } else if (modelType === 'axe') {
                const bladeMat = new THREE.MeshStandardMaterial({ color: 0xf87171, metalness: 0.9, emissive: 0xef4444, emissiveIntensity: 0.8 });
                const axeHead = new THREE.Mesh(new THREE.BoxGeometry(1.6, 1.2, 0.25), bladeMat);
                axeHead.position.set(0.7, 0.8, 0); group.add(axeHead);
            } else if (modelType === 'emerald') {
                const blade = new THREE.Mesh(createTrueBladeGeometry(0.42, 3.0, 0.09), new THREE.MeshStandardMaterial({ color: 0x34d399, metalness: 0.9, emissive: 0x10b981, emissiveIntensity: 0.8 }));
                blade.position.y = 0.6; group.add(blade);
            } else if (modelType === 'diamond') {
                const blade = new THREE.Mesh(createTrueBladeGeometry(0.46, 3.3, 0.1), new THREE.MeshStandardMaterial({ color: 0x38bdf8, metalness: 0.98, emissive: 0x0284c7, emissiveIntensity: 0.8 }));
                blade.position.y = 0.7; group.add(blade);
            } else if (modelType === 'mythic') {
                const blade = new THREE.Mesh(createTrueBladeGeometry(0.55, 3.7, 0.12), new THREE.MeshStandardMaterial({ color: 0xf43f5e, metalness: 0.95, emissive: 0xe11d48, emissiveIntensity: 1.0 }));
                blade.position.y = 0.8; group.add(blade);
            }
            return group;
        }

        function updateWeapon3DModel(modelType) {
            while(wpMeshGroup.children.length > 0) wpMeshGroup.remove(wpMeshGroup.children[0]);
            wpMeshGroup.add(createWeaponMesh(modelType));
        }

        let time3D = 0;
        function animate3D() {
            requestAnimationFrame(animate3D);
            time3D += 0.03;

            if (wpMeshGroup) wpMeshGroup.rotation.y += 0.025;

            if (bossGroup) {
                bossGroup.rotation.y = -Math.PI / 2 + Math.sin(time3D * 0.5) * 0.2;
                bossGroup.position.y = -0.5 + Math.sin(time3D * 2) * 0.1;
            }

            // Animation Hero lao tới chém trực diện Boss
            if (isHeroAttacking && heroGroup) {
                heroAnimFrame += 0.35;
                heroGroup.position.x = -1.8 + Math.sin(heroAnimFrame) * 1.8;
                
                if (heroAnimFrame > Math.PI) {
                    isHeroAttacking = false;
                    heroAnimFrame = 0;
                    heroGroup.position.x = -1.8;
                }
            }

            // Cập nhật hiệu ứng particle 3D di chuyển & biến mất
            if (fxParticlesGroup) {
                for (let i = fxParticlesGroup.children.length - 1; i >= 0; i--) {
                    const p = fxParticlesGroup.children[i];
                    p.position.x += p.userData.vx;
                    p.position.y += p.userData.vy;
                    p.position.z += p.userData.vz;
                    p.scale.multiplyScalar(0.92);
                    if (p.scale.x < 0.05) fxParticlesGroup.remove(p);
                }
            }

            if (oreMeshGroup) {
                oreMeshGroup.rotation.y += 0.01;
                if (isMiningAnim && pickaxeMesh) {
                    miningframe += 0.25;
                    pickaxeMesh.rotation.z = (Math.PI / 4) + Math.sin(miningframe * 3) * 0.6;
                    if (miningframe > Math.PI * 2) { isMiningAnim = false; miningframe = 0; }
                }
            }

            if (battleRenderer && battleScene && battleCamera) battleRenderer.render(battleScene, battleCamera);
            if (wpRenderer && wpScene && wpCamera) wpRenderer.render(wpScene, wpCamera);
            if (oreRenderer && oreScene && oreCamera) oreRenderer.render(oreScene, oreCamera);
        }

        function switchTab(tab) {
            const pageBattle = document.getElementById("pageBattle");
            const pageStats = document.getElementById("pageStats");
            const pageForge = document.getElementById("pageForge");
            const pageGather = document.getElementById("pageGather");

            pageBattle.classList.add("hidden"); pageBattle.classList.remove("flex");
            pageStats.classList.add("hidden"); pageStats.classList.remove("grid");
            pageForge.classList.add("hidden"); pageForge.classList.remove("grid");
            pageGather.classList.add("hidden"); pageGather.classList.remove("flex");

            if (tab === 'battle') {
                pageBattle.classList.remove("hidden"); pageBattle.classList.add("flex");
                resize3DCanvases();
            } else if (tab === 'stats') {
                pageStats.classList.remove("hidden"); pageStats.classList.add("grid");
            } else if (tab === 'forge') {
                pageForge.classList.remove("hidden"); pageForge.classList.add("grid");
                resize3DCanvases();
            } else if (tab === 'gather') {
                pageGather.classList.remove("hidden"); pageGather.classList.add("flex");
                resize3DCanvases();
            }
        }

        function getPlayerStats() {
            let baseAtk = 15 + (player.stats.atkLvl * 8);
            let baseCrit = 5 + (player.stats.critLvl * 2);
            if (player.equippedId) {
                const eq = player.inventory.find(i => i.id === player.equippedId);
                if (eq) { baseAtk += eq.atk; baseCrit += eq.crit; }
            }
            if (tempBerserkActive) baseCrit = 100;
            return { atk: baseAtk, crit: baseCrit };
        }

        function heroAttack() {
            const d = dungeons[currentDungeonIdx];
            if (currentMonsterHp <= 0) return;

            isHeroAttacking = true; heroAnimFrame = 0;
            const stats = getPlayerStats();
            const isCrit = Math.random() * 100 < stats.crit;
            let dmg = stats.atk; if (isCrit) dmg = Math.floor(dmg * 2.0);

            currentMonsterHp -= dmg;
            const arena = document.getElementById("battleArena");
            arena.classList.remove("shake"); void arena.offsetWidth; arena.classList.add("shake");

            playSound(isCrit ? 'crit' : 'hit');
            spawnDamagePop(dmg, isCrit);
            spawn3DSparkEffect(0xffb703, 10);

            if (currentMonsterHp <= 0) { currentMonsterHp = 0; monsterKilled(); }
            updateBattleDisplay();
        }

        // ================= HIỆU ỨNG SKILL 3D HOÀNH TRÁNG BẰNG PARTICLE =================
        function spawn3DSparkEffect(hexColor, count) {
            for(let i=0; i<count; i++) {
                const pGeo = new THREE.SphereGeometry(0.12, 8, 8);
                const pMat = new THREE.MeshStandardMaterial({ color: hexColor, emissive: hexColor, emissiveIntensity: 1.0 });
                const p = new THREE.Mesh(pGeo, pMat);
                p.position.set(0.8 + (Math.random()-0.5)*0.8, (Math.random()-0.5)*0.8, (Math.random()-0.5)*0.8);
                p.userData = {
                    vx: (Math.random() - 0.5) * 0.15,
                    vy: (Math.random() - 0.2) * 0.15,
                    vz: (Math.random() - 0.5) * 0.15
                };
                fxParticlesGroup.add(p);
            }
        }

        function upgradeStat(type) {
            if (type === 'atk') {
                const cost = (player.stats.atkLvl + 1) * 200;
                if (player.gold >= cost) { player.gold -= cost; player.stats.atkLvl++; log("Nâng cấp ATK thành công!"); }
                else log("Không đủ Vàng!");
            } else if (type === 'crit') {
                const cost = (player.stats.critLvl + 1) * 400;
                if (player.gold >= cost) { player.gold -= cost; player.stats.critLvl++; log("Nâng cấp CRIT thành công!"); }
                else log("Không đủ Vàng!");
            } else if (type === 'gold') {
                const cost = (player.stats.goldLvl + 1) * 300;
                if (player.gold >= cost) { player.gold -= cost; player.stats.goldLvl++; log("Nâng cấp Vàng thưởng thành công!"); }
                else log("Không đủ Vàng!");
            }
            updateUI();
        }

        function upgradeSkill(id) {
            if (id === 1) {
                const cost = player.skills.s1.lvl * 10;
                if (player.soul >= cost) { player.soul -= cost; player.skills.s1.lvl++; log("Cường hóa Bão Lửa thành công!"); }
                else log("Không đủ Đá Hồn!");
            } else if (id === 2) {
                const cost = player.skills.s2.lvl * 15;
                if (player.soul >= cost) { player.soul -= cost; player.skills.s2.lvl++; log("Cường hóa Cuồng Bạo thành công!"); }
                else log("Không đủ Đá Hồn!");
            } else if (id === 3) {
                const cost = player.skills.s3.lvl * 25;
                if (player.soul >= cost) { player.soul -= cost; player.skills.s3.lvl++; log("Cường hóa Kết Liễu thành công!"); }
                else log("Không đủ Đá Hồn!");
            }
            updateUI();
        }

        // KĨ NĂNG ĐẶC BIỆT CÓ HIỆU ỨNG PARTICLE 3D THEO MÀU
        function useSkill(id) {
            const d = dungeons[currentDungeonIdx];
            if (currentMonsterHp <= 0) return;

            if (id === 1 && player.skills.s1.cd === 0) {
                const stats = getPlayerStats();
                const mult = 3 + player.skills.s1.lvl;
                const dmg = stats.atk * mult;
                currentMonsterHp -= dmg;
                player.skills.s1.cd = player.skills.s1.maxCd;
                playSound('skill');
                spawnDamagePop(`🔥 BÃO LỬA HUỶ DIỆT -${dmg.toLocaleString()}`, true);
                spawn3DSparkEffect(0xef4444, 30); // Cột lửa đỏ 3D
                log(`Thi triển Bão Lửa 3D gây ${dmg.toLocaleString()} ST!`);
            } else if (id === 2 && player.skills.s2.cd === 0) {
                tempBerserkActive = true;
                const dur = 4 + player.skills.s2.lvl;
                player.skills.s2.cd = player.skills.s2.maxCd;
                playSound('skill');
                spawn3DSparkEffect(0x38bdf8, 25); // Sét xanh 3D
                log(`Kích hoạt Cuồng Bạo Vũ Trụ: 100% Chí Mạng trong ${dur}s!`);
                setTimeout(() => { tempBerserkActive = false; log("Cuồng Bạo đã hết."); }, dur * 1000);
            } else if (id === 3 && player.skills.s3.cd === 0) {
                const pct = 8 + (player.skills.s3.lvl * 4);
                const dmg = Math.floor(d.maxHp * (pct / 100));
                currentMonsterHp -= dmg;
                player.skills.s3.cd = player.skills.s3.maxCd;
                playSound('skill');
                spawnDamagePop(`💀 KẾT LIỄU TỨC THÌ -${dmg.toLocaleString()}`, true);
                spawn3DSparkEffect(0xa855f7, 35); // Hạt tím ma thuật 3D
                log(`Thi triển Kết Liễu trừ ${pct}% Max HP Boss!`);
            }

            if (currentMonsterHp <= 0) { currentMonsterHp = 0; monsterKilled(); }
            updateBattleDisplay();
            updateSkillButtons();
        }

        function updateCooldowns() {
            if (player.skills.s1.cd > 0) player.skills.s1.cd--;
            if (player.skills.s2.cd > 0) player.skills.s2.cd--;
            if (player.skills.s3.cd > 0) player.skills.s3.cd--;
            updateSkillButtons();
        }

        function updateSkillButtons() {
            document.getElementById("cdSkill1").innerText = player.skills.s1.cd;
            document.getElementById("cdSkill2").innerText = player.skills.s2.cd;
            document.getElementById("cdSkill3").innerText = player.skills.s3.cd;
            document.getElementById("btnSkill1").disabled = player.skills.s1.cd > 0;
            document.getElementById("btnSkill2").disabled = player.skills.s2.cd > 0;
            document.getElementById("btnSkill3").disabled = player.skills.s3.cd > 0;
        }

        function performGather() {
            playSound('hit'); isMiningAnim = true; miningframe = 0;
            const addWood = Math.floor(Math.random() * 15) + 5;
            const addIron = Math.floor(Math.random() * 15) + 5;
            const addSoul = Math.random() > 0.5 ? Math.floor(Math.random() * 3) + 1 : 0;
            const addGold = Math.floor(Math.random() * 60) + 30;

            player.wood += addWood; player.iron += addIron; player.soul += addSoul; player.gold += addGold;
            const zone = document.getElementById("gatherZone");
            zone.classList.remove("shake"); void zone.offsetWidth; zone.classList.add("shake");
            spawnGatherPop(`+${addWood} Gỗ, +${addIron} Sắt ${addSoul > 0 ? `, +${addSoul} Đá Hồn` : ''}`);
            log(`⛏️ Khai thác thành công: +${addWood} Gỗ, +${addIron} Sắt, +${addGold} Vàng!`);
            updateUI();
        }

        function spawnGatherPop(text) {
            const zone = document.getElementById("gatherZone");
            const pop = document.createElement("div");
            pop.className = "damage-pop text-emerald-300 text-sm font-bold";
            pop.innerText = text; pop.style.left = (Math.random() * 40 + 30) + "%"; pop.style.top = "30%";
            zone.appendChild(pop); setTimeout(() => pop.remove(), 800);
        }

        function renderRecipes() {
            const list = document.getElementById("recipeList");
            list.innerHTML = "";
            recipes.forEach(r => {
                const isSelected = r.id === selectedRecipeId;
                const div = document.createElement("div");
                div.className = `p-2.5 rounded-xl border cursor-pointer transition flex items-center justify-between ${isSelected ? 'border-amber-400 bg-amber-950/60' : 'border-slate-700 bg-slate-900/80 hover:bg-slate-800'}`;
                div.onclick = () => { selectedRecipeId = r.id; updateWeapon3DModel(r.modelType); renderRecipes(); };
                div.innerHTML = `
                    <div class="flex items-center gap-2.5">
                        <span class="text-2xl">${r.icon}</span>
                        <div><div class="font-bold text-xs sm:text-sm rarity-text-${r.rarity}">${r.name}</div><div class="text-[11px] text-slate-300">ATK: +${r.atk} | CRIT: ${r.crit}%</div></div>
                    </div>
                    <div class="text-right text-[11px] text-slate-300"><div>🪵 ${r.wood} | 🪨 ${r.iron} | 🔮 ${r.soul}</div></div>
                `;
                list.appendChild(div);
            });
        }

        function forgeWeapon() {
            const recipe = recipes.find(r => r.id === selectedRecipeId);
            if (!recipe) return;
            if (player.inventory.length >= 12) { log("Túi đồ đã đầy (Tối đa 12 vũ khí)!"); return; }
            if (player.wood < recipe.wood || player.iron < recipe.iron || player.soul < recipe.soul) { log("Không đủ nguyên liệu rèn kiếm!"); return; }

            player.wood -= recipe.wood; player.iron -= recipe.iron; player.soul -= recipe.soul;
            const newWeapon = { id: 'wp_' + Date.now(), name: recipe.name, icon: recipe.icon, modelType: recipe.modelType, atk: recipe.atk, crit: recipe.crit, rarity: recipe.rarity, level: 1 };
            player.inventory.push(newWeapon);
            if (!player.equippedId) {
                player.equippedId = newWeapon.id;
                createHeroModel();
            }
            playSound('forge'); log(`Rèn thành công bảo kiếm: ${newWeapon.name}!`); updateUI();
        }

        function equipWeapon(id) {
            player.equippedId = id;
            createHeroModel();
            log("Đã trang bị vũ khí mới!");
            updateUI();
        }
        function upgradeWeapon(id) {
            const item = player.inventory.find(i => i.id === id);
            if (!item) return;
            const cost = item.level * 80;
            if (player.gold < cost) { log(`Cần ${cost} Vàng để cường hóa!`); return; }
            player.gold -= cost; item.level += 1; item.atk += Math.floor(item.atk * 0.3);
            log(`Cường hóa thành công ${item.name} lên +${item.level}!`); updateUI();
        }
        function sellWeapon(id) {
            const idx = player.inventory.findIndex(i => i.id === id);
            if (idx === -1) return;
            if (player.equippedId === id) player.equippedId = null;
            player.inventory.splice(idx, 1); player.gold += 150;
            createHeroModel();
            log("Đã bán vũ khí lấy +150 Vàng!"); updateUI();
        }

        function renderInventory() {
            const grid = document.getElementById("inventoryGrid");
            document.getElementById("inventoryCount").innerText = `${player.inventory.length}/12`;
            grid.innerHTML = "";
            if (player.inventory.length === 0) { grid.innerHTML = `<div class="text-xs text-slate-400 text-center py-8">Kho trang bị trống. Hãy rèn kiếm ngay!</div>`; return; }

            player.inventory.forEach(item => {
                const isEquipped = item.id === player.equippedId;
                const div = document.createElement("div");
                div.className = `p-3 rounded-xl border rarity-${item.rarity} bg-slate-900/90 flex items-center justify-between gap-2 shadow-md`;
                div.innerHTML = `
                    <div class="flex items-center gap-2.5">
                        <span class="text-2xl">${item.icon || '⚔️'}</span>
                        <div><div class="font-bold text-xs sm:text-sm rarity-text-${item.rarity}">${item.name} <span class="text-red-400">+${item.level}</span></div><div class="text-[10px] text-slate-300">ATK: +${item.atk} | CRIT: ${item.crit}%</div></div>
                    </div>
                    <div class="flex gap-1">
                        ${isEquipped ? `<span class="text-[10px] bg-red-600/30 text-red-300 px-2 py-1 rounded border border-red-400 font-bold">Đang dùng</span>` : `<button onclick="equipWeapon('${item.id}')" class="text-[10px] bg-slate-800 hover:bg-slate-700 text-slate-200 px-2 py-1 rounded border border-slate-600">Dùng</button>`}
                        <button onclick="upgradeWeapon('${item.id}')" class="text-[10px] bg-amber-950 hover:bg-amber-900 text-amber-200 px-2 py-1 rounded border border-amber-600">Cường hóa (${item.level*80}G)</button>
                        <button onclick="sellWeapon('${item.id}')" class="text-[10px] bg-rose-950 hover:bg-rose-900 text-rose-200 px-1.5 py-1 rounded border border-rose-700">Bán</button>
                    </div>
                `;
                grid.appendChild(div);
            });
        }

        function selectDungeon(idx) {
            currentDungeonIdx = idx;
            currentMonsterHp = dungeons[idx].maxHp;
            updateBoss3DModel(dungeons[idx].bossType);
            updateBattleDisplay();
        }

        function updateBattleDisplay() {
            const d = dungeons[currentDungeonIdx];
            document.getElementById("dungeonLevelBadge").innerText = `MÀN ${d.level}`;
            document.getElementById("monsterName").innerText = d.monster;
            const pct = Math.max(0, (currentMonsterHp / d.maxHp) * 100);
            document.getElementById("monsterHpBar").style.width = pct + "%";
            document.getElementById("monsterHpText").innerText = `${Math.max(0, currentMonsterHp).toLocaleString()} / ${d.maxHp.toLocaleString()} HP`;
        }

        function spawnDamagePop(dmg, isCrit) {
            const arena = document.getElementById("battleArena");
            const pop = document.createElement("div");
            pop.className = `damage-pop ${isCrit ? 'text-red-400 text-2xl' : 'text-amber-300 text-lg'}`;
            pop.innerText = typeof dmg === 'string' ? dmg : ((isCrit ? "💥 CRIT " : "") + "-" + dmg.toLocaleString());
            pop.style.left = (Math.random() * 40 + 50) + "%"; pop.style.top = "40%";
            arena.appendChild(pop); setTimeout(() => pop.remove(), 850);
        }

        function monsterKilled() {
            const d = dungeons[currentDungeonIdx];
            playSound('win');
            const bonusGoldMult = 1 + (player.stats.goldLvl * 0.15);
            const totalGold = Math.floor(d.goldReward * bonusGoldMult);

            player.gold += totalGold; player.wood += d.woodDrop; player.iron += d.ironDrop; player.soul += d.soulDrop;
            log(`🏆 Đã hạ gục ${d.monster}! Nhận +${totalGold.toLocaleString()} Vàng.`);

            if (currentDungeonIdx < dungeons.length - 1) {
                setTimeout(() => {
                    currentDungeonIdx++; currentMonsterHp = dungeons[currentDungeonIdx].maxHp;
                    updateBoss3DModel(dungeons[currentDungeonIdx].bossType); updateBattleDisplay();
                    log(`🚀 Đã bước sang ${dungeons[currentDungeonIdx].name}!`);
                }, 1500);
            } else {
                // HẾT 6 MÀN CHƠI -> BẬT MODAL TIẾN HOÁ NEWGAME+
                setTimeout(() => {
                    document.getElementById("evoModal").classList.remove("hidden");
                }, 600);
            }
            updateUI();
        }

        function chooseEvoOption(opt) {
            document.getElementById("evoModal").classList.add("hidden");
            if (opt === 'evolve') {
                player.bossEvoTier++;
                log(`🧬 BOSS ĐÃ TIẾN HOÁ THÀNH DẠNG TIER ${player.bossEvoTier}! Cực kỳ trâu và thưởng hậu hĩnh!`);
            } else {
                player.bossEvoTier = 1;
                log(`🔄 Đã làm mới các Boss về Dạng 1 Gốc!`);
            }
            recalculateDungeons();
            currentDungeonIdx = 0;
            currentMonsterHp = dungeons[0].maxHp;
            updateBoss3DModel(dungeons[0].bossType);
            updateUI();
        }

        function toggleAutoAttack() {
            const btn = document.getElementById("btnAutoAttack");
            if (autoAttackInterval) {
                clearInterval(autoAttackInterval); autoAttackInterval = null;
                btn.innerText = "🤖 Tự Động: OFF"; btn.className = "py-3 px-4 bg-slate-800 border border-slate-600 hover:bg-slate-700 font-semibold rounded-xl text-slate-200 transition text-xs";
            } else {
                autoAttackInterval = setInterval(() => { heroAttack(); }, 800);
                btn.innerText = "🤖 Tự Động: ON"; btn.className = "py-3 px-4 bg-red-700/60 border border-red-400 hover:bg-red-700 font-bold rounded-xl text-red-200 transition text-xs shadow-lg";
            }
        }

        function log(msg) { document.getElementById("gameLog").innerText = msg; }

        function updateUI() {
            recalculateDungeons();

            document.getElementById("bossEvoLvl").innerText = `Dạng ${player.bossEvoTier} (${player.bossEvoTier > 1 ? '+' + ((player.bossEvoTier-1)*50) + '% Stats' : 'Gốc'})`;
            document.getElementById("resGold").innerText = player.gold.toLocaleString();
            document.getElementById("resWood").innerText = player.wood.toLocaleString();
            document.getElementById("resIron").innerText = player.iron.toLocaleString();
            document.getElementById("resSoul").innerText = player.soul.toLocaleString();

            document.getElementById("statAtkLvl").innerText = player.stats.atkLvl;
            document.getElementById("statCritLvl").innerText = player.stats.critLvl;
            document.getElementById("statGoldLvl").innerText = player.stats.goldLvl;
            document.getElementById("costStatAtk").innerText = ((player.stats.atkLvl + 1) * 200).toLocaleString();
            document.getElementById("costStatCrit").innerText = ((player.stats.critLvl + 1) * 400).toLocaleString();
            document.getElementById("costStatGold").innerText = ((player.stats.goldLvl + 1) * 300).toLocaleString();

            document.getElementById("skill1Lvl").innerText = player.skills.s1.lvl;
            document.getElementById("skill1Mult").innerText = 3 + player.skills.s1.lvl;
            document.getElementById("costSkill1").innerText = player.skills.s1.lvl * 10;

            document.getElementById("skill2Lvl").innerText = player.skills.s2.lvl;
            document.getElementById("skill2Dur").innerText = 4 + player.skills.s2.lvl;
            document.getElementById("costSkill2").innerText = player.skills.s2.lvl * 15;

            document.getElementById("skill3Lvl").innerText = player.skills.s3.lvl;
            document.getElementById("skill3Pct").innerText = 8 + (player.skills.s3.lvl * 4);
            document.getElementById("costSkill3").innerText = player.skills.s3.lvl * 25;

            renderRecipes(); renderInventory(); updateBattleDisplay(); saveGame();
        }

        function saveGame() { localStorage.setItem("nextGenDarkForgeSave", JSON.stringify(player)); }
        function loadGame() {
            const data = localStorage.getItem("nextGenDarkForgeSave");
            if (data) { try { player = Object.assign(player, JSON.parse(data)); } catch(e) {} }
            recalculateDungeons();
            updateUI();
        }

        window.onload = () => { loadGame(); window.addEventListener('resize', resize3DCanvases); };
    </script>
</body>
</html>
