<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>익사이팅 사다리 타기 & 정산기</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .custom-scrollbar::-webkit-scrollbar { width: 6px; height: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: #f1f5f9; border-radius: 10px; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover { background: #94a3b8; }
        .fade-in { animation: fadeIn 0.5s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="bg-slate-100 min-h-screen p-4 md:p-8 text-slate-800">

    <div class="max-w-6xl mx-auto">
        <!-- Header -->
        <header class="mb-8 text-center">
            <div class="inline-flex items-center justify-center p-3 bg-indigo-600 rounded-2xl shadow-lg mb-4">
                <i data-lucide="trophy" class="w-8 h-8 text-white"></i>
            </div>
            <h1 class="text-4xl font-extrabold text-slate-900 tracking-tight">익사이팅 사다리 타기</h1>
            <p class="text-slate-500 mt-2 font-medium">최대 30명 | 자유로운 결과 설정 | 스마트 정산</p>
        </header>

        <!-- Setup Screen -->
        <div id="setup-screen" class="space-y-6 fade-in">
            <!-- Settlement Tool -->
            <div class="bg-white p-6 rounded-3xl shadow-sm border border-slate-200">
                <div class="flex items-center gap-2 mb-4 text-indigo-600 font-bold">
                    <i data-lucide="calculator" class="w-5 h-5"></i>
                    <span>스마트 정산 도구</span>
                </div>
                <div class="flex flex-wrap gap-4 items-end">
                    <div class="flex-1 min-w-[200px]">
                        <label class="block text-xs font-bold text-slate-400 mb-1 ml-1 uppercase">총 금액</label>
                        <div class="relative">
                            <i data-lucide="dollar-sign" class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-slate-400"></i>
                            <input id="calc-total" type="number" class="w-full bg-slate-50 border-2 border-slate-100 rounded-xl py-3 pl-10 pr-4 focus:border-indigo-500 focus:bg-white transition-all outline-none" placeholder="예: 50000">
                        </div>
                    </div>
                    <div class="w-32">
                        <label class="block text-xs font-bold text-slate-400 mb-1 ml-1 uppercase">당첨 인원</label>
                        <input id="calc-winners" type="number" class="w-full bg-slate-50 border-2 border-slate-100 rounded-xl py-3 px-4 focus:border-indigo-500 focus:bg-white transition-all outline-none" value="1" min="1">
                    </div>
                    <button onclick="applySettlement()" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 px-6 rounded-xl transition-all shadow-md active:scale-95">
                        결과 리스트에 적용
                    </button>
                </div>
            </div>

            <!-- Main Editors -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <!-- Participants -->
                <div class="bg-white p-6 rounded-3xl shadow-sm border border-slate-200">
                    <div class="flex justify-between items-center mb-4">
                        <h2 class="text-xl font-bold flex items-center gap-2">
                            <i data-lucide="users" class="w-5 h-5 text-indigo-500"></i>
                            참가자 (<span id="participant-count">3</span>)
                        </h2>
                        <button onclick="addParticipant()" class="text-indigo-600 hover:bg-indigo-50 p-2 rounded-lg transition-colors">
                            <i data-lucide="plus" class="w-6 h-6"></i>
                        </button>
                    </div>
                    <div id="participant-list" class="grid grid-cols-1 sm:grid-cols-2 gap-2 max-h-[400px] overflow-y-auto pr-2 custom-scrollbar">
                        <!-- Rendered by JS -->
                    </div>
                </div>

                <!-- Outcomes -->
                <div class="bg-white p-6 rounded-3xl shadow-sm border border-slate-200">
                    <div class="flex justify-between items-center mb-4">
                        <h2 class="text-xl font-bold flex items-center gap-2">
                            <i data-lucide="gift" class="w-5 h-5 text-rose-500"></i>
                            결과 / 벌칙
                        </h2>
                        <button onclick="shuffleOutcomes()" class="flex items-center gap-1 text-xs font-bold text-slate-500 hover:text-indigo-600 bg-slate-100 px-3 py-1 rounded-full transition-all">
                            <i data-lucide="shuffle" class="w-3 h-3"></i> 순서 섞기
                        </button>
                    </div>
                    <div id="outcome-list" class="grid grid-cols-1 sm:grid-cols-2 gap-2 max-h-[400px] overflow-y-auto pr-2 custom-scrollbar">
                        <!-- Rendered by JS -->
                    </div>
                </div>
            </div>

            <div class="flex justify-center pt-4">
                <button onclick="startGame()" class="group flex items-center gap-4 bg-indigo-600 hover:bg-indigo-700 text-white text-2xl font-black px-16 py-5 rounded-2xl shadow-2xl shadow-indigo-200 transition-all transform hover:scale-105 active:scale-95">
                    사다리 생성하기
                    <i data-lucide="chevron-right" class="w-8 h-8 group-hover:translate-x-1 transition-transform"></i>
                </button>
            </div>
        </div>

        <!-- Game Screen -->
        <div id="game-screen" class="hidden bg-white p-8 rounded-[40px] shadow-2xl border border-slate-100 fade-in">
            <div class="flex flex-wrap justify-between items-center gap-4 mb-10">
                <button onclick="showSetup()" class="flex items-center gap-2 bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold px-5 py-2.5 rounded-xl transition-all">
                    <i data-lucide="rotate-ccw" class="w-4 h-4"></i> 다시 설정
                </button>
                <div class="text-center">
                    <span class="bg-indigo-50 text-indigo-600 px-4 py-1 rounded-full text-xs font-black uppercase tracking-widest">Live Game</span>
                </div>
                <button onclick="revealAll()" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold px-6 py-2.5 rounded-xl transition-all shadow-lg shadow-indigo-100">
                    모든 결과 확인
                </button>
            </div>

            <div class="relative overflow-x-auto pb-8 custom-scrollbar">
                <div id="ladder-container" class="mx-auto min-w-full">
                    <!-- Top Names -->
                    <div id="ladder-names" class="flex justify-between mb-6"></div>
                    
                    <!-- Canvas -->
                    <div class="relative">
                        <canvas id="ladder-canvas" class="mx-auto"></canvas>
                    </div>

                    <!-- Bottom Outcomes -->
                    <div id="ladder-results" class="flex justify-between mt-6"></div>
                </div>
            </div>
            <p class="text-center text-slate-400 text-sm mt-8">참가자의 이름을 클릭하여 사다리를 타보세요!</p>
        </div>
    </div>

    <script>
        // State
        let state = {
            participants: ['참가자 1', '참가자 2', '참가자 3'],
            outcomes: ['당첨', '꽝', '꽝'],
            results: {}, // startIdx: boolean
            ladderData: null,
            isAnimating: false
        };

        const CANVAS_HEIGHT = 450;
        const COL_WIDTH = 120;

        // Initialize Lucide Icons
        function initIcons() { lucide.createIcons(); }

        // Render Setup Screen
        function renderSetup() {
            const pList = document.getElementById('participant-list');
            const oList = document.getElementById('outcome-list');
            document.getElementById('participant-count').innerText = state.participants.length;

            pList.innerHTML = state.participants.map((name, i) => `
                <div class="flex items-center gap-2 bg-slate-50 p-2 rounded-xl group hover:bg-slate-100">
                    <span class="text-[10px] font-black text-slate-300 w-5">${i + 1}</span>
                    <input class="flex-1 bg-transparent border-none text-sm font-semibold outline-none" 
                           value="${name}" onchange="updateItem(${i}, this.value, 'participants')">
                    <button onclick="removeParticipant(${i})" class="opacity-0 group-hover:opacity-100 text-slate-300 hover:text-red-500 transition-all">
                        <i data-lucide="trash-2" class="w-4 h-4"></i>
                    </button>
                </div>
            `).join('');

            oList.innerHTML = state.outcomes.map((item, i) => `
                <div class="flex items-center gap-2 bg-slate-50 p-2 rounded-xl border border-transparent focus-within:border-indigo-200">
                    <i data-lucide="arrow-right" class="w-3 h-3 text-slate-300"></i>
                    <input class="flex-1 bg-transparent border-none text-sm font-medium outline-none" 
                           value="${item}" onchange="updateItem(${i}, this.value, 'outcomes')" placeholder="결과 입력">
                </div>
            `).join('');
            
            initIcons();
        }

        function updateItem(index, value, key) {
            state[key][index] = value;
        }

        function addParticipant() {
            if (state.participants.length >= 30) return;
            state.participants.push(`참가자 ${state.participants.length + 1}`);
            state.outcomes.push('꽝');
            renderSetup();
        }

        function removeParticipant(index) {
            if (state.participants.length <= 2) return;
            state.participants.splice(index, 1);
            state.outcomes.splice(index, 1);
            renderSetup();
        }

        function shuffleOutcomes() {
            state.outcomes.sort(() => Math.random() - 0.5);
            renderSetup();
        }

        function applySettlement() {
            const total = parseInt(document.getElementById('calc-total').value);
            const winners = parseInt(document.getElementById('calc-winners').value);
            if (isNaN(total) || total <= 0) return;

            const count = Math.min(winners, state.participants.length);
            const split = Math.floor(total / count);
            
            state.outcomes = Array(state.participants.length).fill('꽝');
            for(let i=0; i<count; i++) {
                state.outcomes[i] = `${split.toLocaleString()}원`;
            }
            renderSetup();
        }

        // Ladder Logic
        function generateLadder() {
            const count = state.participants.length;
            const steps = 18;
            const horizontalLines = [];

            for (let i = 0; i < count - 1; i++) {
                for (let j = 0; j < steps; j++) {
                    if (Math.random() > 0.65) {
                        horizontalLines.push({
                            fromCol: i, toCol: i + 1,
                            y: (j + 1) * (CANVAS_HEIGHT / (steps + 1)) + (Math.random() * 15 - 7.5)
                        });
                    }
                }
            }

            const paths = state.participants.map((_, startIdx) => {
                let currentCol = startIdx;
                let path = [{ x: currentCol, y: 0 }];
                const sortedLines = [...horizontalLines].sort((a, b) => a.y - b.y);

                sortedLines.forEach(line => {
                    if (line.fromCol === currentCol) {
                        path.push({ x: currentCol, y: line.y });
                        currentCol = line.toCol;
                        path.push({ x: currentCol, y: line.y });
                    } else if (line.toCol === currentCol) {
                        path.push({ x: currentCol, y: line.y });
                        currentCol = line.fromCol;
                        path.push({ x: currentCol, y: line.y });
                    }
                });

                path.push({ x: currentCol, y: CANVAS_HEIGHT });
                return { startIdx, endIdx: currentCol, path };
            });

            return { horizontalLines, paths };
        }

        function drawLadder() {
            const canvas = document.getElementById('ladder-canvas');
            const ctx = canvas.getContext('2d');
            const width = canvas.width;
            const colWidth = width / state.participants.length;

            ctx.clearRect(0, 0, width, CANVAS_HEIGHT);
            
            // vertical
            ctx.strokeStyle = '#cbd5e1';
            ctx.lineWidth = 4;
            ctx.lineCap = 'round';
            state.participants.forEach((_, i) => {
                const x = (i + 0.5) * colWidth;
                ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, CANVAS_HEIGHT); ctx.stroke();
            });

            // horizontal
            ctx.strokeStyle = '#94a3b8';
            ctx.lineWidth = 3;
            state.ladderData.horizontalLines.forEach(line => {
                const x1 = (line.fromCol + 0.5) * colWidth;
                const x2 = (line.toCol + 0.5) * colWidth;
                ctx.beginPath(); ctx.moveTo(x1, line.y); ctx.lineTo(x2, line.y); ctx.stroke();
            });

            // completed paths
            Object.keys(state.results).forEach(idx => {
                ctx.strokeStyle = '#6366f1';
                ctx.lineWidth = 6;
                ctx.beginPath();
                const p = state.ladderData.paths[idx].path;
                p.forEach((pt, i) => {
                    const px = (pt.x + 0.5) * colWidth;
                    if (i === 0) ctx.moveTo(px, pt.y); else ctx.lineTo(px, pt.y);
                });
                ctx.stroke();
            });
        }

        function runAnimation(idx) {
            if (state.results[idx] || state.isAnimating) return;
            state.isAnimating = true;

            const canvas = document.getElementById('ladder-canvas');
            const ctx = canvas.getContext('2d');
            const colWidth = canvas.width / state.participants.length;
            const path = state.ladderData.paths[idx].path;
            
            let currentSegment = 0;
            let currentX = (path[0].x + 0.5) * colWidth;
            let currentY = path[0].y;
            const speed = 6;

            function animate() {
                if (currentSegment >= path.length - 1) {
                    state.results[idx] = true;
                    state.isAnimating = false;
                    renderGameUI();
                    drawLadder();
                    return;
                }

                const next = path[currentSegment + 1];
                const targetX = (next.x + 0.5) * colWidth;
                const targetY = next.y;

                const dx = targetX - currentX;
                const dy = targetY - currentY;
                const dist = Math.sqrt(dx*dx + dy*dy);

                if (dist < speed) {
                    currentX = targetX; currentY = targetY; currentSegment++;
                } else {
                    currentX += (dx/dist) * speed; currentY += (dy/dist) * speed;
                }

                drawLadder();
                // trail
                ctx.strokeStyle = '#ef4444'; ctx.lineWidth = 6;
                ctx.beginPath();
                ctx.moveTo((path[currentSegment].x + 0.5) * colWidth, path[currentSegment].y);
                ctx.lineTo(currentX, currentY);
                ctx.stroke();
                // ball
                ctx.fillStyle = '#ef4444'; ctx.beginPath(); ctx.arc(currentX, currentY, 8, 0, Math.PI*2); ctx.fill();

                requestAnimationFrame(animate);
            }
            animate();
        }

        function renderGameUI() {
            const namesEl = document.getElementById('ladder-names');
            const resultsEl = document.getElementById('ladder-results');

            namesEl.innerHTML = state.participants.map((name, i) => `
                <div class="w-full px-2">
                    <button onclick="runAnimation(${i})" ${state.results[i] || state.isAnimating ? 'disabled' : ''} 
                            class="w-full py-4 px-2 rounded-2xl text-sm font-bold truncate transition-all 
                                   ${state.results[i] ? 'bg-slate-100 text-slate-400' : 'bg-white border-2 border-indigo-100 text-indigo-700 hover:border-indigo-400 hover:shadow-md'}">
                        ${name}
                    </button>
                </div>
            `).join('');

            resultsEl.innerHTML = state.outcomes.map((item, i) => {
                const startIdx = state.ladderData.paths.find(p => p.endIdx === i).startIdx;
                const isRevealed = state.results[startIdx];
                return `
                    <div class="w-full px-2">
                        <div class="w-full h-20 flex items-center justify-center p-3 rounded-2xl border-2 text-center font-bold transition-all duration-700
                                    ${isRevealed ? 'bg-rose-50 border-rose-200 text-rose-600 scale-105' : 'bg-slate-50 border-slate-100 text-transparent'}">
                            <span class="text-sm break-all">${isRevealed ? item : '?'}</span>
                        </div>
                    </div>
                `;
            }).join('');
        }

        function startGame() {
            state.results = {};
            state.ladderData = generateLadder();
            
            const canvas = document.getElementById('ladder-canvas');
            canvas.width = Math.max(1000, state.participants.length * COL_WIDTH);
            document.getElementById('ladder-container').style.width = canvas.width + 'px';
            
            document.getElementById('setup-screen').classList.add('hidden');
            document.getElementById('game-screen').classList.remove('hidden');
            
            renderGameUI();
            drawLadder();
        }

        function showSetup() {
            document.getElementById('setup-screen').classList.remove('hidden');
            document.getElementById('game-screen').classList.add('hidden');
        }

        function revealAll() {
            state.participants.forEach((_, i) => {
                setTimeout(() => {
                    if(!state.results[i]) {
                         state.results[i] = true;
                         renderGameUI();
                         drawLadder();
                    }
                }, i * 150);
            });
        }

        // Init
        renderSetup();
    </script>
</body>
</html>
