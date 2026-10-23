<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Forge & Conquer - Rèn Vũ Khí & Diệt Quái</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@700&family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background: #0f172a;
            color: #f8fafc;
        }
        .font-cinzel { font-family: 'Cinzel', serif; }
        
        .shake {
            animation: shake 0.3s cubic-bezier(.36,.07,.19,.97) both;
        }
        @keyframes shake {
            10%, 90% { transform: translate3d(-1px, 0, 0); }
            20%, 80% { transform: translate3d(2px, 0, 0); }
            30%, 50%, 70% { transform: translate3d(-4px, 0, 0); }
            40%, 60% { transform: translate3d(4px, 0, 0); }
        }

        .damage-pop {
            position: absolute;
            animation: floatUp 0.8s ease-out forwards;
            pointer-events: none;
            font-weight: 800;
            text-shadow: 0 2px 4px rgba(0,0,0,0.8);
        }
        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0) scale(0.8); }
            50% { opacity: 1; transform: translateY(-30px) scale(1.2); }
            100% { opacity: 0; transform: translateY(-60px) scale(1); }
        }

        .tab-btn-active {
            background: linear-gradient(135deg, #d97706 0%, #b45309 100%);
            border-color: #f59e0b;
            box-shadow: 0 0 15px rgba(245, 158, 11, 0.4);
        }

        /* Rarity Borders */
        .rarity-Common { border-color: #94a3b8; }
        .rarity-Rare { border-color: #3b82f6; box-shadow: 0 0 10px rgba(59, 130, 246, 0.3); }
        .rarity-Epic { border-color: #a855f7; box-shadow: 0 0 12px rgba(168, 85, 247, 0.4); }
        .rarity-Legendary { border-color: #eab308; box-shadow: 0 0 15px rgba(234, 179, 8, 0.6); }

        .rarity-text-Common { color: #94a3b8; }
        .rarity-text-Rare { color: #60a5fa; }
        .rarity-text-Epic { color: #c084fc; }
        .rarity-text-Legendary { color: #fde047; }
    </style>
</head>
<body class="min-h-screen flex flex-col items-center justify-between p-2 sm:p-6 bg-slate-950">

    <!-- Top Status Bar -->
    <header class="w-full max-w-4xl bg-slate-900/90 border border-slate-800 backdrop-blur rounded-2xl p-4 mb-4 shadow-xl flex flex-wrap justify-between items-center gap-4">
        <div class="flex items-center gap-3">
            <div class="w-12 h-12 bg-amber-600/20 border border-amber-500/50 rounded-xl flex items-center justify-center text-2xl shadow-inner">
                ⚒️
            </div>
            <div>
                <h1 class="font-cinzel text-xl font-bold text-amber-400">FORGE & CONQUER</h1>
                <p class="text-xs text-slate-400">Cấp Độ Thợ Rèn: <span id="playerLvl" class="text-amber-300 font-bold">1</span></p>
            </div>
        </div>

        <div class="flex flex-wrap gap-3 text-xs sm:text-sm">
            <div class="bg-slate-800/80 px-3 py-1.5 rounded-lg border border-slate-700 flex items-center gap-1.5">
                <span>💰 Vàng:</span>
                <span id="resGold" class="text-yellow-400 font-bold">0</span>
            </div>
            <div class="bg-slate-800/80 px-3 py-1.5 rounded-lg border border-slate-700 flex items-center gap-1.5">
                <span>🪵 Gỗ:</span>
                <span id="resWood" class="text-emerald-400 font-bold">0</span>
            </div>
            <div class="bg-slate-800/80 px-3 py-1.5 rounded-lg border border-slate-700 flex items-center gap-1.5">
                <span>🪨 Sắt:</span>
                <span id="resIron" class="text-slate-300 font-bold">0</span>
            </div>
                <div class="bg-slate-800/80 px-3 py-1.5 rounded-lg border border-slate-700 flex items-center gap-1.5">
                <span>🔮 Đá Hồn:</span>
                <span id="resSoul" class="text-purple-400 font-bold">0</span>
            </div>
        </div>
    </header>

    <!-- Navigation Tabs -->
    <nav class="w-full max-w-4xl flex gap-3 mb-4">
        <button id="tabForgeBtn" onclick="switchTab('forge')" class="tab-btn-active flex-1 py-3 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 transition flex items-center justify-center gap-2 text-white">
            <span>🔨</span> RÈN VŨ KHÍ & KHO
        </button>
        <button id="tabBattleBtn" onclick="switchTab('battle')" class="flex-1 py-3 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 hover:bg-slate-700 transition flex items-center justify-center gap-2 text-slate-300">
            <span>⚔️</span> PLAY / CHIẾN ĐẤU
        </button>
    </nav>

    <!-- Main Content Container -->
    <main class="w-full max-w-4xl flex-1 flex flex-col">
        
        <!-- ================= PAGE 1: FORGE & INVENTORY ================= -->
        <section id="pageForge" class="grid grid-cols-1 md:grid-cols-2 gap-4 flex-1">
            
            <!-- Resource Gathering & Crafting -->
            <div class="bg-slate-900/80 border border-slate-800 rounded-2xl p-4 flex flex-col gap-4">
                <h2 class="text-lg font-bold text-amber-400 border-b border-slate-800 pb-2 flex items-center justify-between">
                    <span>🔨 Lò Rèn Vũ Khí</span>
                    <button onclick="gatherResources()" class="text-xs bg-emerald-600 hover:bg-emerald-500 text-white font-bold py-1 px-3 rounded-lg transition active:scale-95">
                        ⛏️ Khai Thác Nguyên Liệu
                    </button>
                </h2>

                <!-- Crafting Selection -->
                <div class="space-y-3">
                    <label class="text-xs text-slate-400 block font-semibold">Chọn Công Thức Rèn:</label>
                    <div id="recipeList" class="grid grid-cols-1 gap-2 max-h-56 overflow-y-auto pr-1">
                        <!-- Recipes dynamically inserted here -->
                    </div>

                    <button id="btnForgeAction" onclick="forgeWeapon()" class="w-full py-3 bg-gradient-to-r from-amber-600 to-amber-500 hover:from-amber-500 hover:to-amber-400 font-bold rounded-xl text-slate-950 shadow-lg shadow-amber-600/20 active:scale-98 transition flex items-center justify-center gap-2 text-base">
                        <span>🔥</span> RÈN VŨ KHÍ
                    </button>
                </div>

                <!-- Stats summary -->
                <div class="bg-slate-950/60 p-3 rounded-xl border border-slate-800/80 text-xs space-y-1">
                    <div class="text-slate-400 font-bold mb-1">📊 Chỉ Số Vũ Khí Đang Trang Bị:</div>
                    <div class="flex justify-between">
                        <span>Tên Vũ Khí:</span>
                        <span id="equippedName" class="font-bold text-slate-300">Tay Không</span>
                    </div>
                    <div class="flex justify-between">
                        <span>Sức Tấn Công (ATK):</span>
                        <span id="equippedAtk" class="font-bold text-amber-400">+5</span>
                    </div>
                    <div class="flex justify-between">
                        <span>Tỷ Lệ Bạo Kích (CRIT):</span>
                        <span id="equippedCrit" class="font-bold text-purple-400">5%</span>
                    </div>
                </div>
            </div>

            <!-- Inventory Panel -->
            <div class="bg-slate-900/80 border border-slate-800 rounded-2xl p-4 flex flex-col gap-3">
                <h2 class="text-lg font-bold text-amber-400 border-b border-slate-800 pb-2 flex justify-between items-center">
                    <span>🎒 Kho Vũ Khí</span>
                    <span class="text-xs text-slate-500" id="inventoryCount">0/12</span>
                </h2>

                <div id="inventoryGrid" class="grid grid-cols-1 gap-2 flex-1 max-h-96 overflow-y-auto pr-1">
                    <!-- Inventory items -->
                </div>
            </div>
        </section>

        <!-- ================= PAGE 2: BATTLE & DUNGEONS ================= -->
        <section id="pageBattle" class="hidden flex-1 flex-col gap-4">
            
            <!-- Dungeon Selector -->
            <div class="bg-slate-900/80 border border-slate-800 rounded-2xl p-3 flex flex-wrap items-center justify-between gap-2">
                <span class="text-xs text-slate-400 font-bold">CHỌN VÙNG ĐẤT SĂN QUÁI:</span>
                <div class="flex gap-2" id="dungeonButtons">
                    <button onclick="selectDungeon(0)" class="btn-dungeon border border-emerald-500/50 bg-emerald-950/40 text-emerald-300 text-xs px-3 py-1.5 rounded-lg font-semibold hover:bg-emerald-800/50 transition">🌲 Rừng Ma Kh khỏa</button>
                    <button onclick="selectDungeon(1)" class="btn-dungeon border border-slate-600 bg-slate-800/40 text-slate-300 text-xs px-3 py-1.5 rounded-lg font-semibold hover:bg-slate-700/50 transition">🏰 Hầm Ngục Cổ</button>
                    <button onclick="selectDungeon(2)" class="btn-dungeon border border-red-500/50 bg-red-950/40 text-red-300 text-xs px-3 py-1.5 rounded-lg font-semibold hover:bg-red-800/50 transition">🌋 Hang Rồng Lửa</button>
                </div>
            </div>

            <!-- Main Combat Arena -->
            <div id="battleArena" class="relative bg-slate-900/90 border border-slate-800 rounded-2xl p-6 flex-1 flex flex-col items-center justify-between min-h-[360px] overflow-hidden">
                
                <!-- Zone Title -->
                <div class="text-center">
                    <span id="dungeonName" class="text-xs font-bold uppercase tracking-widest text-amber-500">Rừng Ma Khỏa</span>
                    <h3 id="monsterName" class="text-xl font-bold font-cinzel text-slate-100 mt-1">Yêu Tinh Rừng</h3>
                </div>

                <!-- Monster Avatar Display -->
                <div class="relative my-6 flex flex-col items-center">
                    <div id="monsterAvatar" class="w-32 h-32 bg-slate-800/80 border-2 border-slate-700 rounded-full flex items-center justify-center text-6xl shadow-2xl transition-transform duration-100 cursor-pointer" onclick="playerAttack()">
                        👺
                    </div>
                    
                    <!-- Health Bar -->
                    <div class="w-64 bg-slate-950 rounded-full h-4 mt-4 p-0.5 border border-slate-800 overflow-hidden shadow-inner">
                        <div id="monsterHpBar" class="bg-gradient-to-r from-red-600 to-rose-500 h-full rounded-full transition-all duration-200" style="width: 100%;"></div>
                    </div>
                    <div id="monsterHpText" class="text-xs text-slate-400 font-bold mt-1">100 / 100 HP</div>
                </div>

                <!-- Attack Action Controls -->
                <div class="w-full max-w-md flex gap-3">
                    <button onclick="playerAttack()" class="flex-1 py-3 bg-red-600 hover:bg-red-500 font-bold rounded-xl text-white shadow-lg shadow-red-600/30 active:scale-95 transition flex items-center justify-center gap-2 text-base">
                        <span>⚔️</span> TẤN CÔNG (CLICK)
                    </button>
                    <button id="btnAutoAttack" onclick="toggleAutoAttack()" class="py-3 px-5 bg-slate-800 border border-slate-700 hover:bg-slate-700 font-semibold rounded-xl text-slate-300 transition text-sm">
                        🤖 Tự Động: OFF
                    </button>
                </div>
            </div>
        </section>

    </main>

    <!-- Footer Notification Log -->
    <footer class="w-full max-w-4xl mt-4 bg-slate-900/60 border border-slate-800/80 rounded-xl p-2.5 text-xs text-slate-400 flex items-center justify-between">
        <span class="font-semibold text-amber-500/80">📜 Nhật Ký:</span>
        <span id="gameLog" class="truncate max-w-md text-right text-slate-300">Chào mừng bạn đến với Forge & Conquer! hãy rèn vũ khí mới.</span>
    </footer>

    <!-- Game Logic JavaScript -->
    <script>
        // Web Audio Synthesizer
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
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(140, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(30, audioCtx.currentTime + 0.08);
                    gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.08);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.08);
                } else if (type === 'crit') {
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(300, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(600, audioCtx.currentTime + 0.15);
                    gain.gain.setValueAtTime(0.4, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.15);
                } else if (type === 'forge') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(523.25, audioCtx.currentTime); // C5
                    osc.frequency.exponentialRampToValueAtTime(1046.5, audioCtx.currentTime + 0.2); // C6
                    gain.gain.setValueAtTime(0.2, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.2);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.2);
                }
            } catch(e) {}
        }

        // Game State
        let player = {
            level: 1,
            gold: 50,
            wood: 20,
            iron: 10,
            soul: 0,
            inventory: [],
            equippedId: null
        };

        const recipes = [
            { id: 'sword_basic', name: 'Kiếm Gỗ Tập Sự', type: 'Sword', wood: 10, iron: 0, soul: 0, atk: 8, crit: 5, rarity: 'Common' },
            { id: 'sword_iron', name: 'Trường Kiếm Sắt', type: 'Sword', wood: 15, iron: 10, soul: 0, atk: 22, crit: 8, rarity: 'Rare' },
            { id: 'axe_battle', name: 'Rìu Chiến Thép', type: 'Axe', wood: 10, iron: 25, soul: 2, atk: 48, crit: 12, rarity: 'Epic' },
            { id: 'sword_legend', name: 'Thánh Kiếm Diệt Đao', type: 'Sword', wood: 30, iron: 50, soul: 10, atk: 110, crit: 25, rarity: 'Legendary' }
        ];

        let selectedRecipeId = 'sword_basic';

        const dungeons = [
            { name: "Rừng Ma Khỏa", monster: "Yêu Tinh Rừng", icon: "👺", maxHp: 100, goldMin: 8, goldMax: 15, woodDrop: 4, ironDrop: 1, soulDrop: 0 },
            { name: "Hầm Ngục Cổ", monster: "Kẻ Gác Hầm Ngục", icon: "🗿", maxHp: 350, goldMin: 25, goldMax: 45, woodDrop: 2, ironDrop: 8, soulDrop: 1 },
            { name: "Hang Rồng Lửa", monster: "Hỏa Rồng Cổ Đại", icon: "🐉", maxHp: 1200, goldMin: 80, goldMax: 150, woodDrop: 0, ironDrop: 15, soulDrop: 4 }
        ];

        let currentDungeonIdx = 0;
        let currentMonsterHp = dungeons[0].maxHp;
        let autoAttackInterval = null;

        // UI Tabs Switcher
        function switchTab(tab) {
            const pageForge = document.getElementById("pageForge");
            const pageBattle = document.getElementById("pageBattle");
            const btnForge = document.getElementById("tabForgeBtn");
            const btnBattle = document.getElementById("tabBattleBtn");

            if (tab === 'forge') {
                pageForge.classList.remove("hidden");
                pageForge.classList.add("grid");
                pageBattle.classList.add("hidden");
                pageBattle.classList.remove("flex");

                btnForge.className = "tab-btn-active flex-1 py-3 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 transition flex items-center justify-center gap-2 text-white";
                btnBattle.className = "flex-1 py-3 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 hover:bg-slate-700 transition flex items-center justify-center gap-2 text-slate-300";
            } else {
                pageForge.classList.add("hidden");
                pageForge.classList.remove("grid");
                pageBattle.classList.remove("hidden");
                pageBattle.classList.add("flex");

                btnBattle.className = "tab-btn-active flex-1 py-3 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 transition flex items-center justify-center gap-2 text-white";
                btnForge.className = "flex-1 py-3 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 hover:bg-slate-700 transition flex items-center justify-center gap-2 text-slate-300";
            }
        }

        // Gather resources
        function gatherResources() {
            player.wood += Math.floor(Math.random() * 3) + 2;
            player.iron += Math.floor(Math.random() * 2) + 1;
            log("Đã khai thác thêm nguyên liệu gỗ và sắt!");
            updateUI();
        }

        // Render Recipes
        function renderRecipes() {
            const list = document.getElementById("recipeList");
            list.innerHTML = "";
            recipes.forEach(r => {
                const isSelected = r.id === selectedRecipeId;
                const div = document.createElement("div");
                div.className = `p-2.5 rounded-xl border cursor-pointer transition flex items-center justify-between ${isSelected ? 'border-amber-500 bg-amber-950/30' : 'border-slate-800 bg-slate-950/40 hover:bg-slate-800/40'}`;
                div.onclick = () => { selectedRecipeId = r.id; renderRecipes(); };
                
                div.innerHTML = `
                    <div>
                        <div class="font-bold text-sm rarity-text-${r.rarity}">${r.name}</div>
                        <div class="text-[11px] text-slate-400">ATK: +${r.atk} | CRIT: ${r.crit}%</div>
                    </div>
                    <div class="text-right text-[11px] text-slate-400">
                        <div>🪵 ${r.wood} | 🪨 ${r.iron} | 🔮 ${r.soul}</div>
                    </div>
                `;
                list.appendChild(div);
            });
        }

        // Forge Weapon
        function forgeWeapon() {
            const recipe = recipes.find(r => r.id === selectedRecipeId);
            if (!recipe) return;

            if (player.inventory.length >= 12) {
                log("Túi đồ đã đầy (Tối đa 12 vũ khí)!");
                return;
            }

            if (player.wood < recipe.wood || player.iron < recipe.iron || player.soul < recipe.soul) {
                log("Không đủ nguyên liệu để rèn vũ khí này!");
                return;
            }

            player.wood -= recipe.wood;
            player.iron -= recipe.iron;
            player.soul -= recipe.soul;

            const newWeapon = {
                id: 'wp_' + Date.now(),
                name: recipe.name,
                atk: recipe.atk,
                crit: recipe.crit,
                rarity: recipe.rarity,
                level: 1
            };

            player.inventory.push(newWeapon);
            if (!player.equippedId) player.equippedId = newWeapon.id;

            playSound('forge');
            log(`Rèn thành công: ${newWeapon.name}!`);
            updateUI();
        }

        // Upgrade / Sell / Equip Item
        function equipWeapon(id) {
            player.equippedId = id;
            log("Đã thay đổi trang bị vũ khí!");
            updateUI();
        }

        function upgradeWeapon(id) {
            const item = player.inventory.find(i => i.id === id);
            if (!item) return;
            const cost = item.level * 40;
            if (player.gold < cost) {
                log(`Cần ${cost} Vàng để nâng cấp!`);
                return;
            }
            player.gold -= cost;
            item.level += 1;
            item.atk += Math.floor(item.atk * 0.25);
            log(`Nâng cấp thành công ${item.name} lên Cap +${item.level}!`);
            updateUI();
        }

        function sellWeapon(id) {
            const idx = player.inventory.findIndex(i => i.id === id);
            if (idx === -1) return;
            if (player.equippedId === id) player.equippedId = null;
            player.inventory.splice(idx, 1);
            player.gold += 30;
            log("Đã bán vũ khí lấy +30 Vàng!");
            updateUI();
        }

        // Render Inventory
        function renderInventory() {
            const grid = document.getElementById("inventoryGrid");
            document.getElementById("inventoryCount").innerText = `${player.inventory.length}/12`;
            grid.innerHTML = "";

            if (player.inventory.length === 0) {
                grid.innerHTML = `<div class="text-xs text-slate-500 text-center py-8">Chưa có vũ khí nào trong kho.</div>`;
                return;
            }

            player.inventory.forEach(item => {
                const isEquipped = item.id === player.equippedId;
                const div = document.createElement("div");
                div.className = `p-2.5 rounded-xl border rarity-${item.rarity} bg-slate-950/60 flex items-center justify-between gap-2`;

                div.innerHTML = `
                    <div>
                        <div class="font-bold text-xs rarity-text-${item.rarity}">${item.name} <span class="text-amber-400">+${item.level}</span></div>
                        <div class="text-[10px] text-slate-400">ATK: +${item.atk} | CRIT: ${item.crit}%</div>
                    </div>
                    <div class="flex gap-1">
                        ${isEquipped 
                            ? `<span class="text-[10px] bg-amber-500/20 text-amber-400 px-2 py-1 rounded border border-amber-500/30 font-bold">Đang dùng</span>` 
                            : `<button onclick="equipWeapon('${item.id}')" class="text-[10px] bg-slate-800 hover:bg-slate-700 text-slate-200 px-2 py-1 rounded border border-slate-700">Dùng</button>`
                        }
                        <button onclick="upgradeWeapon('${item.id}')" class="text-[10px] bg-indigo-900/60 hover:bg-indigo-800 text-indigo-200 px-2 py-1 rounded border border-indigo-700/50">Cường hóa (${item.level*40}G)</button>
                        <button onclick="sellWeapon('${item.id}')" class="text-[10px] bg-rose-950/50 hover:bg-rose-900 text-rose-300 px-1.5 py-1 rounded border border-rose-800/40">Bán</button>
                    </div>
                `;
                grid.appendChild(div);
            });
        }

        // Dungeon Selection
        function selectDungeon(idx) {
            currentDungeonIdx = idx;
            currentMonsterHp = dungeons[idx].maxHp;
            updateBattleDisplay();
        }

        function updateBattleDisplay() {
            const d = dungeons[currentDungeonIdx];
            document.getElementById("dungeonName").innerText = d.name;
            document.getElementById("monsterName").innerText = d.monster;
            document.getElementById("monsterAvatar").innerText = d.icon;
            
            const pct = Math.max(0, (currentMonsterHp / d.maxHp) * 100);
            document.getElementById("monsterHpBar").style.width = pct + "%";
            document.getElementById("monsterHpText").innerText = `${Math.max(0, currentMonsterHp)} / ${d.maxHp} HP`;
        }

        // Get Player Stats
        function getPlayerStats() {
            let baseAtk = 10;
            let baseCrit = 5;
            if (player.equippedId) {
                const eq = player.inventory.find(i => i.id === player.equippedId);
                if (eq) {
                    baseAtk += eq.atk;
                    baseCrit += eq.crit;
                }
            }
            return { atk: baseAtk, crit: baseCrit };
        }

        // Player Attack Combat
        function playerAttack() {
            const stats = getPlayerStats();
            const isCrit = Math.random() * 100 < stats.crit;
            let dmg = stats.atk;
            if (isCrit) dmg = Math.floor(dmg * 1.8);

            currentMonsterHp -= dmg;

            // FX
            const avatar = document.getElementById("monsterAvatar");
            avatar.classList.remove("shake");
            void avatar.offsetWidth;
            avatar.classList.add("shake");

            playSound(isCrit ? 'crit' : 'hit');
            spawnDamagePop(dmg, isCrit);

            if (currentMonsterHp <= 0) {
                monsterKilled();
            }

            updateBattleDisplay();
        }

        function spawnDamagePop(dmg, isCrit) {
            const arena = document.getElementById("battleArena");
            const pop = document.createElement("div");
            pop.className = `damage-pop ${isCrit ? 'text-purple-400 text-2xl' : 'text-amber-400 text-lg'}`;
            pop.innerText = (isCrit ? "💥 " : "") + "-" + dmg;
            pop.style.left = (Math.random() * 40 + 30) + "%";
            pop.style.top = "40%";
            arena.appendChild(pop);
            setTimeout(() => pop.remove(), 800);
        }

        function monsterKilled() {
            const d = dungeons[currentDungeonIdx];
            const rewardGold = Math.floor(Math.random() * (d.goldMax - d.goldMin + 1)) + d.goldMin;
            
            player.gold += rewardGold;
            player.wood += d.woodDrop;
            player.iron += d.ironDrop;
            player.soul += d.soulDrop;

            log(`Hạ gục ${d.monster}! Nhận +${rewardGold} Vàng, +${d.woodDrop} Gỗ, +${d.ironDrop} Sắt, +${d.soulDrop} Đá Hồn.`);
            
            currentMonsterHp = d.maxHp;
            updateUI();
        }

        function toggleAutoAttack() {
            const btn = document.getElementById("btnAutoAttack");
            if (autoAttackInterval) {
                clearInterval(autoAttackInterval);
                autoAttackInterval = null;
                btn.innerText = "🤖 Tự Động: OFF";
                btn.className = "py-3 px-5 bg-slate-800 border border-slate-700 hover:bg-slate-700 font-semibold rounded-xl text-slate-300 transition text-sm";
            } else {
                autoAttackInterval = setInterval(() => {
                    playerAttack();
                }, 800);
                btn.innerText = "🤖 Tự Động: ON";
                btn.className = "py-3 px-5 bg-amber-600/30 border border-amber-500/50 hover:bg-amber-600/40 font-bold rounded-xl text-amber-300 transition text-sm shadow-lg shadow-amber-600/20";
            }
        }

        function log(msg) {
            document.getElementById("gameLog").innerText = msg;
        }

        function updateUI() {
            document.getElementById("resGold").innerText = player.gold.toLocaleString();
            document.getElementById("resWood").innerText = player.wood.toLocaleString();
            document.getElementById("resIron").innerText = player.iron.toLocaleString();
            document.getElementById("resSoul").innerText = player.soul.toLocaleString();

            const eq = player.inventory.find(i => i.id === player.equippedId);
            if (eq) {
                document.getElementById("equippedName").innerText = eq.name + ` (+${eq.level})`;
                document.getElementById("equippedAtk").innerText = "+" + eq.atk;
                document.getElementById("equippedCrit").innerText = eq.crit + "%";
            } else {
                document.getElementById("equippedName").innerText = "Tay Không";
                document.getElementById("equippedAtk").innerText = "+5";
                document.getElementById("equippedCrit").innerText = "5%";
            }

            renderRecipes();
            renderInventory();
            updateBattleDisplay();
            saveGame();
        }

        function saveGame() {
            localStorage.setItem("forgeAndConquerSave", JSON.stringify(player));
        }

        function loadGame() {
            const data = localStorage.getItem("forgeAndConquerSave");
            if (data) {
                try {
                    player = Object.assign(player, JSON.parse(data));
                } catch(e) {}
            }
            updateUI();
        }

        // Initialize App
        loadGame();
    </script>
</body>
</html>
