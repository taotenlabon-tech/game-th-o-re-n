<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Forge & Conquer 3D - Next-Gen Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@700;900&family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background: #020408;
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
            background: linear-gradient(135deg, #991b1b 0%, #450a0a 100%);
            border-color: #ef4444;
            box-shadow: 0 0 25px rgba(239, 68, 68, 0.6);
        }

        .rarity-Common { border-color: #475569; }
        .rarity-Rare { border-color: #2563eb; box-shadow: 0 0 12px rgba(37, 99, 235, 0.5); }
        .rarity-Epic { border-color: #9333ea; box-shadow: 0 0 16px rgba(147, 51, 234, 0.6); }
        .rarity-Legendary { border-color: #eab308; box-shadow: 0 0 20px rgba(234, 179, 8, 0.7); }
        .rarity-Mythic { border-color: #f43f5e; box-shadow: 0 0 25px rgba(244, 63, 94, 0.8); }

        .rarity-text-Common { color: #94a3b8; }
        .rarity-text-Rare { color: #60a5fa; }
        .rarity-text-Epic { color: #c084fc; }
        .rarity-text-Legendary { color: #fde047; }
        .rarity-text-Mythic { color: #fb7185; }

        /* Khung kính mờ phong cách game AAA */
        .glass-panel {
            background: linear-gradient(135deg, rgba(15, 23, 42, 0.85) 0%, rgba(3, 7, 18, 0.95) 100%);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(239, 68, 68, 0.2);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col items-center justify-between p-2 sm:p-6 bg-[#020408]">

    <!-- Top Status Bar -->
    <header class="w-full max-w-5xl glass-panel rounded-2xl p-4 mb-4 shadow-2xl flex flex-wrap justify-between items-center gap-4">
        <div class="flex items-center gap-3">
            <div class="w-12 h-12 bg-red-950 border border-red-600/60 rounded-xl flex items-center justify-center text-2xl shadow-[0_0_15px_rgba(239,68,68,0.4)]">
                ⚔️
            </div>
            <div>
                <h1 class="font-cinzel text-xl font-bold text-red-500 tracking-wider">DARK FORGE & CONQUER</h1>
                <p class="text-xs text-slate-400">Cấp Độ Thợ Rèn: <span id="playerLvl" class="text-red-400 font-bold">1</span></p>
            </div>
        </div>

        <div class="flex flex-wrap gap-2.5 text-xs sm:text-sm">
            <div class="bg-slate-950/90 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5 shadow-inner">
                <span>💰 Vàng:</span>
                <span id="resGold" class="text-yellow-400 font-bold">0</span>
            </div>
            <div class="bg-slate-950/90 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5 shadow-inner">
                <span>🪵 Gỗ:</span>
                <span id="resWood" class="text-emerald-400 font-bold">0</span>
            </div>
            <div class="bg-slate-950/90 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5 shadow-inner">
                <span>🪨 Sắt:</span>
                <span id="resIron" class="text-slate-300 font-bold">0</span>
            </div>
            <div class="bg-slate-950/90 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5 shadow-inner">
                <span>🔮 Đá Hồn:</span>
                <span id="resSoul" class="text-purple-400 font-bold">0</span>
            </div>
        </div>
    </header>

    <!-- Navigation Tabs -->
    <nav class="w-full max-w-5xl flex gap-3 mb-4">
        <button id="tabForgeBtn" onclick="switchTab('forge')" class="tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-900 transition flex items-center justify-center gap-2 text-white shadow-lg">
            <span>⚒️</span> LÒ RÈN & KHO VŨ KHÍ
        </button>
        <button id="tabBattleBtn" onclick="switchTab('battle')" class="flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-900 hover:bg-slate-800 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg">
            <span>👹</span> SĂN BOSS HẮC ÁM
        </button>
    </nav>

    <!-- Main Content Container -->
    <main class="w-full max-w-5xl flex-1 flex flex-col">
        
        <!-- PAGE 1: FORGE & INVENTORY -->
        <section id="pageForge" class="grid grid-cols-1 md:grid-cols-2 gap-5 flex-1">
            <div class="glass-panel rounded-2xl p-5 flex flex-col gap-4 shadow-2xl">
                <h2 class="text-lg font-bold text-red-400 border-b border-slate-800 pb-2.5 flex items-center justify-between">
                    <span>⚒️ Bàn Rèn 3D Điện Ảnh</span>
                    <button onclick="gatherResources()" class="text-xs bg-emerald-700 hover:bg-emerald-600 text-white font-bold py-1.5 px-3 rounded-lg transition active:scale-95 shadow">
                        ⛏️ Khai Thác
                    </button>
                </h2>

                <div class="relative w-full h-56 bg-black rounded-xl border border-red-950/60 overflow-hidden flex items-center justify-center shadow-[inset_0_0_30px_rgba(0,0,0,0.9)]">
                    <div id="weapon3DCanvas" class="w-full h-full"></div>
                    <div class="absolute bottom-2 left-3 text-[10px] text-red-400 font-semibold bg-black/80 px-2 py-0.5 rounded border border-red-500/30">
                        ✨ Đồ Họa PBR & Hiệu Ứng Ánh Sáng Thực
                    </div>
                </div>

                <div class="space-y-3">
                    <label class="text-xs text-slate-400 block font-semibold">Chọn Kiếm Cần Rèn:</label>
                    <div id="recipeList" class="grid grid-cols-1 gap-2 max-h-44 overflow-y-auto pr-1"></div>

                    <button id="btnForgeAction" onclick="forgeWeapon()" class="w-full py-3.5 bg-gradient-to-r from-red-700 to-rose-700 hover:from-red-600 hover:to-rose-600 font-bold rounded-xl text-white shadow-lg shadow-red-700/50 active:scale-98 transition flex items-center justify-center gap-2 text-base">
                        <span>🔥</span> RÈN BẢO KIẾM
                    </button>
                </div>
            </div>

            <div class="glass-panel rounded-2xl p-5 flex flex-col gap-3 shadow-2xl">
                <h2 class="text-lg font-bold text-red-400 border-b border-slate-800 pb-2.5 flex justify-between items-center">
                    <span>🎒 Kho Trang Bị</span>
                    <span class="text-xs text-slate-500" id="inventoryCount">0/12</span>
                </h2>

                <div id="inventoryGrid" class="grid grid-cols-1 gap-2.5 flex-1 max-h-[460px] overflow-y-auto pr-1"></div>
            </div>
        </section>

        <!-- PAGE 2: BATTLE & BOSS 3D -->
        <section id="pageBattle" class="hidden flex-1 flex-col gap-4">
            <div class="glass-panel rounded-2xl p-3 flex flex-wrap items-center justify-between gap-2 shadow-lg">
                <span class="text-xs text-slate-400 font-bold">CHỌN HẦM NGỤC BOSS:</span>
                <div class="flex gap-2 flex-wrap" id="dungeonButtons">
                    <button onclick="selectDungeon(0)" class="btn-dungeon border border-emerald-600/60 bg-emerald-950/50 text-emerald-300 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-emerald-800/50 transition">🌲 Yêu Tinh Quỷ</button>
                    <button onclick="selectDungeon(1)" class="btn-dungeon border border-slate-500 bg-slate-800/50 text-slate-300 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-slate-700/50 transition">🗿 Golem Địa Ngục</button>
                    <button onclick="selectDungeon(2)" class="btn-dungeon border border-red-600/60 bg-red-950/50 text-red-300 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-red-800/50 transition">🌋 Hỏa Long Tử Thần</button>
                    <button onclick="selectDungeon(3)" class="btn-dungeon border border-cyan-600/60 bg-cyan-950/50 text-cyan-300 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-cyan-800/50 transition">🌊 Leviathan Vực Sâu</button>
                    <button onclick="selectDungeon(4)" class="btn-dungeon border border-purple-600/60 bg-purple-950/50 text-purple-300 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-purple-800/50 transition">👑 Ma Vương Hắc Ám</button>
                </div>
            </div>

            <div id="battleArena" class="relative glass-panel rounded-2xl p-6 flex-1 flex flex-col items-center justify-between min-h-[440px] overflow-hidden shadow-2xl">
                <div class="text-center z-10">
                    <span id="dungeonName" class="text-xs font-bold uppercase tracking-widest text-red-500">Yêu Tinh Quỷ</span>
                    <h3 id="monsterName" class="text-2xl font-bold font-cinzel text-slate-100 mt-0.5">Goblin Đồ Sát</h3>
                </div>

                <div class="relative my-2 w-full max-w-md h-64 flex flex-col items-center justify-center">
                    <div id="boss3DCanvas" class="w-full h-full cursor-pointer bg-black/80 rounded-2xl border border-red-950 shadow-[inset_0_0_40px_rgba(0,0,0,0.9)]" onclick="playerAttack()"></div>
                    
                    <div class="w-full bg-slate-950 rounded-full h-4 p-0.5 border border-slate-800 overflow-hidden shadow-inner mt-2">
                        <div id="monsterHpBar" class="bg-gradient-to-r from-red-700 to-rose-600 h-full rounded-full transition-all duration-200" style="width: 100%;"></div>
                    </div>
                    <div id="monsterHpText" class="text-xs text-red-400 font-bold mt-1">120 / 120 HP</div>
                </div>

                <div class="w-full max-w-md flex gap-3 z-10">
                    <button onclick="playerAttack()" class="flex-1 py-3.5 bg-gradient-to-r from-red-700 to-rose-700 hover:from-red-600 hover:to-rose-600 font-bold rounded-xl text-white shadow-lg shadow-red-700/50 active:scale-95 transition flex items-center justify-center gap-2 text-base">
                        <span>⚔️</span> TẤN CÔNG (CLICK)
                    </button>
                    <button id="btnAutoAttack" onclick="toggleAutoAttack()" class="py-3.5 px-5 bg-slate-800 border border-slate-700 hover:bg-slate-700 font-semibold rounded-xl text-slate-300 transition text-sm">
                        🤖 Tự Động: OFF
                    </button>
                </div>
            </div>
        </section>

    </main>

    <footer class="w-full max-w-5xl mt-4 glass-panel rounded-xl p-3 text-xs text-slate-400 flex items-center justify-between shadow-lg">
        <span class="font-semibold text-red-400">📜 Nhật Ký:</span>
        <span id="gameLog" class="truncate max-w-md text-right text-slate-300">Sẵn sàng chinh chiến thế giới hắc ám!</span>
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
            gold: 200,
            wood: 50,
            iron: 40,
            soul: 15,
            inventory: [],
            equippedId: null
        };

        const recipes = [
            { id: 'sword_wood', name: 'Đao Gỗ Thâm Sơn', modelType: 'wood', wood: 10, iron: 0, soul: 0, atk: 15, crit: 5, rarity: 'Common' },
            { id: 'sword_iron', name: 'Trường Kiếm Sắt Đẫm Máu', modelType: 'iron', wood: 15, iron: 15, soul: 0, atk: 35, crit: 10, rarity: 'Rare' },
            { id: 'axe_steel', name: 'Rìu Bão Lửa Địa Ngục', modelType: 'axe', wood: 15, iron: 30, soul: 5, atk: 75, crit: 15, rarity: 'Epic' },
            { id: 'sword_emerald', name: 'Bảo Kiếm Lục Bảo Ma Quái', modelType: 'emerald', wood: 20, iron: 40, soul: 8, atk: 140, crit: 20, rarity: 'Epic' },
            { id: 'sword_diamond', name: 'Thánh Kiếm Kim Cương Vĩnh Cửu', modelType: 'diamond', wood: 30, iron: 60, soul: 12, atk: 230, crit: 25, rarity: 'Legendary' },
            { id: 'sword_mythic', name: 'Hủy Diệt Kiếm - Vô Cực Tối Thượng', modelType: 'mythic', wood: 50, iron: 100, soul: 30, atk: 450, crit: 45, rarity: 'Mythic' }
        ];

        let selectedRecipeId = 'sword_wood';

        const dungeons = [
            { name: "Rừng Sương Mù", monster: "Goblin Đồ Sát", bossType: 'goblin', maxHp: 160, goldMin: 15, goldMax: 25, woodDrop: 5, ironDrop: 2, soulDrop: 0 },
            { name: "Hầm Ngục Tăm Tối", monster: "Golem Thạch Anh Đỏ", bossType: 'golem', maxHp: 550, goldMin: 45, goldMax: 75, woodDrop: 2, ironDrop: 10, soulDrop: 2 },
            { name: "Hang Long Xương", monster: "Hỏa Long Tử Thần", bossType: 'dragon', maxHp: 2000, goldMin: 130, goldMax: 280, woodDrop: 0, ironDrop: 20, soulDrop: 6 },
            { name: "Vực Thẳm Băng Giá", monster: "Leviathan Quái Thú Vực Sâu", bossType: 'leviathan', maxHp: 7000, goldMin: 400, goldMax: 800, woodDrop: 0, ironDrop: 50, soulDrop: 15 },
            { name: "Lâu Đài Ma Vương", monster: "Ma Vương Bóng Tối", bossType: 'demon', maxHp: 25000, goldMin: 1500, goldMax: 3000, woodDrop: 15, ironDrop: 100, soulDrop: 40 }
        ];

        let currentDungeonIdx = 0;
        let currentMonsterHp = dungeons[0].maxHp;
        let autoAttackInterval = null;

        let wpScene, wpCamera, wpRenderer, wpMeshGroup;
        let bossScene, bossCamera, bossRenderer, bossMeshGroup;

        function init3DScenes() {
            // Weapon Scene với đồ họa cao cấp
            const wpContainer = document.getElementById("weapon3DCanvas");
            wpScene = new THREE.Scene();
            wpCamera = new THREE.PerspectiveCamera(42, wpContainer.clientWidth / wpContainer.clientHeight, 0.1, 1000);
            wpCamera.position.set(0, 0, 5.2);

            wpRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true, powerPreference: "high-performance" });
            wpRenderer.setSize(wpContainer.clientWidth, wpContainer.clientHeight);
            wpRenderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            wpContainer.appendChild(wpRenderer.domElement);

            // Hệ thống ánh sáng điện ảnh 3 điểm (Three-point lighting)
            const wpLight1 = new THREE.DirectionalLight(0xffffff, 2.5);
            wpLight1.position.set(5, 5, 4);
            wpScene.add(wpLight1);
            const wpLight2 = new THREE.DirectionalLight(0xef4444, 1.8);
            wpLight2.position.set(-5, -3, -2);
            wpScene.add(wpLight2);
            const wpRim = new THREE.DirectionalLight(0x38bdf8, 1.5);
            wpRim.position.set(0, 6, -3);
            wpScene.add(wpRim);
            wpScene.add(new THREE.AmbientLight(0xffffff, 0.6));

            wpMeshGroup = new THREE.Group();
            wpScene.add(wpMeshGroup);

            // Boss Scene
            const bossContainer = document.getElementById("boss3DCanvas");
            bossScene = new THREE.Scene();
            bossCamera = new THREE.PerspectiveCamera(42, bossContainer.clientWidth / bossContainer.clientHeight, 0.1, 1000);
            bossCamera.position.set(0, 0.2, 5.8);

            bossRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true, powerPreference: "high-performance" });
            bossRenderer.setSize(bossContainer.clientWidth, bossContainer.clientHeight);
            bossRenderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            bossContainer.appendChild(bossRenderer.domElement);

            const bossLight1 = new THREE.DirectionalLight(0xffffff, 2.5);
            bossLight1.position.set(4, 8, 5);
            bossScene.add(bossLight1);
            const bossLight2 = new THREE.DirectionalLight(0xd97706, 2.0);
            bossLight2.position.set(-5, -2, -2);
            bossScene.add(bossLight2);
            const bossRim = new THREE.DirectionalLight(0xf43f5e, 1.5);
            bossRim.position.set(0, 5, -4);
            bossScene.add(bossRim);
            bossScene.add(new THREE.AmbientLight(0xffffff, 0.7));

            bossMeshGroup = new THREE.Group();
            bossScene.add(bossMeshGroup);

            animate3D();
            updateWeapon3DModel('wood');
            updateBoss3DModel('goblin');
        }

        function resize3DCanvases() {
            setTimeout(() => {
                const bossContainer = document.getElementById("boss3DCanvas");
                if (bossContainer && bossRenderer && bossCamera) {
                    const w = bossContainer.clientWidth || 300;
                    const h = bossContainer.clientHeight || 240;
                    bossCamera.aspect = w / h;
                    bossCamera.updateProjectionMatrix();
                    bossRenderer.setSize(w, h);
                }

                const wpContainer = document.getElementById("weapon3DCanvas");
                if (wpContainer && wpRenderer && wpCamera) {
                    const w = wpContainer.clientWidth || 300;
                    const h = wpContainer.clientHeight || 220;
                    wpCamera.aspect = w / h;
                    wpCamera.updateProjectionMatrix();
                    wpRenderer.setSize(w, h);
                }
            }, 50);
        }

        // TẠO LƯỠI KIẾM VÁT CẠNH ĐA GIÁC ĐỒ HỌA CAO (HIGH-POLY BLADE)
        function createHighPolyBlade(width, length, thickness, colorHex, metalness, roughness, emissiveHex = 0x000000) {
            const shape = new THREE.Shape();
            shape.moveTo(0, 0);
            shape.lineTo(width / 2, 0.3);
            shape.lineTo(width / 2, length - 0.8);
            shape.lineTo(0, length + 0.4); // Mũi kiếm cực nhọn điện ảnh
            shape.lineTo(-width / 2, length - 0.8);
            shape.lineTo(-width / 2, 0.3);
            shape.closePath();

            const extrudeSettings = {
                steps: 2,
                depth: thickness,
                bevelEnabled: true,
                bevelThickness: thickness / 1.8,
                bevelSize: width / 4,
                bevelSegments: 5
            };

            const geo = new THREE.ExtrudeGeometry(shape, extrudeSettings);
            geo.center();

            const mat = new THREE.MeshStandardMaterial({
                color: colorHex,
                metalness: metalness,
                roughness: roughness,
                emissive: emissiveHex,
                emissiveIntensity: emissiveHex !== 0x000000 ? 0.7 : 0,
                wireframe: false
            });

            return new THREE.Mesh(geo, mat);
        }

        function createWeaponMesh(modelType) {
            const group = new THREE.Group();

            if (modelType === 'wood') {
                const blade = createHighPolyBlade(0.48, 3.5, 0.14, 0x451a03, 0.15, 0.75);
                blade.position.y = 0.8;
                group.add(blade);
            } else if (modelType === 'iron') {
                const blade = createHighPolyBlade(0.55, 4.1, 0.12, 0xccbdc, 0.96, 0.12);
                blade.position.y = 1.0;
                group.add(blade);

                const guardGeo = new THREE.BoxGeometry(1.6, 0.2, 0.4);
                const guardMat = new THREE.MeshStandardMaterial({ color: 0x0f172a, metalness: 0.98, roughness: 0.1 });
                const guard = new THREE.Mesh(guardGeo, guardMat);
                guard.position.y = -0.7;
                group.add(guard);
            } else if (modelType === 'axe') {
                const handleGeo = new THREE.CylinderGeometry(0.12, 0.12, 4.0, 16);
                const handleMat = new THREE.MeshStandardMaterial({ color: 0x0f172a, metalness: 0.8, roughness: 0.3 });
                const handle = new THREE.Mesh(handleGeo, handleMat);
                group.add(handle);

                const headGeo = new THREE.ConeGeometry(1.3, 2.0, 5);
                const headMat = new THREE.MeshStandardMaterial({ color: 0x991b1b, metalness: 0.92, roughness: 0.15, emissive: 0x7f1d1d, emissiveIntensity: 0.6 });
                const head = new THREE.Mesh(headGeo, headMat);
                head.rotation.z = Math.PI / 2;
                head.position.set(0.75, 0.8, 0);
                group.add(head);
            } else if (modelType === 'emerald') {
                const blade = createHighPolyBlade(0.6, 4.3, 0.14, 0x059669, 0.9, 0.1, 0x047857);
                blade.position.y = 1.1;
                group.add(blade);

                const guardGeo = new THREE.OctahedronGeometry(0.45);
                const guardMat = new THREE.MeshStandardMaterial({ color: 0x10b981, metalness: 0.9, emissive: 0x059669, emissiveIntensity: 0.5 });
                const guard = new THREE.Mesh(guardGeo, guardMat);
                guard.position.y = -0.7;
                group.add(guard);
            } else if (modelType === 'diamond') {
                const blade = createHighPolyBlade(0.65, 4.6, 0.16, 0x38bdf8, 0.98, 0.04, 0x0284c7);
                blade.position.y = 1.2;
                group.add(blade);

                const guardGeo = new THREE.BoxGeometry(1.9, 0.25, 0.45);
                const guardMat = new THREE.MeshStandardMaterial({ color: 0x0284c7, metalness: 0.98, roughness: 0.05 });
                const guard = new THREE.Mesh(guardGeo, guardMat);
                guard.position.y = -0.75;
                group.add(guard);
            } else if (modelType === 'mythic') {
                const blade = createHighPolyBlade(0.8, 5.0, 0.2, 0xf43f5e, 0.98, 0.02, 0xe11d48);
                blade.position.y = 1.3;
                group.add(blade);

                const ringGeo = new THREE.TorusKnotGeometry(1.0, 0.18, 64, 24);
                const ringMat = new THREE.MeshStandardMaterial({ color: 0x881337, metalness: 0.9, emissive: 0xf43f5e, emissiveIntensity: 1.0 });
                const ring = new THREE.Mesh(ringGeo, ringMat);
                ring.scale.set(0.6, 0.6, 0.6);
                ring.position.y = -0.6;
                group.add(ring);
            }

            return group;
        }

        function updateWeapon3DModel(modelType) {
            while(wpMeshGroup.children.length > 0) { 
                wpMeshGroup.remove(wpMeshGroup.children[0]); 
            }
            const mesh = createWeaponMesh(modelType);
            wpMeshGroup.add(mesh);
        }

        // TẠO BOSS HẮC ÁM ĐỒ HỌA CAO CẤP & CHI TIẾT
        function createBossMesh(type) {
            const group = new THREE.Group();

            if (type === 'goblin') {
                const headGeo = new THREE.BoxGeometry(1.5, 1.5, 1.5);
                const skinMat = new THREE.MeshStandardMaterial({ color: 0x064e3b, roughness: 0.5, metalness: 0.2 });
                const head = new THREE.Mesh(headGeo, skinMat);
                head.position.y = 0.5;
                group.add(head);

                const jawGeo = new THREE.BoxGeometry(1.3, 0.6, 1.3);
                const jaw = new THREE.Mesh(jawGeo, skinMat);
                jaw.position.set(0, -0.45, 0.1);
                group.add(jaw);

                const eyeGeo = new THREE.ConeGeometry(0.2, 0.6, 6);
                const eyeMat = new THREE.MeshBasicMaterial({ color: 0xf59e0b });
                const eye1 = new THREE.Mesh(eyeGeo, eyeMat);
                eye1.rotation.x = Math.PI / 2;
                eye1.position.set(-0.4, 0.5, 0.8);
                const eye2 = eye1.clone();
                eye2.position.x = 0.4;
                group.add(eye1); group.add(eye2);
            } else if (type === 'golem') {
                const stoneMat = new THREE.MeshStandardMaterial({ color: 0x1e293b, roughness: 0.85, metalness: 0.5 });

                const headGeo = new THREE.BoxGeometry(2.0, 1.4, 1.7);
                const head = new THREE.Mesh(headGeo, stoneMat);
                head.position.y = 1.2;
                group.add(head);

                const bodyGeo = new THREE.BoxGeometry(3.2, 2.6, 2.0);
                const body = new THREE.Mesh(bodyGeo, stoneMat);
                body.position.y = -0.7;
                group.add(body);

                const coreGeo = new THREE.BoxGeometry(0.9, 0.45, 0.45);
                const coreMat = new THREE.MeshBasicMaterial({ color: 0xd97706 });
                const core = new THREE.Mesh(coreGeo, coreMat);
                core.position.set(0, 1.1, 0.88);
                group.add(core);
            } else if (type === 'dragon') {
                const dragonMat = new THREE.MeshStandardMaterial({ color: 0x450a0a, roughness: 0.25, metalness: 0.4, emissive: 0x7f1d1d, emissiveIntensity: 0.6 });

                const headGeo = new THREE.ConeGeometry(1.5, 3.4, 6);
                const head = new THREE.Mesh(headGeo, dragonMat);
                head.rotation.x = Math.PI / 2.3;
                head.position.set(0, 0.2, 0.4);
                group.add(head);

                const hornGeo = new THREE.ConeGeometry(0.38, 2.0, 5);
                const hornMat = new THREE.MeshStandardMaterial({ color: 0x020617, metalness: 0.95 });
                const horn1 = new THREE.Mesh(hornGeo, hornMat);
                horn1.rotation.x = -Math.PI / 3;
                horn1.position.set(-0.7, 1.5, -0.3);
                const horn2 = horn1.clone();
                horn2.position.x = 0.7;
                group.add(horn1); group.add(horn2);

                const eyeGeo = new THREE.SphereGeometry(0.22, 12, 12);
                const eyeMat = new THREE.MeshBasicMaterial({ color: 0xf43f5e });
                const eye1 = new THREE.Mesh(eyeGeo, eyeMat);
                eye1.position.set(-0.5, 0.6, 0.9);
                const eye2 = eye1.clone();
                eye2.position.x = 0.5;
                group.add(eye1); group.add(eye2);
            } else if (type === 'leviathan') {
                const waterMat = new THREE.MeshStandardMaterial({ color: 0x0284c7, roughness: 0.15, metalness: 0.8, emissive: 0x0369a1, emissiveIntensity: 0.4 });
                const bodyGeo = new THREE.CylinderGeometry(1.6, 2.8, 4.5, 16);
                const body = new THREE.Mesh(bodyGeo, waterMat);
                body.rotation.z = Math.PI / 2;
                group.add(body);

                const tentacleGeo = new THREE.TorusGeometry(2.5, 0.45, 8, 28);
                const tentacleMat = new THREE.MeshStandardMaterial({ color: 0x0f172a, roughness: 0.2, metalness: 0.8 });
                const tentacle = new THREE.Mesh(tentacleGeo, tentacleMat);
                tentacle.rotation.x = Math.PI / 2;
                group.add(tentacle);
            } else if (type === 'demon') {
                const demonMat = new THREE.MeshStandardMaterial({ color: 0x09090b, roughness: 0.2, metalness: 0.6, emissive: 0x581c87, emissiveIntensity: 0.9 });
                const bodyGeo = new THREE.BoxGeometry(2.6, 3.8, 2.0);
                const body = new THREE.Mesh(bodyGeo, demonMat);
                group.add(body);

                const hornGeo = new THREE.ConeGeometry(0.55, 3.0, 5);
                const hornMat = new THREE.MeshStandardMaterial({ color: 0x450a0a, metalness: 0.95, emissive: 0x991b1b, emissiveIntensity: 0.5 });
                const horn1 = new THREE.Mesh(hornGeo, hornMat);
                horn1.position.set(-1.3, 2.6, 0);
                horn1.rotation.z = -Math.PI / 3.5;
                const horn2 = horn1.clone();
                horn2.position.x = 1.3;
                horn2.rotation.z = Math.PI / 3.5;
                group.add(horn1); group.add(horn2);

                const eyeGeo = new THREE.BoxGeometry(0.6, 0.18, 0.18);
                const eyeMat = new THREE.MeshBasicMaterial({ color: 0xf43f5e });
                const eye1 = new THREE.Mesh(eyeGeo, eyeMat);
                eye1.position.set(-0.7, 1.3, 1.02);
                const eye2 = eye1.clone();
                eye2.position.x = 0.7;
                group.add(eye1); group.add(eye2);
            }

            return group;
        }

        function updateBoss3DModel(type) {
            while(bossMeshGroup.children.length > 0) { 
                bossMeshGroup.remove(bossMeshGroup.children[0]); 
            }
            const mesh = createBossMesh(type);
            bossMeshGroup.add(mesh);
        }

        let time3D = 0;
        function animate3D() {
            requestAnimationFrame(animate3D);
            time3D += 0.03;

            if (wpMeshGroup) {
                wpMeshGroup.rotation.y += 0.025;
            }

            if (bossMeshGroup) {
                bossMeshGroup.rotation.y = Math.sin(time3D * 0.5) * 0.4;
                bossMeshGroup.position.y = Math.sin(time3D * 1.5) * 0.15;
            }

            wpRenderer.render(wpScene, wpCamera);
            bossRenderer.render(bossScene, bossCamera);
        }

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

                btnForge.className = "tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-900 transition flex items-center justify-center gap-2 text-white shadow-lg";
                btnBattle.className = "flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-900 hover:bg-slate-800 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg";
            } else {
                pageForge.classList.add("hidden");
                pageForge.classList.remove("grid");
                pageBattle.classList.remove("hidden");
                pageBattle.classList.add("flex");

                btnBattle.className = "tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-900 transition flex items-center justify-center gap-2 text-white shadow-lg";
                btnForge.className = "flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-900 hover:bg-slate-800 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg";
                
                resize3DCanvases();
            }
        }

        function gatherResources() {
            player.wood += Math.floor(Math.random() * 5) + 4;
            player.iron += Math.floor(Math.random() * 4) + 3;
            log("Đã khai thác thêm nguyên liệu quý hiếm!");
            updateUI();
        }

        function renderRecipes() {
            const list = document.getElementById("recipeList");
            list.innerHTML = "";
            recipes.forEach(r => {
                const isSelected = r.id === selectedRecipeId;
                const div = document.createElement("div");
                div.className = `p-2.5 rounded-xl border cursor-pointer transition flex items-center justify-between ${isSelected ? 'border-red-500 bg-red-950/50' : 'border-slate-800 bg-slate-950/60 hover:bg-slate-800/40'}`;
                div.onclick = () => { 
                    selectedRecipeId = r.id; 
                    updateWeapon3DModel(r.modelType);
                    renderRecipes(); 
                };
                
                div.innerHTML = `
                    <div>
                        <div class="font-bold text-xs sm:text-sm rarity-text-${r.rarity}">${r.name}</div>
                        <div class="text-[11px] text-slate-400">ATK: +${r.atk} | CRIT: ${r.crit}%</div>
                    </div>
                    <div class="text-right text-[11px] text-slate-400">
                        <div>🪵 ${r.wood} | 🪨 ${r.iron} | 🔮 ${r.soul}</div>
                    </div>
                `;
                list.appendChild(div);
            });
        }

        function forgeWeapon() {
            const recipe = recipes.find(r => r.id === selectedRecipeId);
            if (!recipe) return;

            if (player.inventory.length >= 12) {
                log("Túi đồ đã đầy (Tối đa 12 vũ khí)!");
                return;
            }

            if (player.wood < recipe.wood || player.iron < recipe.iron || player.soul < recipe.soul) {
                log("Không đủ nguyên liệu để rèn bảo kiếm này!");
                return;
            }

            player.wood -= recipe.wood;
            player.iron -= recipe.iron;
            player.soul -= recipe.soul;

            const newWeapon = {
                id: 'wp_' + Date.now(),
                name: recipe.name,
                modelType: recipe.modelType,
                atk: recipe.atk,
                crit: recipe.crit,
                rarity: recipe.rarity,
                level: 1
            };

            player.inventory.push(newWeapon);
            if (!player.equippedId) player.equippedId = newWeapon.id;

            playSound('forge');
            log(`Rèn thành công bảo kiếm: ${newWeapon.name}!`);
            updateUI();
        }

        function equipWeapon(id) {
            player.equippedId = id;
            log("Đã trang bị vũ khí hắc ám mới!");
            updateUI();
        }

        function upgradeWeapon(id) {
            const item = player.inventory.find(i => i.id === id);
            if (!item) return;
            const cost = item.level * 50;
            if (player.gold < cost) {
                log(`Cần ${cost} Vàng để cường hóa!`);
                return;
            }
            player.gold -= cost;
            item.level += 1;
            item.atk += Math.floor(item.atk * 0.35);
            log(`Cường hóa thành công ${item.name} lên +${item.level}!`);
            updateUI();
        }

        function sellWeapon(id) {
            const idx = player.inventory.findIndex(i => i.id === id);
            if (idx === -1) return;
            if (player.equippedId === id) player.equippedId = null;
            player.inventory.splice(idx, 1);
            player.gold += 50;
            log("Đã bán vũ khí lấy +50 Vàng!");
            updateUI();
        }

        function renderInventory() {
            const grid = document.getElementById("inventoryGrid");
            document.getElementById("inventoryCount").innerText = `${player.inventory.length}/12`;
            grid.innerHTML = "";

            if (player.inventory.length === 0) {
                grid.innerHTML = `<div class="text-xs text-slate-500 text-center py-8">Kho trang bị trống. Hãy rèn bảo kiếm ngay!</div>`;
                return;
            }

            player.inventory.forEach(item => {
                const isEquipped = item.id === player.equippedId;
                const div = document.createElement("div");
                div.className = `p-3 rounded-xl border rarity-${item.rarity} bg-slate-950/85 flex items-center justify-between gap-2 shadow-md`;

                div.innerHTML = `
                    <div>
                        <div class="font-bold text-xs sm:text-sm rarity-text-${item.rarity}">${item.name} <span class="text-red-400">+${item.level}</span></div>
                        <div class="text-[10px] text-slate-400">ATK: +${item.atk} | CRIT: ${item.crit}%</div>
                    </div>
                    <div class="flex gap-1">
                        ${isEquipped 
                            ? `<span class="text-[10px] bg-red-600/20 text-red-400 px-2 py-1 rounded border border-red-500/30 font-bold">Đang dùng</span>` 
                            : `<button onclick="equipWeapon('${item.id}')" class="text-[10px] bg-slate-800 hover:bg-slate-700 text-slate-200 px-2 py-1 rounded border border-slate-700">Dùng</button>`
                        }
                        <button onclick="upgradeWeapon('${item.id}')" class="text-[10px] bg-red-950/70 hover:bg-red-900 text-red-200 px-2 py-1 rounded border border-red-700/50">Cường hóa (${item.level*50}G)</button>
                        <button onclick="sellWeapon('${item.id}')" class="text-[10px] bg-rose-950/60 hover:bg-rose-900 text-rose-300 px-1.5 py-1 rounded border border-rose-800/40">Bán</button>
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
            document.getElementById("monsterHpText").innerText = `${Math.max(0, currentMonsterHp)} / ${d.maxHp} HP`;
        }

        function getPlayerStats() {
            let baseAtk = 18;
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

        function playerAttack() {
            const stats = getPlayerStats();
            const isCrit = Math.random() * 100 < stats.crit;
            let dmg = stats.atk;
            if (isCrit) dmg = Math.floor(dmg * 2.2);

            currentMonsterHp -= dmg;

            const bossCanvas = document.getElementById("boss3DCanvas");
            bossCanvas.classList.remove("shake");
            void bossCanvas.offsetWidth;
            bossCanvas.classList.add("shake");

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
            pop.className = `damage-pop ${isCrit ? 'text-red-500 text-2xl' : 'text-amber-400 text-lg'}`;
            pop.innerText = (isCrit ? "💥 CRIT " : "") + "-" + dmg;
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

            log(`Đã tiêu diệt ${d.monster}! Nhận +${rewardGold} Vàng, +${d.woodDrop} Gỗ, +${d.ironDrop} Sắt, +${d.soulDrop} Đá Hồn.`);
            
            currentMonsterHp = d.maxHp;
            updateUI();
        }

        function toggleAutoAttack() {
            const btn = document.getElementById("btnAutoAttack");
            if (autoAttackInterval) {
                clearInterval(autoAttackInterval);
                autoAttackInterval = null;
                btn.innerText = "🤖 Tự Động: OFF";
                btn.className = "py-3.5 px-5 bg-slate-800 border border-slate-700 hover:bg-slate-700 font-semibold rounded-xl text-slate-300 transition text-sm";
            } else {
                autoAttackInterval = setInterval(() => {
                    playerAttack();
                }, 750);
                btn.innerText = "🤖 Tự Động: ON";
                btn.className = "py-3.5 px-5 bg-red-700/40 border border-red-600 hover:bg-red-700/50 font-bold rounded-xl text-red-300 transition text-sm shadow-lg shadow-red-700/40";
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

            renderRecipes();
            renderInventory();
            updateBattleDisplay();
            saveGame();
        }

        function saveGame() {
            localStorage.setItem("nextGenDarkForgeSave", JSON.stringify(player));
        }

        function loadGame() {
            const data = localStorage.getItem("nextGenDarkForgeSave");
            if (data) {
                try {
                    player = Object.assign(player, JSON.parse(data));
                } catch(e) {}
            }
            updateUI();
        }

        window.onload = () => {
            init3DScenes();
            loadGame();
            window.addEventListener('resize', resize3DCanvases);
        };
    </script>
</body>
</html>
