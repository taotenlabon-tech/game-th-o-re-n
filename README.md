<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Forge & Conquer 3D - Rèn Vũ Khí & Diệt Quái</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Three.js CDN cho Đồ Họa 3D -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@700&family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background: #090d16;
            color: #f8fafc;
            overflow-x: hidden;
        }
        .font-cinzel { font-family: 'Cinzel', serif; }
        
        .shake {
            animation: shake 0.3s cubic-bezier(.36,.07,.19,.97) both;
        }
        @keyframes shake {
            10%, 90% { transform: translate3d(-2px, 0, 0); }
            20%, 80% { transform: translate3d(4px, 0, 0); }
            30%, 50%, 70% { transform: translate3d(-6px, 0, 0); }
            40%, 60% { transform: translate3d(6px, 0, 0); }
        }

        .damage-pop {
            position: absolute;
            animation: floatUp 0.8s ease-out forwards;
            pointer-events: none;
            font-weight: 800;
            text-shadow: 0 0 10px rgba(0,0,0,0.9);
            z-index: 50;
        }
        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0) scale(0.8); }
            50% { opacity: 1; transform: translateY(-35px) scale(1.3); }
            100% { opacity: 0; transform: translateY(-70px) scale(1); }
        }

        .tab-btn-active {
            background: linear-gradient(135deg, #d97706 0%, #b45309 100%);
            border-color: #f59e0b;
            box-shadow: 0 0 20px rgba(245, 158, 11, 0.4);
        }

        /* Phân cấp hiếm */
        .rarity-Common { border-color: #94a3b8; }
        .rarity-Rare { border-color: #3b82f6; box-shadow: 0 0 10px rgba(59, 130, 246, 0.3); }
        .rarity-Epic { border-color: #a855f7; box-shadow: 0 0 15px rgba(168, 85, 247, 0.4); }
        .rarity-Legendary { border-color: #eab308; box-shadow: 0 0 20px rgba(234, 179, 8, 0.6); }

        .rarity-text-Common { color: #94a3b8; }
        .rarity-text-Rare { color: #60a5fa; }
        .rarity-text-Epic { color: #c084fc; }
        .rarity-text-Legendary { color: #fde047; }
    </style>
</head>
<body class="min-h-screen flex flex-col items-center justify-between p-2 sm:p-6 bg-slate-950">

    <!-- Top Status Bar -->
    <header class="w-full max-w-5xl bg-slate-900/90 border border-slate-800/80 backdrop-blur-md rounded-2xl p-4 mb-4 shadow-2xl flex flex-wrap justify-between items-center gap-4">
        <div class="flex items-center gap-3">
            <div class="w-12 h-12 bg-amber-600/20 border border-amber-500/50 rounded-xl flex items-center justify-center text-2xl shadow-inner">
                ⚒️
            </div>
            <div>
                <h1 class="font-cinzel text-xl font-bold text-amber-400 tracking-wider">FORGE & CONQUER 3D</h1>
                <p class="text-xs text-slate-400">Cấp Độ Thợ Rèn: <span id="playerLvl" class="text-amber-300 font-bold">1</span></p>
            </div>
        </div>

        <div class="flex flex-wrap gap-2.5 text-xs sm:text-sm">
            <div class="bg-slate-950/80 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5">
                <span>💰 Vàng:</span>
                <span id="resGold" class="text-yellow-400 font-bold">0</span>
            </div>
            <div class="bg-slate-950/80 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5">
                <span>🪵 Gỗ:</span>
                <span id="resWood" class="text-emerald-400 font-bold">0</span>
            </div>
            <div class="bg-slate-950/80 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5">
                <span>🪨 Sắt:</span>
                <span id="resIron" class="text-slate-300 font-bold">0</span>
            </div>
            <div class="bg-slate-950/80 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5">
                <span>🔮 Đá Hồn:</span>
                <span id="resSoul" class="text-purple-400 font-bold">0</span>
            </div>
        </div>
    </header>

    <!-- Navigation Tabs -->
    <nav class="w-full max-w-5xl flex gap-3 mb-4">
        <button id="tabForgeBtn" onclick="switchTab('forge')" class="tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 transition flex items-center justify-center gap-2 text-white shadow-lg">
            <span>🔨</span> RÈN VŨ KHÍ 3D & KHO
        </button>
        <button id="tabBattleBtn" onclick="switchTab('battle')" class="flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 hover:bg-slate-700 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg">
            <span>⚔️</span> PLAY / CHIẾN ĐẤU BOSS 3D
        </button>
    </nav>

    <!-- Main Content Container -->
    <main class="w-full max-w-5xl flex-1 flex flex-col">
        
        <!-- ================= PAGE 1: FORGE & INVENTORY ================= -->
        <section id="pageForge" class="grid grid-cols-1 md:grid-cols-2 gap-5 flex-1">
            
            <!-- Lò Rèn Vũ Khí & Viewport 3D -->
            <div class="bg-slate-900/80 border border-slate-800/80 backdrop-blur rounded-2xl p-5 flex flex-col gap-4 shadow-xl">
                <h2 class="text-lg font-bold text-amber-400 border-b border-slate-800 pb-2.5 flex items-center justify-between">
                    <span>🔨 Lò Rèn & Xem Vũ Khí 3D</span>
                    <button onclick="gatherResources()" class="text-xs bg-emerald-600 hover:bg-emerald-500 text-white font-bold py-1.5 px-3 rounded-lg transition active:scale-95 shadow">
                        ⛏️ Khai Thác Nguyên Liệu
                    </button>
                </h2>

                <!-- Viewport Xem Vũ Khí 3D -->
                <div class="relative w-full h-48 bg-slate-950/90 rounded-xl border border-slate-800 overflow-hidden flex items-center justify-center">
                    <div id="weapon3DCanvas" class="w-full h-full"></div>
                    <div class="absolute bottom-2 left-3 text-[10px] text-amber-400 font-semibold bg-slate-900/80 px-2 py-0.5 rounded border border-amber-500/30">
                        🔄 Xoay Mẫu Vũ Khí 3D
                    </div>
                </div>

                <!-- Chọn công thức rèn -->
                <div class="space-y-3">
                    <label class="text-xs text-slate-400 block font-semibold">Chọn Công Thức Rèn Vũ Khí:</label>
                    <div id="recipeList" class="grid grid-cols-1 gap-2 max-h-44 overflow-y-auto pr-1">
                        <!-- Recipes dynamically inserted -->
                    </div>

                    <button id="btnForgeAction" onclick="forgeWeapon()" class="w-full py-3.5 bg-gradient-to-r from-amber-600 to-amber-500 hover:from-amber-500 hover:to-amber-400 font-bold rounded-xl text-slate-950 shadow-lg shadow-amber-600/30 active:scale-98 transition flex items-center justify-center gap-2 text-base">
                        <span>🔥</span> RÈN VŨ KHÍ NÀY
                    </button>
                </div>
            </div>

            <!-- Kho Vũ Khí -->
            <div class="bg-slate-900/80 border border-slate-800/80 backdrop-blur rounded-2xl p-5 flex flex-col gap-3 shadow-xl">
                <h2 class="text-lg font-bold text-amber-400 border-b border-slate-800 pb-2.5 flex justify-between items-center">
                    <span>🎒 Kho Vũ Khí Của Bạn</span>
                    <span class="text-xs text-slate-500" id="inventoryCount">0/12</span>
                </h2>

                <div id="inventoryGrid" class="grid grid-cols-1 gap-2.5 flex-1 max-h-[460px] overflow-y-auto pr-1">
                    <!-- Inventory items dynamically inserted -->
                </div>
            </div>
        </section>

        <!-- ================= PAGE 2: BATTLE & BOSS 3D ================= -->
        <section id="pageBattle" class="hidden flex-1 flex-col gap-4">
            
            <!-- Selector Vùng Đất -->
            <div class="bg-slate-900/80 border border-slate-800/80 backdrop-blur rounded-2xl p-3 flex flex-wrap items-center justify-between gap-2 shadow-lg">
                <span class="text-xs text-slate-400 font-bold">CHỌN VÙNG ĐẤT SĂN BOSS:</span>
                <div class="flex gap-2" id="dungeonButtons">
                    <button onclick="selectDungeon(0)" class="btn-dungeon border border-emerald-500/50 bg-emerald-950/40 text-emerald-300 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-emerald-800/50 transition">🌲 Rừng Yêu Tinh</button>
                    <button onclick="selectDungeon(1)" class="btn-dungeon border border-slate-600 bg-slate-800/40 text-slate-300 text-xs px-3 py-1.5 rounded-lg font-semibold hover:bg-slate-700/50 transition">🗿 Golem Cổ Đại</button>
                    <button onclick="selectDungeon(2)" class="btn-dungeon border border-red-500/50 bg-red-950/40 text-red-300 text-xs px-3 py-1.5 rounded-lg font-semibold hover:bg-red-800/50 transition">🌋 Rồng Lửa Cổ Đại</button>
                </div>
            </div>

            <!-- Main Combat Arena có Khung Boss 3D -->
            <div id="battleArena" class="relative bg-slate-900/90 border border-slate-800/80 rounded-2xl p-6 flex-1 flex flex-col items-center justify-between min-h-[420px] overflow-hidden shadow-2xl">
                
                <!-- Tên Boss -->
                <div class="text-center z-10">
                    <span id="dungeonName" class="text-xs font-bold uppercase tracking-widest text-amber-500">Rừng Yêu Tinh</span>
                    <h3 id="monsterName" class="text-2xl font-bold font-cinzel text-slate-100 mt-0.5">Yêu Tinh Rừng 3D</h3>
                </div>

                <!-- Canvas Boss 3D -->
                <div class="relative my-2 w-full max-w-sm h-64 flex flex-col items-center justify-center">
                    <div id="boss3DCanvas" class="w-full h-full cursor-pointer" onclick="playerAttack()"></div>
                    
                    <!-- Health Bar Boss -->
                    <div class="w-full bg-slate-950 rounded-full h-4 p-0.5 border border-slate-800 overflow-hidden shadow-inner mt-2">
                        <div id="monsterHpBar" class="bg-gradient-to-r from-red-600 to-rose-500 h-full rounded-full transition-all duration-200" style="width: 100%;"></div>
                    </div>
                    <div id="monsterHpText" class="text-xs text-slate-400 font-bold mt-1">100 / 100 HP</div>
                </div>

                <!-- Nút Tấn Công -->
                <div class="w-full max-w-md flex gap-3 z-10">
                    <button onclick="playerAttack()" class="flex-1 py-3.5 bg-gradient-to-r from-red-600 to-rose-600 hover:from-red-500 hover:to-rose-500 font-bold rounded-xl text-white shadow-lg shadow-red-600/30 active:scale-95 transition flex items-center justify-center gap-2 text-base">
                        <span>⚔️</span> TẤN CÔNG (CLICK)
                    </button>
                    <button id="btnAutoAttack" onclick="toggleAutoAttack()" class="py-3.5 px-5 bg-slate-800 border border-slate-700 hover:bg-slate-700 font-semibold rounded-xl text-slate-300 transition text-sm">
                        🤖 Tự Động: OFF
                    </button>
                </div>
            </div>
        </section>

    </main>

    <!-- Nhật ký thông báo -->
    <footer class="w-full max-w-5xl mt-4 bg-slate-900/60 border border-slate-800/80 backdrop-blur rounded-xl p-3 text-xs text-slate-400 flex items-center justify-between shadow-lg">
        <span class="font-semibold text-amber-500/90">📜 Nhật Ký:</span>
        <span id="gameLog" class="truncate max-w-md text-right text-slate-300">Chào mừng bạn đến với Forge & Conquer 3D!</span>
    </footer>

    <!-- Logic Game & Đồ Họa Three.js -->
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
                    osc.frequency.setValueAtTime(160, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(30, audioCtx.currentTime + 0.08);
                    gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.08);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.08);
                } else if (type === 'crit') {
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(320, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(650, audioCtx.currentTime + 0.15);
                    gain.gain.setValueAtTime(0.4, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.15);
                } else if (type === 'forge') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(523.25, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(1046.5, audioCtx.currentTime + 0.2);
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
            gold: 100,
            wood: 30,
            iron: 20,
            soul: 5,
            inventory: [],
            equippedId: null
        };

        const recipes = [
            { id: 'sword_wood', name: 'Kiếm Gỗ Tập Sự 3D', modelType: 'wood', wood: 10, iron: 0, soul: 0, atk: 10, crit: 5, rarity: 'Common' },
            { id: 'sword_iron', name: 'Trường Kiếm Sắt 3D', modelType: 'iron', wood: 15, iron: 15, soul: 0, atk: 28, crit: 10, rarity: 'Rare' },
            { id: 'axe_steel', name: 'Rìu Chiến Thép 3D', modelType: 'axe', wood: 15, iron: 30, soul: 3, atk: 60, crit: 15, rarity: 'Epic' },
            { id: 'sword_holy', name: 'Thánh Kiếm Diệt Đao 3D', modelType: 'holy', wood: 40, iron: 60, soul: 12, atk: 150, crit: 30, rarity: 'Legendary' }
        ];

        let selectedRecipeId = 'sword_wood';

        const dungeons = [
            { name: "Rừng Yêu Tinh", monster: "Yêu Tinh Rừng 3D", bossType: 'goblin', maxHp: 120, goldMin: 10, goldMax: 20, woodDrop: 5, ironDrop: 2, soulDrop: 0 },
            { name: "Gạc Hầm Ngục", monster: "Golem Cổ Đại 3D", bossType: 'golem', maxHp: 450, goldMin: 35, goldMax: 60, woodDrop: 2, ironDrop: 10, soulDrop: 2 },
            { name: "Hang Rồng Lửa", monster: "Hỏa Rồng Cổ Đại 3D", bossType: 'dragon', maxHp: 1500, goldMin: 100, goldMax: 200, woodDrop: 0, ironDrop: 20, soulDrop: 5 }
        ];

        let currentDungeonIdx = 0;
        let currentMonsterHp = dungeons[0].maxHp;
        let autoAttackInterval = null;

        // --- THREE.JS GRAPHICS CONTROLLER (3D WEAPONS & 3D BOSS) ---
        let wpScene, wpCamera, wpRenderer, wpMeshGroup;
        let bossScene, bossCamera, bossRenderer, bossMeshGroup;

        function init3DScenes() {
            // 1. Weapon 3D Viewport
            const wpContainer = document.getElementById("weapon3DCanvas");
            wpScene = new THREE.Scene();
            wpCamera = new THREE.PerspectiveCamera(45, wpContainer.clientWidth / wpContainer.clientHeight, 0.1, 1000);
            wpCamera.position.set(0, 0, 6);

            wpRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
            wpRenderer.setSize(wpContainer.clientWidth, wpContainer.clientHeight);
            wpContainer.appendChild(wpRenderer.domElement);

            const wpLight1 = new THREE.DirectionalLight(0xffffff, 1.2);
            wpLight1.position.set(5, 5, 5);
            wpScene.add(wpLight1);
            wpScene.add(new THREE.AmbientLight(0xffffff, 0.6));

            wpMeshGroup = new THREE.Group();
            wpScene.add(wpMeshGroup);

            // 2. Boss 3D Viewport
            const bossContainer = document.getElementById("boss3DCanvas");
            bossScene = new THREE.Scene();
            bossCamera = new THREE.PerspectiveCamera(45, bossContainer.clientWidth / bossContainer.clientHeight, 0.1, 1000);
            bossCamera.position.set(0, 1, 7);

            bossRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
            bossRenderer.setSize(bossContainer.clientWidth, bossContainer.clientHeight);
            bossContainer.appendChild(bossRenderer.domElement);

            const bossLight = new THREE.DirectionalLight(0xffffff, 1.2);
            bossLight.position.set(5, 8, 5);
            bossScene.add(bossLight);
            bossScene.add(new THREE.AmbientLight(0xffffff, 0.5));

            bossMeshGroup = new THREE.Group();
            bossScene.add(bossMeshGroup);

            // Start Animation Loop
            animate3D();
            updateWeapon3DModel('wood');
            updateBoss3DModel('goblin');
        }

        // Tạo Mẫu Vũ Khí 3D
        function createWeaponMesh(modelType) {
            const group = new THREE.Group();

            if (modelType === 'wood') {
                // Kiếm Gỗ
                const bladeGeo = new THREE.BoxGeometry(0.3, 3, 0.1);
                const bladeMat = new THREE.MeshStandardMaterial({ color: 0x8b5a2b, roughness: 0.8 });
                const blade = new THREE.Mesh(bladeGeo, bladeMat);
                blade.position.y = 1;
                group.add(blade);

                const hiltGeo = new THREE.CylinderGeometry(0.1, 0.1, 1);
                const hilt = new THREE.Mesh(hiltGeo, bladeMat);
                hilt.position.y = -0.8;
                group.add(hilt);
            } else if (modelType === 'iron') {
                // Trường Kiếm Sắt
                const bladeGeo = new THREE.BoxGeometry(0.35, 3.5, 0.08);
                const bladeMat = new THREE.MeshStandardMaterial({ color: 0xcbd5e1, metalness: 0.9, roughness: 0.2 });
                const blade = new THREE.Mesh(bladeGeo, bladeMat);
                blade.position.y = 1;
                group.add(blade);

                const guardGeo = new THREE.BoxGeometry(1.2, 0.15, 0.2);
                const guardMat = new THREE.MeshStandardMaterial({ color: 0x475569, metalness: 0.8 });
                const guard = new THREE.Mesh(guardGeo, guardMat);
                guard.position.y = -0.6;
                group.add(guard);
            } else if (modelType === 'axe') {
                // Rìu Chiến Thép
                const handleGeo = new THREE.CylinderGeometry(0.1, 0.1, 3.5);
                const handleMat = new THREE.MeshStandardMaterial({ color: 0x475569, metalness: 0.5 });
                const handle = new THREE.Mesh(handleGeo, handleMat);
                group.add(handle);

                const headGeo = new THREE.BoxGeometry(1.4, 1.2, 0.15);
                const headMat = new THREE.MeshStandardMaterial({ color: 0x9333ea, metalness: 0.9, roughness: 0.1 });
                const head = new THREE.Mesh(headGeo, headMat);
                head.position.set(0.5, 1, 0);
                group.add(head);
            } else if (modelType === 'holy') {
                // Thánh Kiếm Diệt Đao (Phát Sáng)
                const bladeGeo = new THREE.BoxGeometry(0.45, 4, 0.12);
                const bladeMat = new THREE.MeshStandardMaterial({ color: 0xfacc15, metalness: 0.9, roughness: 0.1, emissive: 0xeab308, emissiveIntensity: 0.5 });
                const blade = new THREE.Mesh(bladeGeo, bladeMat);
                blade.position.y = 1.2;
                group.add(blade);

                const guardGeo = new THREE.BoxGeometry(1.6, 0.2, 0.3);
                const guardMat = new THREE.MeshStandardMaterial({ color: 0xca8a04, metalness: 0.9 });
                const guard = new THREE.Mesh(guardGeo, guardMat);
                guard.position.y = -0.5;
                group.add(guard);
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

        // Tạo Mẫu Boss 3D
        function createBossMesh(type) {
            const group = new THREE.Group();

            if (type === 'goblin') {
                const bodyGeo = new THREE.SphereGeometry(1, 16, 16);
                const bodyMat = new THREE.MeshStandardMaterial({ color: 0x16a34a, roughness: 0.6 });
                const body = new THREE.Mesh(bodyGeo, bodyMat);
                group.add(body);

                const eyeGeo = new THREE.SphereGeometry(0.2, 8, 8);
                const eyeMat = new THREE.MeshBasicMaterial({ color: 0xef4444 });
                const eye1 = new THREE.Mesh(eyeGeo, eyeMat);
                eye1.position.set(-0.35, 0.3, 0.85);
                const eye2 = eye1.clone();
                eye2.position.x = 0.35;
                group.add(eye1); group.add(eye2);
            } else if (type === 'golem') {
                const bodyGeo = new THREE.BoxGeometry(2, 2.5, 1.8);
                const bodyMat = new THREE.MeshStandardMaterial({ color: 0x64748b, roughness: 0.9, metalness: 0.2 });
                const body = new THREE.Mesh(bodyGeo, bodyMat);
                group.add(body);
            } else if (type === 'dragon') {
                const bodyGeo = new THREE.ConeGeometry(1.5, 3, 16);
                const bodyMat = new THREE.MeshStandardMaterial({ color: 0xd97706, roughness: 0.3, emissive: 0xb45309, emissiveIntensity: 0.4 });
                const body = new THREE.Mesh(bodyGeo, bodyMat);
                body.rotation.x = Math.PI;
                group.add(body);
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

        function animate3D() {
            requestAnimationFrame(animate3D);

            // Xoay Vũ Khí 3D
            if (wpMeshGroup) {
                wpMeshGroup.rotation.y += 0.02;
            }

            // Xoay nhẹ Boss 3D
            if (bossMeshGroup) {
                bossMeshGroup.rotation.y += 0.01;
            }

            wpRenderer.render(wpScene, wpCamera);
            bossRenderer.render(bossScene, bossCamera);
        }

        // Chuyển Tab UI
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

                btnForge.className = "tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 transition flex items-center justify-center gap-2 text-white shadow-lg";
                btnBattle.className = "flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 hover:bg-slate-700 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg";
            } else {
                pageForge.classList.add("hidden");
                pageForge.classList.remove("grid");
                pageBattle.classList.remove("hidden");
                pageBattle.classList.add("flex");

                btnBattle.className = "tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 transition flex items-center justify-center gap-2 text-white shadow-lg";
                btnForge.className = "flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 hover:bg-slate-700 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg";
            }
        }

        function gatherResources() {
            player.wood += Math.floor(Math.random() * 4) + 3;
            player.iron += Math.floor(Math.random() * 3) + 2;
            log("Đã khai thác thêm nguyên liệu gỗ và sắt!");
            updateUI();
        }

        function renderRecipes() {
            const list = document.getElementById("recipeList");
            list.innerHTML = "";
            recipes.forEach(r => {
                const isSelected = r.id === selectedRecipeId;
                const div = document.createElement("div");
                div.className = `p-2.5 rounded-xl border cursor-pointer transition flex items-center justify-between ${isSelected ? 'border-amber-500 bg-amber-950/40' : 'border-slate-800 bg-slate-950/50 hover:bg-slate-800/40'}`;
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
                log("Không đủ nguyên liệu để rèn vũ khí này!");
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
            log(`Rèn thành công: ${newWeapon.name}!`);
            updateUI();
        }

        function equipWeapon(id) {
            player.equippedId = id;
            log("Đã trang bị vũ khí mới!");
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
            log(`Nâng cấp thành công ${item.name} lên Cấp +${item.level}!`);
            updateUI();
        }

        function sellWeapon(id) {
            const idx = player.inventory.findIndex(i => i.id === id);
            if (idx === -1) return;
            if (player.equippedId === id) player.equippedId = null;
            player.inventory.splice(idx, 1);
            player.gold += 35;
            log("Đã bán vũ khí lấy +35 Vàng!");
            updateUI();
        }

        function renderInventory() {
            const grid = document.getElementById("inventoryGrid");
            document.getElementById("inventoryCount").innerText = `${player.inventory.length}/12`;
            grid.innerHTML = "";

            if (player.inventory.length === 0) {
                grid.innerHTML = `<div class="text-xs text-slate-500 text-center py-8">Chưa có vũ khí 3D nào trong kho.</div>`;
                return;
            }

            player.inventory.forEach(item => {
                const isEquipped = item.id === player.equippedId;
                const div = document.createElement("div");
                div.className = `p-3 rounded-xl border rarity-${item.rarity} bg-slate-950/70 flex items-center justify-between gap-2 shadow-md`;

                div.innerHTML = `
                    <div>
                        <div class="font-bold text-xs sm:text-sm rarity-text-${item.rarity}">${item.name} <span class="text-amber-400">+${item.level}</span></div>
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
            let baseAtk = 12;
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
            if (isCrit) dmg = Math.floor(dmg * 1.8);

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
                btn.className = "py-3.5 px-5 bg-slate-800 border border-slate-700 hover:bg-slate-700 font-semibold rounded-xl text-slate-300 transition text-sm";
            } else {
                autoAttackInterval = setInterval(() => {
                    playerAttack();
                }, 800);
                btn.innerText = "🤖 Tự Động: ON";
                btn.className = "py-3.5 px-5 bg-amber-600/30 border border-amber-500/50 hover:bg-amber-600/40 font-bold rounded-xl text-amber-300 transition text-sm shadow-lg shadow-amber-600/20";
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
            localStorage.setItem("forgeAndConquer3DSave", JSON.stringify(player));
        }

        function loadGame() {
            const data = localStorage.getItem("forgeAndConquer3DSave");
            if (data) {
                try {
                    player = Object.assign(player, JSON.parse(data));
                } catch(e) {}
            }
            updateUI();
        }

        // Window onload
        window.onload = () => {
            init3DScenes();
            loadGame();
        };
    </script>
</body>
</html>
