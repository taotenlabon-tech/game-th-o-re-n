<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Forge & Conquer 3D - Bright Epic Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@700;900&family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background: #1e293b; /* Nền sáng hơn: Xanh slate sáng thay vì đen tuyền */
            color: #f8fafc;
            overflow-x: hidden;
        }
        .font-cinzel { font-family: 'Cinzel', serif; }
        
        .shake {
            animation: shake 0.3s cubic-bezier(.36,.07,.19,.97) both;
        }
        @keyframes shake {
            10%, 90% { transform: translate3d(-3px, 0, 0); }
            20%, 80% { transform: translate3d(6px, 0, 0); }
            30%, 50%, 70% { transform: translate3d(-10px, 0, 0); }
            40%, 60% { transform: translate3d(10px, 0, 0); }
        }

        .damage-pop {
            position: absolute;
            animation: floatUp 0.8s cubic-bezier(0.25, 1, 0.5, 1) forwards;
            pointer-events: none;
            font-weight: 900;
            text-shadow: 0 0 15px rgba(239, 68, 68, 0.9);
            z-index: 50;
        }
        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0) scale(0.6); }
            40% { opacity: 1; transform: translateY(-40px) scale(1.4); }
            100% { opacity: 0; transform: translateY(-80px) scale(1); }
        }

        .tab-btn-active {
            background: linear-gradient(135deg, #b91c1c 0%, #7f1d1d 100%);
            border-color: #f87171;
            box-shadow: 0 0 25px rgba(248, 113, 113, 0.6);
            color: #ffffff;
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

        /* Panel sáng sủa, sang trọng, dễ nhìn rõ màu sắc vũ khí và quái vật */
        .glass-panel {
            background: linear-gradient(135deg, rgba(30, 41, 59, 0.95) 0%, rgba(15, 23, 42, 0.98) 100%);
            backdrop-filter: blur(16px);
            border: 1px solid rgba(245, 158, 11, 0.4);
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
        }

        .menu-bg {
            background: radial-gradient(circle at center, #334155 0%, #0f172a 75%);
        }

        .gothic-border {
            border: 2px solid rgba(245, 158, 11, 0.7);
            box-shadow: 0 0 35px rgba(245, 158, 11, 0.3), inset 0 0 20px rgba(245, 158, 11, 0.2);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col items-center justify-between p-2 sm:p-6 bg-slate-800">

    <!-- ================= MÀN HÌNH BẮT ĐẦU (START MENU) ================= -->
    <div id="startMenu" class="fixed inset-0 z-50 menu-bg flex flex-col items-center justify-center p-6 transition-opacity duration-700">
        <div class="absolute inset-0 bg-[radial-gradient(ellipse_at_top,_var(--tw-gradient-stops))] from-amber-500/20 via-transparent to-slate-950 pointer-events-none"></div>
        
        <div class="z-10 text-center max-w-xl mx-auto space-y-6 gothic-border p-8 rounded-3xl bg-slate-900/95 backdrop-blur-xl">
            <div class="inline-block p-4 bg-amber-900/60 border border-amber-400 rounded-3xl shadow-[0_0_35px_rgba(245,158,11,0.5)] mb-2 animate-bounce">
                <span class="text-5xl">⚔️</span>
            </div>
            
            <h1 class="font-cinzel text-3xl sm:text-5xl font-black text-transparent bg-clip-text bg-gradient-to-r from-amber-300 via-rose-400 to-red-500 tracking-wider drop-shadow-[0_5px_15px_rgba(245,158,11,0.6)]">
                DARK FORGE & CONQUER
            </h1>
            
            <p class="text-slate-300 text-xs sm:text-sm max-w-md mx-auto italic">
                Phiên bản sáng sủa - Phóng to bảo kiếm cực đại, hiện rõ từng chi tiết màu sắc lộng lẫy!
            </p>

            <div class="pt-4 space-y-3 w-full max-w-sm mx-auto">
                <button onclick="startAction('battle')" class="w-full py-3.5 bg-gradient-to-r from-red-600 to-rose-600 hover:from-red-500 hover:to-rose-500 font-cinzel font-bold text-base rounded-2xl text-white shadow-[0_0_20px_rgba(239,68,68,0.6)] active:scale-95 transition tracking-wider border border-red-400 flex items-center justify-center gap-3">
                    <span>⚔️</span> VÀO ĐÁNH BOSS
                </button>

                <button onclick="startAction('forge')" class="w-full py-3.5 bg-gradient-to-r from-amber-600 to-amber-500 hover:from-amber-500 hover:to-amber-400 font-cinzel font-bold text-base rounded-2xl text-white shadow-[0_0_20px_rgba(217,119,6,0.6)] active:scale-95 transition tracking-wider border border-amber-400 flex items-center justify-center gap-3">
                    <span>🔥</span> VÀO PHÒNG RÈN BẢO KIẾM
                </button>

                <button onclick="startAction('gather')" class="w-full py-3.5 bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-500 hover:to-teal-500 font-cinzel font-bold text-base rounded-2xl text-white shadow-[0_0_20px_rgba(16,185,129,0.6)] active:scale-95 transition tracking-wider border border-emerald-400 flex items-center justify-center gap-3">
                    <span>⛏️</span> VÀO KHU KHAI THÁC
                </button>

                <div class="pt-2">
                    <button onclick="resetData()" class="w-full py-2 bg-slate-800 hover:bg-slate-700 font-semibold text-xs rounded-xl text-slate-300 border border-slate-600 transition">
                        🔄 Xóa Dữ Liệu Lưu Cũ
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- ================= GIAO DIỆN CHÍNH TRONG GAME ================= -->
    <header class="w-full max-w-5xl glass-panel rounded-2xl p-4 mb-4 shadow-2xl flex flex-wrap justify-between items-center gap-4">
        <div class="flex items-center gap-3">
            <div class="w-12 h-12 bg-red-900 border border-red-500 rounded-xl flex items-center justify-center text-2xl shadow-[0_0_15px_rgba(239,68,68,0.4)] cursor-pointer hover:bg-red-800 transition" onclick="returnToMenu()" title="Về Menu Chính">
                🏠
            </div>
            <div>
                <h1 class="font-cinzel text-xl font-bold text-amber-400 tracking-wider">DARK FORGE & CONQUER</h1>
                <p class="text-xs text-slate-300">Cấp Độ Thợ Rèn: <span id="playerLvl" class="text-amber-300 font-bold">1</span></p>
            </div>
        </div>

        <div class="flex flex-wrap gap-2.5 text-xs sm:text-sm">
            <div class="bg-slate-900/90 px-3 py-1.5 rounded-xl border border-slate-700 flex items-center gap-1.5 shadow-inner">
                <span>💰 Vàng:</span>
                <span id="resGold" class="text-yellow-300 font-bold">0</span>
            </div>
            <div class="bg-slate-900/90 px-3 py-1.5 rounded-xl border border-slate-700 flex items-center gap-1.5 shadow-inner">
                <span>🪵 Gỗ:</span>
                <span id="resWood" class="text-emerald-300 font-bold">0</span>
            </div>
            <div class="bg-slate-900/90 px-3 py-1.5 rounded-xl border border-slate-700 flex items-center gap-1.5 shadow-inner">
                <span>🪨 Sắt:</span>
                <span id="resIron" class="text-slate-200 font-bold">0</span>
            </div>
            <div class="bg-slate-900/90 px-3 py-1.5 rounded-xl border border-slate-700 flex items-center gap-1.5 shadow-inner">
                <span>🔮 Đá Hồn:</span>
                <span id="resSoul" class="text-purple-300 font-bold">0</span>
            </div>
        </div>
    </header>

    <nav class="w-full max-w-5xl flex gap-3 mb-4">
        <button id="tabBattleBtn" onclick="switchTab('battle')" class="flex-1 py-3.5 px-4 rounded-xl font-cinzel font-bold text-sm sm:text-base border border-slate-600 bg-slate-900 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg">
            <span>⚔️</span> ĐÁNH BOSS
        </button>
        <button id="tabForgeBtn" onclick="switchTab('forge')" class="flex-1 py-3.5 px-4 rounded-xl font-cinzel font-bold text-sm sm:text-base border border-slate-600 bg-slate-900 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg">
            <span>🔥</span> RÈN VŨ KHÍ
        </button>
        <button id="tabGatherBtn" onclick="switchTab('gather')" class="flex-1 py-3.5 px-4 rounded-xl font-cinzel font-bold text-sm sm:text-base border border-slate-600 bg-slate-900 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg">
            <span>⛏️</span> KHAI THÁC
        </button>
    </nav>

    <main class="w-full max-w-5xl flex-1 flex flex-col">
        
        <!-- MÀN HÌNH 1: ĐÁNH BOSS -->
        <section id="pageBattle" class="flex-1 flex-col gap-4">
            <div class="glass-panel rounded-2xl p-3 flex flex-wrap items-center justify-between gap-2 shadow-lg">
                <span class="text-xs text-slate-300 font-bold">CHỌN CHÚA TỂ HẮC ÁM:</span>
                <div class="flex gap-2 flex-wrap" id="dungeonButtons">
                    <button onclick="selectDungeon(0)" class="btn-dungeon border border-emerald-500 bg-emerald-950/70 text-emerald-200 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-emerald-900 transition">🌿 Thần Rừng Wukong</button>
                    <button onclick="selectDungeon(1)" class="btn-dungeon border border-slate-400 bg-slate-800/70 text-slate-200 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-slate-700 transition">🗿 Titan Thạch Cổ</button>
                    <button onclick="selectDungeon(2)" class="btn-dungeon border border-red-500 bg-red-950/70 text-red-200 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-red-900 transition">🔥 Hỏa Long Hoàng Giả</button>
                    <button onclick="selectDungeon(3)" class="btn-dungeon border border-cyan-500 bg-cyan-950/70 text-cyan-200 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-cyan-900 transition">❄️ Băng Vương Vực Sâu</button>
                    <button onclick="selectDungeon(4)" class="btn-dungeon border border-purple-500 bg-purple-950/70 text-purple-200 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-purple-900 transition">👑 Ma Vương Tối Thượng</button>
                </div>
            </div>

            <div id="battleArena" class="relative glass-panel rounded-2xl p-6 flex-1 flex flex-col items-center justify-between min-h-[440px] overflow-hidden shadow-2xl">
                <div class="text-center z-10">
                    <span id="dungeonName" class="text-xs font-bold uppercase tracking-widest text-amber-400">Thần Rừng Wukong</span>
                    <h3 id="monsterName" class="text-2xl font-bold font-cinzel text-white mt-0.5">Hầu Vương Tôn Ngộ Không</h3>
                </div>

                <div class="relative my-2 w-full max-w-md h-64 flex flex-col items-center justify-center">
                    <!-- Nền màn hình hiển thị Boss sáng sủa (màu slate-900 thay vì đen đặc) -->
                    <div id="boss3DCanvas" class="w-full h-full cursor-pointer bg-slate-900/90 rounded-2xl border border-slate-700 shadow-[inset_0_0_30px_rgba(0,0,0,0.5)]" onclick="playerAttack()"></div>
                    
                    <div class="w-full bg-slate-950 rounded-full h-4 p-0.5 border border-slate-700 overflow-hidden shadow-inner mt-2">
                        <div id="monsterHpBar" class="bg-gradient-to-r from-red-600 to-rose-500 h-full rounded-full transition-all duration-200" style="width: 100%;"></div>
                    </div>
                    <div id="monsterHpText" class="text-xs text-red-300 font-bold mt-1">160 / 160 HP</div>
                </div>

                <div class="w-full max-w-md flex gap-3 z-10">
                    <button onclick="playerAttack()" class="flex-1 py-3.5 bg-gradient-to-r from-red-600 to-rose-600 hover:from-red-500 hover:to-rose-500 font-bold rounded-xl text-white shadow-lg shadow-red-600/40 active:scale-95 transition flex items-center justify-center gap-2 text-base">
                        <span>⚔️</span> TẤN CÔNG (CLICK)
                    </button>
                    <button id="btnAutoAttack" onclick="toggleAutoAttack()" class="py-3.5 px-5 bg-slate-800 border border-slate-600 hover:bg-slate-700 font-semibold rounded-xl text-slate-200 transition text-sm">
                        🤖 Tự Động: OFF
                    </button>
                </div>
            </div>
        </section>

        <!-- MÀN HÌNH 2: RÈN VŨ KHÍ OP -->
        <section id="pageForge" class="hidden grid-cols-1 md:grid-cols-2 gap-5 flex-1">
            <div class="glass-panel rounded-2xl p-5 flex flex-col gap-4 shadow-2xl">
                <h2 class="text-lg font-bold text-amber-400 border-b border-slate-700 pb-2.5 flex items-center gap-2">
                    <span>⚔️ Bàn Rèn Bảo Kiếm Khổng Lồ (OP)</span>
                </h2>

                <div class="relative w-full h-56 bg-slate-900/90 rounded-xl border border-slate-700 overflow-hidden flex items-center justify-center shadow-[inset_0_0_30px_rgba(0,0,0,0.5)]">
                    <div id="weapon3DCanvas" class="w-full h-full"></div>
                    <div class="absolute bottom-2 left-3 text-[10px] text-amber-300 font-semibold bg-slate-950/80 px-2 py-0.5 rounded border border-amber-500/40">
                        ✨ Xem Rõ Màu Sắc & Phóng To
                    </div>
                </div>

                <div class="space-y-3">
                    <label class="text-xs text-slate-300 block font-semibold">Chọn Kiếm OP Cần Rèn:</label>
                    <div id="recipeList" class="grid grid-cols-1 gap-2 max-h-44 overflow-y-auto pr-1"></div>

                    <button id="btnForgeAction" onclick="forgeWeapon()" class="w-full py-3.5 bg-gradient-to-r from-amber-600 to-amber-500 hover:from-amber-500 hover:to-amber-400 font-bold rounded-xl text-white shadow-lg shadow-amber-600/40 active:scale-98 transition flex items-center justify-center gap-2 text-base">
                        <span>⚒️</span> RÈN BẢO KIẾM TỐI THƯỢNG
                    </button>
                </div>
            </div>

            <div class="glass-panel rounded-2xl p-5 flex flex-col gap-3 shadow-2xl">
                <h2 class="text-lg font-bold text-amber-400 border-b border-slate-700 pb-2.5 flex justify-between items-center">
                    <span>🎒 Kho Trang Bị (Đầy Đủ Icon)</span>
                    <span class="text-xs text-slate-400" id="inventoryCount">0/12</span>
                </h2>

                <div id="inventoryGrid" class="grid grid-cols-1 gap-2.5 flex-1 max-h-[460px] overflow-y-auto pr-1"></div>
            </div>
        </section>

        <!-- MÀN HÌNH 3: KHAI THÁC -->
        <section id="pageGather" class="hidden flex-1 flex-col items-center justify-center gap-6 glass-panel rounded-2xl p-8 shadow-2xl">
            <div class="text-center space-y-2">
                <span class="text-xs text-emerald-400 font-bold tracking-widest uppercase">MỎ NGUYÊN LIỆU HẮC ÁM</span>
                <h2 class="text-3xl font-bold font-cinzel text-white">KHU KHAI THÁC 3D</h2>
                <p class="text-xs text-slate-300 max-w-md mx-auto">Vung cuốc vào tảng quặng ma thuật để thu thập nguyên liệu tinh hoa!</p>
            </div>

            <div class="relative w-full max-w-md h-64 bg-slate-900/90 rounded-2xl border border-emerald-800/80 flex flex-col items-center justify-center p-6 shadow-inner overflow-hidden cursor-pointer" onclick="performGather()" id="gatherZone">
                <div id="ore3DContainer" class="w-full h-full absolute inset-0"></div>
                <div class="absolute bottom-3 text-xs font-semibold text-emerald-300 bg-slate-950/80 px-3 py-1 rounded-full border border-emerald-500/50 animate-pulse pointer-events-none">
                    CLICK ĐỂ ĐÀO QUẶNG!
                </div>
            </div>

            <button onclick="performGather()" class="w-full max-w-md py-4 bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-500 hover:to-teal-500 font-cinzel font-bold text-lg rounded-2xl text-white shadow-lg shadow-emerald-600/50 active:scale-95 transition tracking-wider border border-emerald-400 flex items-center justify-center gap-3">
                <span>⛏️</span> VUNG CUỐC KHAI THÁC
            </button>
        </section>

    </main>

    <footer class="w-full max-w-5xl mt-4 glass-panel rounded-xl p-3 text-xs text-slate-300 flex items-center justify-between shadow-lg">
        <span class="font-semibold text-amber-400">📜 Nhật Ký:</span>
        <span id="gameLog" class="truncate max-w-md text-right text-slate-200">Sẵn sàng chinh chiến thế giới hắc ám!</span>
    </footer>

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
                osc.connect(gain);
                gain.connect(audioCtx.destination);

                if (type === 'hit') {
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(150, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(30, audioCtx.currentTime + 0.12);
                    gain.gain.setValueAtTime(0.4, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.12);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.12);
                } else if (type === 'crit') {
                    osc.type = 'square';
                    osc.frequency.setValueAtTime(350, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(800, audioCtx.currentTime + 0.18);
                    gain.gain.setValueAtTime(0.5, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.18);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.18);
                } else if (type === 'forge') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(523.25, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(1046.5, audioCtx.currentTime + 0.3);
                    gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.3);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.3);
                }
            } catch(e) {}
        }

        let player = {
            level: 1,
            gold: 5000,
            wood: 500,
            iron: 500,
            soul: 200,
            inventory: [],
            equippedId: null
        };

        const recipes = [
            { id: 'sword_wood', name: 'Đao Gỗ Thâm Sơn', icon: '🪵', modelType: 'wood', wood: 10, iron: 0, soul: 0, atk: 150, crit: 10, rarity: 'Common' },
            { id: 'sword_iron', name: 'Trường Kiếm Sắt Đẫm Máu', icon: '🗡️', modelType: 'iron', wood: 15, iron: 15, soul: 0, atk: 450, crit: 20, rarity: 'Rare' },
            { id: 'axe_steel', name: 'Rìu Bão Lửa Địa Ngục', icon: '🪓', modelType: 'axe', wood: 15, iron: 30, soul: 5, atk: 1200, crit: 30, rarity: 'Epic' },
            { id: 'sword_emerald', name: 'Bảo Kiếm Lục Bảo Ma Quái', icon: '🗡️', modelType: 'emerald', wood: 20, iron: 40, soul: 8, atk: 3500, crit: 40, rarity: 'Epic' },
            { id: 'sword_diamond', name: 'Thánh Kiếm Kim Cương Vĩnh Cửu', icon: '💎', modelType: 'diamond', wood: 30, iron: 60, soul: 12, atk: 9500, crit: 55, rarity: 'Legendary' },
            { id: 'sword_mythic', name: 'Hủy Diệt Kiếm - Vô Cực Tối Thượng', icon: '👑', modelType: 'mythic', wood: 50, iron: 100, soul: 30, atk: 35000, crit: 85, rarity: 'Mythic' }
        ];

        let selectedRecipeId = 'sword_wood';

        const dungeons = [
            { name: "Thần Rừng Wukong", monster: "Hầu Vương Tôn Ngộ Không", bossType: 'wukong', maxHp: 160, goldMin: 15, goldMax: 25, woodDrop: 5, ironDrop: 2, soulDrop: 0 },
            { name: "Hầm Ngục Tăm Tối", monster: "Titan Thạch Cổ", bossType: 'titan', maxHp: 550, goldMin: 45, goldMax: 75, woodDrop: 2, ironDrop: 10, soulDrop: 2 },
            { name: "Hang Long Xương", monster: "Hỏa Long Hoàng Giả", bossType: 'dragon', maxHp: 2000, goldMin: 130, goldMax: 280, woodDrop: 0, ironDrop: 20, soulDrop: 6 },
            { name: "Vực Thẳm Băng Giá", monster: "Băng Vương Vực Sâu", bossType: 'frostking', maxHp: 7000, goldMin: 400, goldMax: 800, woodDrop: 0, ironDrop: 50, soulDrop: 15 },
            { name: "Lâu Đài Ma Vương", monster: "Ma Vương Tối Thượng", bossType: 'overlord', maxHp: 25000, goldMin: 1500, goldMax: 3000, woodDrop: 15, ironDrop: 100, soulDrop: 40 }
        ];

        let currentDungeonIdx = 0;
        let currentMonsterHp = dungeons[0].maxHp;
        let autoAttackInterval = null;

        let wpScene, wpCamera, wpRenderer, wpMeshGroup;
        let bossScene, bossCamera, bossRenderer, bossMeshGroup;
        let oreScene, oreCamera, oreRenderer, oreMeshGroup, pickaxeMesh, isMiningAnim = false, miningframe = 0;
        let is3DInitialized = false;

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
            if (autoAttackInterval) {
                clearInterval(autoAttackInterval);
                autoAttackInterval = null;
            }
            const menu = document.getElementById("startMenu");
            menu.style.display = 'flex';
            setTimeout(() => menu.style.opacity = '1', 10);
        }

        function resetData() {
            if (confirm("Bạn có chắc chắn muốn xóa toàn bộ dữ liệu lưu và chơi lại từ đầu không?")) {
                localStorage.removeItem("nextGenDarkForgeSave");
                location.reload();
            }
        }

        function init3DScenes() {
            // Weapon 3D Scene - Phóng to to hơn rõ rệt (Scale 1.9)
            const wpContainer = document.getElementById("weapon3DCanvas");
            wpScene = new THREE.Scene();
            wpCamera = new THREE.PerspectiveCamera(42, wpContainer.clientWidth / wpContainer.clientHeight, 0.1, 1000);
            wpCamera.position.set(0, 0, 5.2);

            wpRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true, powerPreference: "high-performance" });
            wpRenderer.setSize(wpContainer.clientWidth, wpContainer.clientHeight);
            wpRenderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            wpContainer.appendChild(wpRenderer.domElement);

            // Đèn chiếu sáng mạnh và rực rỡ hơn giúp thấy rõ màu sắc
            wpScene.add(new THREE.DirectionalLight(0xffffff, 3.5).translateX(5).translateY(5).translateZ(4));
            wpScene.add(new THREE.DirectionalLight(0xfbbf24, 2.8).translateX(-5).translateY(-3).translateZ(-2));
            wpScene.add(new THREE.AmbientLight(0xffffff, 0.9));
            wpMeshGroup = new THREE.Group();
            wpMeshGroup.scale.set(1.9, 1.9, 1.9); // ĐÃ PHÓNG TO HƠN NỮA
            wpScene.add(wpMeshGroup);

            // Boss 3D Scene
            const bossContainer = document.getElementById("boss3DCanvas");
            bossScene = new THREE.Scene();
            bossCamera = new THREE.PerspectiveCamera(42, bossContainer.clientWidth / bossContainer.clientHeight, 0.1, 1000);
            bossCamera.position.set(0, 0.2, 5.8);

            bossRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true, powerPreference: "high-performance" });
            bossRenderer.setSize(bossContainer.clientWidth, bossContainer.clientHeight);
            bossRenderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            bossContainer.appendChild(bossRenderer.domElement);

            bossScene.add(new THREE.DirectionalLight(0xffffff, 3.0).translateX(4).translateY(8).translateZ(5));
            bossScene.add(new THREE.DirectionalLight(0xf59e0b, 2.5).translateX(-5).translateY(-2).translateZ(-2));
            bossScene.add(new THREE.AmbientLight(0xffffff, 0.8));
            bossMeshGroup = new THREE.Group();
            bossScene.add(bossMeshGroup);

            // Ore 3D Scene
            const oreContainer = document.getElementById("ore3DContainer");
            oreScene = new THREE.Scene();
            oreCamera = new THREE.PerspectiveCamera(45, oreContainer.clientWidth / oreContainer.clientHeight, 0.1, 1000);
            oreCamera.position.set(0, 0, 5.5);

            oreRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true, powerPreference: "high-performance" });
            oreRenderer.setSize(oreContainer.clientWidth, oreContainer.clientHeight);
            oreRenderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            oreContainer.appendChild(oreRenderer.domElement);

            oreScene.add(new THREE.DirectionalLight(0xffffff, 3.0).translateX(3).translateY(5).translateZ(4));
            oreScene.add(new THREE.DirectionalLight(0x34d399, 2.5).translateX(-4).translateY(-3).translateZ(-2));
            oreScene.add(new THREE.AmbientLight(0xffffff, 0.8));

            oreMeshGroup = new THREE.Group();
            const oreGeo = new THREE.DodecahedronGeometry(1.5, 1);
            const oreMat = new THREE.MeshStandardMaterial({ color: 0x047857, metalness: 0.8, roughness: 0.2, flatShading: true });
            const oreMesh = new THREE.Mesh(oreGeo, oreMat);
            oreMeshGroup.add(oreMesh);

            for(let i=0; i<6; i++) {
                const crystalGeo = new THREE.ConeGeometry(0.25, 0.8, 4);
                const crystalMat = new THREE.MeshStandardMaterial({ color: 0x34d399, emissive: 0x059669, emissiveIntensity: 0.9, metalness: 0.9, roughness: 0.1 });
                const crystal = new THREE.Mesh(crystalGeo, crystalMat);
                crystal.position.set((Math.random()-0.5)*1.4, (Math.random()-0.5)*1.4, (Math.random()-0.5)*1.4);
                crystal.rotation.set(Math.random()*Math.PI, Math.random()*Math.PI, 0);
                oreMeshGroup.add(crystal);
            }

            const pickGroup = new THREE.Group();
            const handleGeo = new THREE.CylinderGeometry(0.06, 0.08, 2.6, 8);
            const handleMat = new THREE.MeshStandardMaterial({ color: 0x9a3412, roughness: 0.6 });
            const handle = new THREE.Mesh(handleGeo, handleMat);
            handle.position.y = -0.8;
            pickGroup.add(handle);

            const headGeo = new THREE.TorusGeometry(0.7, 0.12, 8, 24, Math.PI);
            const headMat = new THREE.MeshStandardMaterial({ color: 0xccbdc0, metalness: 0.95, roughness: 0.1 });
            const head = new THREE.Mesh(headGeo, headMat);
            head.rotation.z = Math.PI / 2;
            head.position.y = 0.5;
            pickGroup.add(head);

            pickGroup.position.set(1.8, 1.2, 0.5);
            pickGroup.rotation.z = Math.PI / 4;
            pickaxeMesh = pickGroup;
            oreMeshGroup.add(pickaxeMesh);

            oreScene.add(oreMeshGroup);

            animate3D();
            updateWeapon3DModel('wood');
            updateBoss3DModel('wukong');
        }

        function resize3DCanvases() {
            setTimeout(() => {
                const bossContainer = document.getElementById("boss3DCanvas");
                if (bossContainer && bossRenderer && bossCamera) {
                    bossCamera.aspect = bossContainer.clientWidth / bossContainer.clientHeight;
                    bossCamera.updateProjectionMatrix();
                    bossRenderer.setSize(bossContainer.clientWidth, bossContainer.clientHeight);
                }

                const wpContainer = document.getElementById("weapon3DCanvas");
                if (wpContainer && wpRenderer && wpCamera) {
                    wpCamera.aspect = wpContainer.clientWidth / wpContainer.clientHeight;
                    wpCamera.updateProjectionMatrix();
                    wpRenderer.setSize(wpContainer.clientWidth, wpContainer.clientHeight);
                }

                const oreContainer = document.getElementById("ore3DContainer");
                if (oreContainer && oreRenderer && oreCamera) {
                    oreCamera.aspect = oreContainer.clientWidth / oreContainer.clientHeight;
                    oreCamera.updateProjectionMatrix();
                    oreRenderer.setSize(oreContainer.clientWidth, oreContainer.clientHeight);
                }
            }, 50);
        }

        // ================= MÔ HÌNH VŨ KHÍ 3D TO LỚN, RÕ MÀU SẮC =================
        function createWeaponMesh(modelType) {
            const group = new THREE.Group();

            if (modelType === 'wood') {
                const bladeGeo = new THREE.BoxGeometry(0.6, 3.8, 0.25);
                const woodMat = new THREE.MeshStandardMaterial({ color: 0x854d0e, roughness: 0.7, metalness: 0.1 });
                const blade = new THREE.Mesh(bladeGeo, woodMat);
                blade.position.y = 0.8; group.add(blade);

                const guard = new THREE.Mesh(new THREE.BoxGeometry(1.4, 0.3, 0.5), woodMat);
                guard.position.y = -0.7; group.add(guard);

                const hilt = new THREE.Mesh(new THREE.CylinderGeometry(0.14, 0.14, 1.4, 8), new THREE.MeshStandardMaterial({ color: 0x334155 }));
                hilt.position.y = -1.5; group.add(hilt);
            } 
            else if (modelType === 'iron') {
                const bladeGeo = new THREE.ConeGeometry(0.55, 4.4, 4);
                const ironMat = new THREE.MeshStandardMaterial({ color: 0xf8fafc, metalness: 0.95, roughness: 0.15 });
                const blade = new THREE.Mesh(bladeGeo, ironMat);
                blade.position.y = 1.0; group.add(blade);

                const guard = new THREE.Mesh(new THREE.BoxGeometry(1.8, 0.35, 0.6), new THREE.MeshStandardMaterial({ color: 0x64748b, metalness: 0.9 }));
                guard.position.y = -0.7; group.add(guard);

                const pommel = new THREE.Mesh(new THREE.SphereGeometry(0.35, 12, 12), new THREE.MeshStandardMaterial({ color: 0xdf1d1d, emissive: 0x991b1b, emissiveIntensity: 0.6 }));
                pommel.position.y = -1.6; group.add(pommel);
            } 
            else if (modelType === 'axe') {
                const handle = new THREE.Mesh(new THREE.CylinderGeometry(0.18, 0.18, 4.5, 12), new THREE.MeshStandardMaterial({ color: 0x1e293b, metalness: 0.8 }));
                group.add(handle);

                const bladeMat = new THREE.MeshStandardMaterial({ color: 0xf87171, metalness: 0.9, roughness: 0.2, emissive: 0xef4444, emissiveIntensity: 0.9 });
                const axeHead = new THREE.Mesh(new THREE.BoxGeometry(2.2, 1.6, 0.35), bladeMat);
                axeHead.position.set(0.9, 1.1, 0); group.add(axeHead);

                const spike = new THREE.Mesh(new THREE.ConeGeometry(0.25, 0.9, 6), bladeMat);
                spike.rotation.z = -Math.PI / 2; spike.position.set(2.0, 1.1, 0); group.add(spike);
            } 
            else if (modelType === 'emerald') {
                const bladeGeo = new THREE.BoxGeometry(0.65, 4.8, 0.22);
                const emMat = new THREE.MeshStandardMaterial({ color: 0x34d399, metalness: 0.9, roughness: 0.1, emissive: 0x10b981, emissiveIntensity: 0.8 });
                const blade = new THREE.Mesh(bladeGeo, emMat);
                blade.position.y = 1.0; group.add(blade);

                const guard = new THREE.Mesh(new THREE.OctahedronGeometry(0.7, 0), new THREE.MeshStandardMaterial({ color: 0x059669, metalness: 0.95, emissive: 0x34d399, emissiveIntensity: 0.9 }));
                guard.position.y = -0.8; group.add(guard);

                const gem = new THREE.Mesh(new THREE.DodecahedronGeometry(0.4, 0), emMat);
                gem.position.y = -1.7; group.add(gem);
            } 
            else if (modelType === 'diamond') {
                const bladeGeo = new THREE.ConeGeometry(0.6, 5.4, 5);
                const diaMat = new THREE.MeshStandardMaterial({ color: 0x38bdf8, metalness: 0.98, roughness: 0.05, emissive: 0x0284c7, emissiveIntensity: 0.9 });
                const blade = new THREE.Mesh(bladeGeo, diaMat);
                blade.position.y = 1.2; group.add(blade);

                const guard = new THREE.Mesh(new THREE.BoxGeometry(2.3, 0.35, 0.7), new THREE.MeshStandardMaterial({ color: 0xfbbf24, metalness: 0.95, roughness: 0.1, emissive: 0xd97706, emissiveIntensity: 0.6 }));
                guard.position.y = -0.9; group.add(guard);

                const ring = new THREE.Mesh(new THREE.TorusGeometry(0.6, 0.1, 16, 32), diaMat);
                ring.rotation.x = Math.PI / 2; ring.position.y = -1.8; group.add(ring);
            } 
            else if (modelType === 'mythic') {
                const bladeGeo = new THREE.BoxGeometry(0.85, 5.8, 0.26);
                const mythicMat = new THREE.MeshStandardMaterial({ color: 0xf43f5e, metalness: 0.95, roughness: 0.05, emissive: 0xe11d48, emissiveIntensity: 1.0 });
                const blade = new THREE.Mesh(bladeGeo, mythicMat);
                blade.position.y = 1.3; group.add(blade);

                const wingMat = new THREE.MeshStandardMaterial({ color: 0x334155, metalness: 0.9, roughness: 0.2 });
                const wing1 = new THREE.Mesh(new THREE.ConeGeometry(0.5, 1.9, 4), wingMat);
                wing1.rotation.z = Math.PI / 3; wing1.position.set(-1.2, -0.6, 0); group.add(wing1);
                const wing2 = wing1.clone(); wing2.rotation.z = -Math.PI / 3; wing2.position.x = 1.2; group.add(wing2);

                const coreGem = new THREE.Mesh(new THREE.OctahedronGeometry(0.6, 0), mythicMat);
                coreGem.position.y = -0.9; group.add(coreGem);

                const pommel = new THREE.Mesh(new THREE.SphereGeometry(0.45, 16, 16), new THREE.MeshStandardMaterial({ color: 0xfde047, metalness: 0.95, emissive: 0xf59e0b, emissiveIntensity: 1.0 }));
                pommel.position.y = -1.9; group.add(pommel);
            }

            return group;
        }

        function updateWeapon3DModel(modelType) {
            while(wpMeshGroup.children.length > 0) wpMeshGroup.remove(wpMeshGroup.children[0]);
            wpMeshGroup.add(createWeaponMesh(modelType));
        }

        // ================= BOSS QUÁI VẬT SÁNG SỦA, RÕ MÀU =================
        function createBossMesh(type) {
            const group = new THREE.Group();

            if (type === 'wukong') {
                const crownMat = new THREE.MeshStandardMaterial({ color: 0xfbbf24, metalness: 0.95, roughness: 0.1, emissive: 0xd97706, emissiveIntensity: 0.7 });
                const headMat = new THREE.MeshStandardMaterial({ color: 0x475569, roughness: 0.3, metalness: 0.7 });
                const goldTrim = new THREE.MeshStandardMaterial({ color: 0x34d399, metalness: 0.9, roughness: 0.2 });

                const head = new THREE.Mesh(new THREE.SphereGeometry(1.1, 24, 24), headMat);
                head.position.y = 0.6; group.add(head);

                const crownBase = new THREE.Mesh(new THREE.CylinderGeometry(1.15, 1.25, 0.3, 24), crownMat);
                crownBase.position.y = 1.4; group.add(crownBase);

                for (let i = 0; i < 5; i++) {
                    const angle = (i / 5) * Math.PI * 2;
                    const spike = new THREE.Mesh(new THREE.ConeGeometry(0.18, 0.7, 4), crownMat);
                    spike.position.set(Math.cos(angle) * 0.95, 1.8, Math.sin(angle) * 0.95);
                    group.add(spike);
                }

                const armor = new THREE.Mesh(new THREE.CylinderGeometry(1.5, 1.8, 2.2, 16), goldTrim);
                armor.position.y = -1.0; group.add(armor);

                const staff = new THREE.Mesh(new THREE.CylinderGeometry(0.1, 0.1, 4.5, 12), crownMat);
                staff.position.set(1.6, 0, 0); staff.rotation.z = 0.2; group.add(staff);
            } 
            else if (type === 'titan') {
                const rockMat = new THREE.MeshStandardMaterial({ color: 0x334155, roughness: 0.8, metalness: 0.6, flatShading: true });
                const coreMat = new THREE.MeshStandardMaterial({ color: 0xf87171, emissive: 0xef4444, emissiveIntensity: 1.0 });

                const head = new THREE.Mesh(new THREE.DodecahedronGeometry(1.4, 0), rockMat);
                head.position.y = 1.4; group.add(head);

                const visor = new THREE.Mesh(new THREE.BoxGeometry(1.8, 0.3, 0.6), coreMat);
                visor.position.set(0, 1.4, 0.9); group.add(visor);

                const body = new THREE.Mesh(new THREE.BoxGeometry(3.6, 3.0, 2.2), rockMat);
                body.position.y = -0.6; group.add(body);

                const core = new THREE.Mesh(new THREE.OctahedronGeometry(0.7, 0), coreMat);
                core.position.set(0, -0.4, 1.15); group.add(core);
            } 
            else if (type === 'dragon') {
                const dragonMat = new THREE.MeshStandardMaterial({ color: 0x991b1b, roughness: 0.2, metalness: 0.7, emissive: 0x7f1d1d, emissiveIntensity: 0.6 });
                const goldMat = new THREE.MeshStandardMaterial({ color: 0xfbbf24, metalness: 0.95, roughness: 0.1 });

                const snout = new THREE.Mesh(new THREE.ConeGeometry(1.2, 3.0, 6), dragonMat);
                snout.rotation.x = Math.PI / 2.2; snout.position.set(0, 0.2, 0.5); group.add(snout);

                for(let i = -1; i <= 1; i += 2) {
                    const horn = new THREE.Mesh(new THREE.ConeGeometry(0.35, 2.4, 6), goldMat);
                    horn.position.set(i * 0.9, 1.6, -0.5);
                    horn.rotation.z = i * -0.3; horn.rotation.x = -0.3;
                    group.add(horn);
                }

                const halo = new THREE.Mesh(new THREE.TorusGeometry(1.8, 0.1, 16, 32), goldMat);
                halo.position.set(0, 0.8, -0.8); group.add(halo);

                const eye = new THREE.Mesh(new THREE.SphereGeometry(0.2, 12, 12), new THREE.MeshBasicMaterial({ color: 0xfde047 }));
                eye.position.set(-0.5, 0.6, 1.1); group.add(eye);
                const eye2 = eye.clone(); eye2.position.x = 0.5; group.add(eye2);
            } 
            else if (type === 'frostking') {
                const iceMat = new THREE.MeshStandardMaterial({ color: 0x38bdf8, roughness: 0.1, metalness: 0.9, transparent: true, opacity: 0.95, emissive: 0x0284c7, emissiveIntensity: 0.8 });
                const crownIce = new THREE.MeshStandardMaterial({ color: 0xf0fdf4, roughness: 0.05, metalness: 0.95, emissive: 0x38bdf8, emissiveIntensity: 0.9 });

                const head = new THREE.Mesh(new THREE.OctahedronGeometry(1.3, 1), iceMat);
                head.position.y = 0.8; group.add(head);

                for (let i = 0; i < 6; i++) {
                    const angle = (i / 6) * Math.PI * 2;
                    const spike = new THREE.Mesh(new THREE.ConeGeometry(0.2, 1.2, 4), crownIce);
                    spike.position.set(Math.cos(angle) * 1.0, 1.8, Math.sin(angle) * 1.0);
                    group.add(spike);
                }

                const shoulders = new THREE.Mesh(new THREE.BoxGeometry(3.4, 1.5, 1.6), iceMat);
                shoulders.position.y = -0.9; group.add(shoulders);
            } 
            else if (type === 'overlord') {
                const darkMat = new THREE.MeshStandardMaterial({ color: 0x1e293b, roughness: 0.15, metalness: 0.8 });
                const boneMat = new THREE.MeshStandardMaterial({ color: 0xf43f5e, roughness: 0.3, metalness: 0.5, emissive: 0xbe123c, emissiveIntensity: 0.9 });
                const crownGold = new THREE.MeshStandardMaterial({ color: 0xfbbf24, metalness: 0.95, roughness: 0.1, emissive: 0xd97706, emissiveIntensity: 0.8 });

                const head = new THREE.Mesh(new THREE.BoxGeometry(2.0, 2.2, 1.8), darkMat);
                head.position.y = 0.5; group.add(head);

                for(let i = -1; i <= 1; i += 2) {
                    const demonHorn = new THREE.Mesh(new THREE.ConeGeometry(0.5, 3.2, 8), boneMat);
                    demonHorn.position.set(i * 1.2, 2.2, -0.2);
                    demonHorn.rotation.z = i * -0.5; demonHorn.rotation.x = 0.3;
                    group.add(demonHorn);
                }

                const overlordCrown = new THREE.Mesh(new THREE.CylinderGeometry(1.1, 1.2, 0.4, 16), crownGold);
                overlordCrown.position.y = 1.7; group.add(overlordCrown);

                const eye = new THREE.Mesh(new THREE.BoxGeometry(0.5, 0.18, 0.2), new THREE.MeshBasicMaterial({ color: 0xfbbf24 }));
                eye.position.set(-0.7, 1.3, 1.02); group.add(eye);
                const eye2 = eye.clone(); eye2.position.x = 0.7; group.add(eye2);

                const body = new THREE.Mesh(new THREE.BoxGeometry(3.2, 3.5, 2.0), darkMat);
                body.position.y = -1.5; group.add(body);
            }

            return group;
        }

        function updateBoss3DModel(type) {
            while(bossMeshGroup.children.length > 0) bossMeshGroup.remove(bossMeshGroup.children[0]);
            bossMeshGroup.add(createBossMesh(type));
        }

        let time3D = 0;
        function animate3D() {
            requestAnimationFrame(animate3D);
            time3D += 0.03;

            if (wpMeshGroup) wpMeshGroup.rotation.y += 0.025;

            if (bossMeshGroup) {
                bossMeshGroup.rotation.y = Math.sin(time3D * 0.5) * 0.4;
                bossMeshGroup.position.y = Math.sin(time3D * 1.5) * 0.15;
            }

            if (oreMeshGroup) {
                oreMeshGroup.rotation.y += 0.01;
                if (isMiningAnim && pickaxeMesh) {
                    miningframe += 0.25;
                    pickaxeMesh.rotation.z = (Math.PI / 4) + Math.sin(miningframe * 3) * 0.6;
                    pickaxeMesh.position.x = 1.8 + Math.sin(miningframe * 3) * 0.3;
                    if (miningframe > Math.PI * 2) {
                        isMiningAnim = false;
                        miningframe = 0;
                        pickaxeMesh.rotation.z = Math.PI / 4;
                        pickaxeMesh.position.x = 1.8;
                    }
                }
            }

            if (wpRenderer && wpScene && wpCamera) wpRenderer.render(wpScene, wpCamera);
            if (bossRenderer && bossScene && bossCamera) bossRenderer.render(bossScene, bossCamera);
            if (oreRenderer && oreScene && oreCamera) oreRenderer.render(oreScene, oreCamera);
        }

        function switchTab(tab) {
            const pageBattle = document.getElementById("pageBattle");
            const pageForge = document.getElementById("pageForge");
            const pageGather = document.getElementById("pageGather");

            const btnBattle = document.getElementById("tabBattleBtn");
            const btnForge = document.getElementById("tabForgeBtn");
            const btnGather = document.getElementById("tabGatherBtn");

            pageBattle.classList.add("hidden"); pageBattle.classList.remove("flex");
            pageForge.classList.add("hidden"); pageForge.classList.remove("grid");
            pageGather.classList.add("hidden"); pageGather.classList.remove("flex");

            btnBattle.className = "flex-1 py-3.5 px-4 rounded-xl font-cinzel font-bold text-sm sm:text-base border border-slate-600 bg-slate-900 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg";
            btnForge.className = "flex-1 py-3.5 px-4 rounded-xl font-cinzel font-bold text-sm sm:text-base border border-slate-600 bg-slate-900 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg";
            btnGather.className = "flex-1 py-3.5 px-4 rounded-xl font-cinzel font-bold text-sm sm:text-base border border-slate-600 bg-slate-900 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg";

            if (tab === 'battle') {
                pageBattle.classList.remove("hidden"); pageBattle.classList.add("flex");
                btnBattle.className = "tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-cinzel font-bold text-sm sm:text-base border border-red-400 transition flex items-center justify-center gap-2 shadow-lg";
                resize3DCanvases();
            } else if (tab === 'forge') {
                pageForge.classList.remove("hidden"); pageForge.classList.add("grid");
                btnForge.className = "tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-cinzel font-bold text-sm sm:text-base border border-amber-400 transition flex items-center justify-center gap-2 shadow-lg";
                resize3DCanvases();
            } else if (tab === 'gather') {
                pageGather.classList.remove("hidden"); pageGather.classList.add("flex");
                btnGather.className = "tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-cinzel font-bold text-sm sm:text-base border border-emerald-400 transition flex items-center justify-center gap-2 shadow-lg";
                resize3DCanvases();
            }
        }

        function performGather() {
            playSound('hit');
            isMiningAnim = true;
            miningframe = 0;

            const addWood = Math.floor(Math.random() * 20) + 10;
            const addIron = Math.floor(Math.random() * 20) + 10;
            const addSoul = Math.random() > 0.4 ? Math.floor(Math.random() * 5) + 2 : 0;
            const addGold = Math.floor(Math.random() * 100) + 50;

            player.wood += addWood; player.iron += addIron; player.soul += addSoul; player.gold += addGold;

            const zone = document.getElementById("gatherZone");
            zone.classList.remove("shake"); void zone.offsetWidth; zone.classList.add("shake");

            spawnGatherPop(`+${addWood} Gỗ, +${addIron} Sắt ${addSoul > 0 ? `, +${addSoul} Đá Hồn` : ''}`);
            log(`⛏️ Khai thác quặng OP thành công: +${addWood} Gỗ, +${addIron} Sắt, +${addGold} Vàng!`);
            updateUI();
        }

        function spawnGatherPop(text) {
            const zone = document.getElementById("gatherZone");
            const pop = document.createElement("div");
            pop.className = "damage-pop text-emerald-300 text-sm font-bold";
            pop.innerText = text;
            pop.style.left = (Math.random() * 40 + 30) + "%"; pop.style.top = "30%";
            zone.appendChild(pop);
            setTimeout(() => pop.remove(), 800);
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
                        <div><div class="font-bold text-xs sm:text-sm rarity-text-${r.rarity}">${r.name}</div><div class="text-[11px] text-slate-300">ATK: +${r.atk.toLocaleString()} | CRIT: ${r.crit}%</div></div>
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
            if (player.wood < recipe.wood || player.iron < recipe.iron || player.soul < recipe.soul) { log("Không đủ nguyên liệu để rèn bảo kiếm này!"); return; }

            player.wood -= recipe.wood; player.iron -= recipe.iron; player.soul -= recipe.soul;
            const newWeapon = { id: 'wp_' + Date.now(), name: recipe.name, icon: recipe.icon, modelType: recipe.modelType, atk: recipe.atk, crit: recipe.crit, rarity: recipe.rarity, level: 1 };
            player.inventory.push(newWeapon);
            if (!player.equippedId) player.equippedId = newWeapon.id;

            playSound('forge');
            log(`Rèn thành công bảo kiếm OP: ${newWeapon.name}!`);
            updateUI();
        }

        function equipWeapon(id) { player.equippedId = id; log("Đã trang bị vũ khí OP mới!"); updateUI(); }
        function upgradeWeapon(id) {
            const item = player.inventory.find(i => i.id === id);
            if (!item) return;
            const cost = item.level * 100;
            if (player.gold < cost) { log(`Cần ${cost} Vàng để cường hóa!`); return; }
            player.gold -= cost; item.level += 1; item.atk += Math.floor(item.atk * 0.4);
            log(`Cường hóa thành công ${item.name} lên +${item.level}!`);
            updateUI();
        }
        function sellWeapon(id) {
            const idx = player.inventory.findIndex(i => i.id === id);
            if (idx === -1) return;
            if (player.equippedId === id) player.equippedId = null;
            player.inventory.splice(idx, 1); player.gold += 200;
            log("Đã bán vũ khí lấy +200 Vàng!"); updateUI();
        }

        function renderInventory() {
            const grid = document.getElementById("inventoryGrid");
            document.getElementById("inventoryCount").innerText = `${player.inventory.length}/12`;
            grid.innerHTML = "";
            if (player.inventory.length === 0) { grid.innerHTML = `<div class="text-xs text-slate-400 text-center py-8">Kho trang bị trống. Hãy rèn bảo kiếm OP ngay!</div>`; return; }

            player.inventory.forEach(item => {
                const isEquipped = item.id === player.equippedId;
                const div = document.createElement("div");
                div.className = `p-3 rounded-xl border rarity-${item.rarity} bg-slate-900/90 flex items-center justify-between gap-2 shadow-md`;
                div.innerHTML = `
                    <div class="flex items-center gap-2.5">
                        <span class="text-2xl">${item.icon || '⚔️'}</span>
                        <div><div class="font-bold text-xs sm:text-sm rarity-text-${item.rarity}">${item.name} <span class="text-red-400">+${item.level}</span></div><div class="text-[10px] text-slate-300">ATK: +${item.atk.toLocaleString()} | CRIT: ${item.crit}%</div></div>
                    </div>
                    <div class="flex gap-1">
                        ${isEquipped ? `<span class="text-[10px] bg-red-600/30 text-red-300 px-2 py-1 rounded border border-red-400 font-bold">Đang dùng</span>` : `<button onclick="equipWeapon('${item.id}')" class="text-[10px] bg-slate-800 hover:bg-slate-700 text-slate-200 px-2 py-1 rounded border border-slate-600">Dùng</button>`}
                        <button onclick="upgradeWeapon('${item.id}')" class="text-[10px] bg-amber-950 hover:bg-amber-900 text-amber-200 px-2 py-1 rounded border border-amber-600">Cường hóa (${item.level*100}G)</button>
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
            document.getElementById("dungeonName").innerText = d.name;
            document.getElementById("monsterName").innerText = d.monster;
            const pct = Math.max(0, (currentMonsterHp / d.maxHp) * 100);
            document.getElementById("monsterHpBar").style.width = pct + "%";
            document.getElementById("monsterHpText").innerText = `${Math.max(0, currentMonsterHp).toLocaleString()} / ${d.maxHp.toLocaleString()} HP`;
        }

        function getPlayerStats() {
            let baseAtk = 100, baseCrit = 15;
            if (player.equippedId) {
                const eq = player.inventory.find(i => i.id === player.equippedId);
                if (eq) { baseAtk += eq.atk; baseCrit += eq.crit; }
            }
            return { atk: baseAtk, crit: baseCrit };
        }

        function playerAttack() {
            const stats = getPlayerStats();
            const isCrit = Math.random() * 100 < stats.crit;
            let dmg = stats.atk; if (isCrit) dmg = Math.floor(dmg * 2.5);

            currentMonsterHp -= dmg;
            const bossCanvas = document.getElementById("boss3DCanvas");
            bossCanvas.classList.remove("shake"); void bossCanvas.offsetWidth; bossCanvas.classList.add("shake");

            playSound(isCrit ? 'crit' : 'hit');
            spawnDamagePop(dmg, isCrit);

            if (currentMonsterHp <= 0) monsterKilled();
            updateBattleDisplay();
        }

        function spawnDamagePop(dmg, isCrit) {
            const arena = document.getElementById("battleArena");
            const pop = document.createElement("div");
            pop.className = `damage-pop ${isCrit ? 'text-red-400 text-2xl' : 'text-amber-300 text-lg'}`;
            pop.innerText = (isCrit ? "💥 CRIT " : "") + "-" + dmg.toLocaleString();
            pop.style.left = (Math.random() * 40 + 30) + "%"; pop.style.top = "40%";
            arena.appendChild(pop);
            setTimeout(() => pop.remove(), 800);
        }

        function monsterKilled() {
            const d = dungeons[currentDungeonIdx];
            const rewardGold = Math.floor(Math.random() * (d.goldMax - d.goldMin + 1)) * 5 + 500;
            player.gold += rewardGold; player.wood += d.woodDrop * 5; player.iron += d.ironDrop * 5; player.soul += d.soulDrop + 2;

            log(`Đã tiêu diệt ${d.monster}! Nhận +${rewardGold.toLocaleString()} Vàng và tài nguyên OP.`);
            currentMonsterHp = d.maxHp;
            updateUI();
        }

        function toggleAutoAttack() {
            const btn = document.getElementById("btnAutoAttack");
            if (autoAttackInterval) {
                clearInterval(autoAttackInterval); autoAttackInterval = null;
                btn.innerText = "🤖 Tự Động: OFF";
                btn.className = "py-3.5 px-5 bg-slate-800 border border-slate-600 hover:bg-slate-700 font-semibold rounded-xl text-slate-200 transition text-sm";
            } else {
                autoAttackInterval = setInterval(() => { playerAttack(); }, 750);
                btn.innerText = "🤖 Tự Động: ON";
                btn.className = "py-3.5 px-5 bg-red-700/60 border border-red-400 hover:bg-red-700 font-bold rounded-xl text-red-200 transition text-sm shadow-lg shadow-red-600/40";
            }
        }

        function log(msg) { document.getElementById("gameLog").innerText = msg; }

        function updateUI() {
            document.getElementById("resGold").innerText = player.gold.toLocaleString();
            document.getElementById("resWood").innerText = player.wood.toLocaleString();
            document.getElementById("resIron").innerText = player.iron.toLocaleString();
            document.getElementById("resSoul").innerText = player.soul.toLocaleString();
            renderRecipes(); renderInventory(); updateBattleDisplay(); saveGame();
        }

        function saveGame() { localStorage.setItem("nextGenDarkForgeSave", JSON.stringify(player)); }
        function loadGame() {
            const data = localStorage.getItem("nextGenDarkForgeSave");
            if (data) { try { player = Object.assign(player, JSON.parse(data)); } catch(e) {} }
            updateUI();
        }

        window.onload = () => { loadGame(); window.addEventListener('resize', resize3DCanvases); };
    </script>
</body>
</html>
