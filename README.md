# yong-lofy-music
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RetroBeat - 시대와 장르를 넘나드는 음악 여행</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;700&display=swap');
        
        body {
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #0f172a;
            color: #f1f5f9;
        }

        .era-card {
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            cursor: pointer;
            border: 1px solid rgba(255, 255, 255, 0.05);
        }

        .era-card:hover {
            transform: translateY(-10px);
            border-color: #38bdf8;
            box-shadow: 0 10px 25px -5px rgba(56, 189, 248, 0.3);
        }

        .active-tab {
            color: #38bdf8 !important;
            border-bottom: 2px solid #38bdf8;
        }

        .active-filter {
            background-color: #38bdf8 !important;
            color: #0f172a !important;
        }

        /* 커스텀 애니메이션 */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .fade-in {
            animation: fadeIn 0.5s ease forwards;
        }

        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #1e293b; }
        ::-webkit-scrollbar-thumb { background: #334155; border-radius: 4px; }
        
        .gradient-text {
            background: linear-gradient(to right, #38bdf8, #818cf8, #c084fc);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
    </style>
</head>
<body class="min-h-screen">

    <header class="sticky top-0 z-50 bg-[#0f172a]/90 backdrop-blur-md border-b border-slate-800">
        <div class="container mx-auto px-4 py-4 flex justify-between items-center">
            <h1 class="text-2xl font-bold flex items-center gap-2">
                <i class="fas fa-music text-sky-400"></i>
                <span class="gradient-text">RetroBeat</span>
            </h1>
            <div class="flex gap-8 text-sm font-bold">
                <button id="tab-era" class="active-tab pb-1 transition" onclick="switchMainTab('era')">시대별 탐색</button>
                <button id="tab-genre" class="pb-1 transition text-slate-400 hover:text-white" onclick="switchMainTab('genre')">장르별 탐색</button>
            </div>
            <button class="hidden md:block bg-slate-800 hover:bg-slate-700 px-4 py-2 rounded-full text-xs font-bold transition border border-slate-700">
                내 플레이리스트
            </button>
        </div>
    </header>

    <main class="container mx-auto px-4 py-12">
        <section class="text-center mb-12">
            <h2 id="main-title" class="text-4xl md:text-5xl font-bold mb-4">그 시절 우리가 사랑했던 <span class="text-sky-400">음악</span></h2>
            <p id="main-desc" class="text-slate-400 max-w-2xl mx-auto">전후의 애환부터 글로벌 K-POP까지, 대한민국 대중음악의 역사를 탐험해보세요.</p>
        </section>

        <!-- 필터링 버튼 영역 -->
        <div class="flex flex-wrap justify-center gap-2 mb-12" id="filter-container">
            <!-- JS에서 동적으로 생성됨 -->
        </div>

        <!-- 카드 그리드 -->
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8" id="music-grid">
            <!-- 데이터 삽입 영역 -->
        </div>
    </main>

    <script>
        // 시대별 데이터
        const eraData = [
            { id: 'e1', era: "1970s", title: "청년 문화와 포크", category: "gold", tags: ["통기타", "청바지"], color: "from-emerald-900/40", songs: [{t:"아침 이슬", a:"양희은"}, {t:"고래사냥", a:"송창식"}] },
            { id: 'e2', era: "1980s", title: "발라드의 황금기", category: "gold", tags: ["감성", "조용필"], color: "from-purple-900/40", songs: [{t:"단발머리", a:"조용필"}, {t:"광화문 연가", a:"이문세"}] },
            { id: 'e3', era: "1990s", title: "K-POP의 르네상스", category: "gold", tags: ["서태지", "아이돌"], color: "from-red-900/40", songs: [{t:"난 알아요", a:"서태지와 아이들"}, {t:"잘못된 만남", a:"김건모"}] },
            { id: 'e4', era: "2000s", title: "아이돌 전성시대", category: "modern", tags: ["동방신기", "빅뱅"], color: "from-pink-900/40", songs: [{t:"거짓말", a:"빅뱅"}, {t:"Tell Me", a:"원더걸스"}] },
            { id: 'e5', era: "2010s", title: "글로벌 K-POP", category: "modern", tags: ["BTS", "싸이"], color: "from-indigo-900/40", songs: [{t:"강남스타일", a:"싸이"}, {t:"피 땀 눈물", a:"방탄소년단"}] },
            { id: 'e6', era: "2020s", title: "뉴진스와 뉴트로", category: "modern", tags: ["이지리스닝", "아이브"], color: "from-cyan-900/40", songs: [{t:"Ditto", a:"뉴진스"}, {t:"Love Dive", a:"아이브"}] }
        ];

        // 장르별 데이터 추가
        const genreData = [
            { id: 'g1', genre: "Ballad", title: "애절한 감성 발라드", category: "vocal", tags: ["감성", "고음"], color: "from-blue-900/40", songs: [{t:"보고싶다", a:"김범수"}, {t:"야생화", a:"박효신"}, {t:"밤편지", a:"아이유"}] },
            { id: 'g2', genre: "Dance", title: "화려한 퍼포먼스 댄스", category: "performance", tags: ["아이돌", "군무"], color: "from-orange-900/40", songs: [{t:"으르렁", a:"EXO"}, {t:"Next Level", a:"에스파"}, {t:"I Am The Best", a:"2NE1"}] },
            { id: 'g3', genre: "Hip-hop", title: "비트 위의 언어, 힙합", category: "rhythm", tags: ["쇼미", "라임"], color: "from-slate-800/40", songs: [{t:"연결고리", a:"일리네어"}, {t:"VVS", a:"미란이"}, {t:"시차", a:"우원재"}] },
            { id: 'g4', genre: "Rock/Indie", title: "밴드 사운드와 인디", category: "vocal", tags: ["밴드", "기타"], color: "from-red-950/40", songs: [{t:"비와 당신", a:"노브레인"}, {t:"사건의 지평선", a:"윤하"}, {t:"뜨거운 여름밤은 가고", a:"잔나비"}] },
            { id: 'g5', genre: "Trot", title: "전통의 재해석, 트로트", category: "classic", tags: ["미스터트롯", "흥"], color: "from-yellow-900/40", songs: [{t:"안동역에서", a:"진성"}, {t:"이제 나만 믿어요", a:"임영웅"}, {t:"어머나", a:"장윤정"}] },
            { id: 'g6', genre: "City Pop", title: "세련된 도시의 밤", category: "rhythm", tags: ["레트로", "신스"], color: "from-violet-900/40", songs: [{t:"서울여자", a:"유키카"}, {t:"네온", a:"유빈"}, {t:"환상", a:"박지윤"}] }
        ];

        let currentView = 'era'; // 'era' or 'genre'

        function switchMainTab(tab) {
            currentView = tab;
            document.getElementById('tab-era').classList.toggle('active-tab', tab === 'era');
            document.getElementById('tab-genre').classList.toggle('active-tab', tab === 'genre');
            
            const title = document.getElementById('main-title');
            const desc = document.getElementById('main-desc');

            if (tab === 'era') {
                title.innerHTML = '그 시절 우리가 사랑했던 <span class="text-sky-400">음악</span>';
                desc.innerText = '전후의 애환부터 글로벌 K-POP까지, 대한민국 대중음악의 역사를 탐험해보세요.';
                renderFilters(['전체', '근대 가요', '황금기', '현대 팝']);
                renderCards(eraData);
            } else {
                title.innerHTML = '당신의 취향을 저격할 <span class="text-purple-400">장르</span>';
                desc.innerText = '발라드부터 힙합까지, 깊이 있는 장르별 명곡들을 만나보세요.';
                renderFilters(['전체', '보컬/감성', '퍼포먼스', '리듬/비트']);
                renderCards(genreData);
            }
        }

        function renderFilters(filters) {
            const container = document.getElementById('filter-container');
            container.innerHTML = filters.map((f, i) => `
                <button class="filter-btn ${i===0?'active-filter':''} px-5 py-2 rounded-xl bg-slate-800 text-sm hover:bg-slate-700 transition border border-slate-700" 
                        onclick="applyFilter('${f}', this)">
                    ${f}
                </button>
            `).join('');
        }

        function renderCards(data) {
            const grid = document.getElementById('music-grid');
            grid.innerHTML = data.map((item, idx) => `
                <div class="era-card fade-in bg-slate-800/40 rounded-3xl overflow-hidden p-6 bg-gradient-to-br ${item.color} to-slate-900" 
                     style="animation-delay: ${idx * 0.1}s">
                    <div class="flex justify-between items-start mb-6">
                        <span class="text-sky-400 font-bold text-lg">${item.era || item.genre}</span>
                        <div class="flex gap-2">
                            ${item.tags.map(tag => `<span class="text-[10px] bg-white/5 border border-white/10 px-2 py-1 rounded-md text-slate-300">#${tag}</span>`).join('')}
                        </div>
                    </div>
                    <h3 class="text-2xl font-bold mb-3">${item.title}</h3>
                    <div class="space-y-3 mt-6">
                        ${item.songs.map((song, i) => `
                            <div class="flex items-center gap-3 bg-black/30 p-3 rounded-2xl text-sm group hover:bg-sky-500/10 cursor-pointer transition">
                                <span class="text-sky-500 font-bold opacity-50">0${i+1}</span>
                                <div class="flex flex-col">
                                    <span class="font-bold text-slate-200">${song.t}</span>
                                    <span class="text-xs text-slate-500">${song.a}</span>
                                </div>
                                <i class="fas fa-play ml-auto text-[10px] text-slate-600 group-hover:text-sky-400"></i>
                            </div>
                        `).join('')}
                    </div>
                </div>
            `).join('');
        }

        function applyFilter(filter, btn) {
            document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active-filter'));
            btn.classList.add('active-filter');
            
            // 필터 로직 (데모를 위해 간단히 구현)
            const data = currentView === 'era' ? eraData : genreData;
            if (filter === '전체') {
                renderCards(data);
            } else {
                // 특정 카테고리 매핑은 실제 상용 앱에선 데이터 속성으로 관리함
                const filtered = data.slice(0, 3); // 샘플 필터링
                renderCards(filtered);
            }
        }

        window.onload = () => {
            switchMainTab('era');
        };
    </script>

    <!-- 모달 등 상세 기능은 이전과 동일하게 유지하거나 확장 가능 -->
</body>
</html>
