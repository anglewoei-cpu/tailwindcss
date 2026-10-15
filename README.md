<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>中日口譯與 Small Talk / PDF 講義特訓 Web App</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Custom Styles & Ruby Customization -->
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Noto+Sans+JP:wght@400;500;700&display=swap');
        
        body {
            font-family: 'Inter', 'Noto Sans JP', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
            background-color: #f8fafc;
            color: #0f172a;
        }

        /* Ruby style refinements */
        ruby {
            ruby-position: over;
            margin-right: 2px;
        }
        rt {
            font-size: 0.68em;
            color: #0284c7;
            font-weight: 500;
            user-select: none;
            line-height: 1.2;
        }
        .hide-furigana rt {
            display: none !important;
        }

        /* 3D Flip Card Styles */
        .perspective-1000 {
            perspective: 1000px;
        }
        .transform-style-3d {
            transform-style: preserve-3d;
        }
        .backface-hidden {
            backface-visibility: hidden;
            -webkit-backface-visibility: hidden;
        }
        .rotate-y-180 {
            transform: rotateY(180deg);
        }

        /* Sentence Highlight effect for speech sync */
        .sentence-speaking {
            background-color: #f0f9ff !important;
            border-left: 4px solid #0284c7 !important;
            box-shadow: 0 4px 12px rgba(2, 132, 199, 0.12);
            transform: translateY(-1px);
        }

        /* Print styles optimization */
        @media print {
            .no-print {
                display: none !important;
            }
            .print-only {
                display: block !important;
            }
            body {
                background: white;
                color: black;
            }
        }
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <!-- Top Navigation Header -->
    <header class="bg-slate-900 text-white sticky top-0 z-40 shadow-md no-print">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-sky-500 to-indigo-600 flex items-center justify-center text-white font-bold text-xl shadow-inner">
                    譯
                </div>
                <div>
                    <h1 class="font-bold text-lg leading-tight">中日口譯 & Small Talk 特訓大師</h1>
                    <p class="text-xs text-slate-400">講義特訓 × 視譯跟讀 × 破冰對話</p>
                </div>
            </div>

            <!-- Quick Action Toolbar -->
            <div class="flex items-center gap-2 sm:gap-4">
                <!-- Import Custom PDF/Doc Button -->
                <button onclick="toggleImportModal()" class="px-3 py-1.5 rounded-lg bg-sky-600 hover:bg-sky-500 text-white text-xs sm:text-sm font-medium flex items-center gap-1.5 transition shadow-sm">
                    <i data-lucide="file-up" class="w-4 h-4"></i>
                    <span class="hidden sm:inline">匯入講義</span>
                </button>

                <!-- Furigana Toggle -->
                <button id="toggleFuriganaBtn" onclick="toggleFurigana()" class="px-3 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 border border-slate-700 text-xs sm:text-sm font-medium flex items-center gap-1.5 transition">
                    <i data-lucide="languages" class="w-4 h-4 text-sky-400"></i>
                    <span id="furiganaBtnText">隱藏假名</span>
                </button>

                <!-- Timer Button -->
                <button onclick="toggleTimerModal()" class="px-3 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 border border-slate-700 text-xs sm:text-sm font-medium flex items-center gap-1.5 transition">
                    <i data-lucide="timer" class="w-4 h-4 text-emerald-400"></i>
                    <span id="headerTimerDisplay" class="font-mono">02:00</span>
                </button>

                <!-- Print Button -->
                <button onclick="window.print()" class="px-3 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 border border-slate-700 text-xs sm:text-sm font-medium flex items-center gap-1.5 transition hidden md:flex">
                    <i data-lucide="printer" class="w-4 h-4 text-slate-300"></i>
                    <span>列印</span>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="flex-grow max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6 flex flex-col md:flex-row gap-6">

        <!-- Sidebar: Navigation & Unit Selection -->
        <aside class="w-full md:w-80 flex-shrink-0 space-y-4 no-print">
            
            <!-- System Main Module Switcher -->
            <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-200/80 space-y-2">
                <span class="text-xs font-bold text-slate-400 uppercase tracking-wider block px-1">訓練模式切換</span>
                <div class="grid grid-cols-2 gap-1.5 p-1 bg-slate-100 rounded-xl">
                    <button id="tab-pdf-train" onclick="switchMainTab('pdf')" class="py-2 px-3 text-xs font-bold rounded-lg transition text-sky-700 bg-white shadow-sm flex items-center justify-center gap-1.5">
                        <i data-lucide="book-open-check" class="w-4 h-4"></i> 講義特訓
                    </button>
                    <button id="tab-smalltalk" onclick="switchMainTab('smalltalk')" class="py-2 px-3 text-xs font-medium text-slate-600 hover:text-slate-900 rounded-lg transition flex items-center justify-center gap-1.5">
                        <i data-lucide="messages-square" class="w-4 h-4"></i> Small Talk
                    </button>
                </div>
            </div>

            <!-- PDF Unit Selector Panel -->
            <div id="pdfUnitPanel" class="bg-white rounded-2xl shadow-sm border border-slate-200/80 overflow-hidden flex flex-col max-h-[calc(100vh-280px)] md:max-h-[580px]">
                <div class="p-3.5 bg-slate-50 border-b border-slate-200/80 flex items-center justify-between">
                    <div>
                        <h3 class="text-xs font-bold text-slate-800 uppercase tracking-wider">《中日口譯叢書 2》單元</h3>
                        <p class="text-[11px] text-slate-500">專屬特訓講義單元選擇</p>
                    </div>
                    <span id="pdfUnitCount" class="text-xs font-bold px-2 py-0.5 bg-sky-100 text-sky-700 rounded-full">5</span>
                </div>
                <div id="pdfUnitList" class="overflow-y-auto divide-y divide-slate-100 flex-grow">
                    <!-- PDF Units injected via JS -->
                </div>
            </div>

            <!-- Small Talk Topic List Panel -->
            <div id="smallTalkPanel" class="hidden bg-white rounded-2xl shadow-sm border border-slate-200/80 overflow-hidden flex flex-col max-h-[calc(100vh-280px)] md:max-h-[580px]">
                <div class="p-3 bg-slate-50 border-b border-slate-200/80 space-y-2">
                    <div class="relative">
                        <i data-lucide="search" class="w-4 h-4 absolute left-3 top-1/2 -translate-y-1/2 text-slate-400"></i>
                        <input type="text" id="searchInput" oninput="filterTopics()" placeholder="搜尋 Small Talk 話題..." class="w-full pl-9 pr-3 py-1.5 text-xs bg-white border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-sky-500">
                    </div>
                    <div id="categoryContainer" class="flex flex-wrap gap-1">
                        <!-- Dynamic Categories -->
                    </div>
                </div>
                <div id="topicList" class="overflow-y-auto divide-y divide-slate-100 flex-grow">
                    <!-- Topics injected via JS -->
                </div>
            </div>
        </aside>

        <!-- Main Content Area -->
        <section class="flex-grow space-y-4 min-w-0">

            <!-- Active Header Card -->
            <div id="activeHeaderCard" class="bg-gradient-to-r from-slate-900 via-indigo-950 to-slate-900 text-white p-5 rounded-2xl shadow-sm space-y-2">
                <div class="flex items-center justify-between gap-2">
                    <span id="activeCategoryBadge" class="px-2.5 py-0.5 rounded-full text-xs font-semibold bg-sky-500/20 text-sky-300 border border-sky-500/30">
                        講義單元 1
                    </span>
                    <span id="activeDocBadge" class="text-xs font-mono text-slate-400">《中日口譯必備工具叢書2》</span>
                </div>
                <h2 id="activeTitle" class="text-xl sm:text-2xl font-bold tracking-tight">基本練習 Quick Response (數字/名詞)</h2>
                <p id="activeDesc" class="text-xs sm:text-sm text-slate-300">快速反應訓練：包含金額、日期、地名與專有名詞的中日無縫切換。</p>
            </div>

            <!-- PDF Training Stage Sub-Tabs -->
            <div id="pdfStageBar" class="bg-white p-2 rounded-2xl shadow-sm border border-slate-200/80 flex flex-wrap items-center justify-between gap-2 no-print">
                <div class="flex flex-wrap items-center gap-1">
                    <button onclick="setPdfStage(1)" id="pdfStageBtn-1" class="pdf-stage-tab px-3 py-1.5 rounded-xl text-xs font-bold transition bg-sky-600 text-white shadow-sm flex items-center gap-1">
                        <i data-lucide="zap" class="w-3.5 h-3.5"></i> 關卡1: 閃卡 Quick Response
                    </button>
                    <button onclick="setPdfStage(2)" id="pdfStageBtn-2" class="pdf-stage-tab px-3 py-1.5 rounded-xl text-xs font-medium transition text-slate-600 hover:text-slate-900 flex items-center gap-1">
                        <i data-lucide="eye" class="w-3.5 h-3.5"></i> 關卡2: 視譯/短逐步
                    </button>
                    <button onclick="setPdfStage(3)" id="pdfStageBtn-3" class="pdf-stage-tab px-3 py-1.5 rounded-xl text-xs font-medium transition text-slate-600 hover:text-slate-900 flex items-center gap-1">
                        <i data-lucide="headphones" class="w-3.5 h-3.5"></i> 關卡3: 跟讀/Shadowing
                    </button>
                    <button onclick="setPdfStage(4)" id="pdfStageBtn-4" class="pdf-stage-tab px-3 py-1.5 rounded-xl text-xs font-medium transition text-slate-600 hover:text-slate-900 flex items-center gap-1">
                        <i data-lucide="help-circle" class="w-3.5 h-3.5"></i> 關卡4: 角色扮演測驗
                    </button>
                </div>
            </div>

            <!-- Small Talk Mode Sub-Controls -->
            <div id="smallTalkModeBar" class="hidden bg-white p-2.5 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col sm:flex-row items-center justify-between gap-3 no-print">
                <div class="flex items-center p-1 bg-slate-100 rounded-xl w-full sm:w-auto">
                    <button id="mode-standard" onclick="setSmallTalkMode('standard')" class="st-mode-tab flex-1 sm:flex-none px-3.5 py-1.5 text-xs font-semibold rounded-lg transition text-sky-700 bg-white shadow-sm">
                        雙語對照
                    </button>
                    <button id="mode-flashcard" onclick="setSmallTalkMode('flashcard')" class="st-mode-tab flex-1 sm:flex-none px-3.5 py-1.5 text-xs font-medium text-slate-600 hover:text-slate-900 rounded-lg transition">
                        3D 翻轉卡
                    </button>
                    <button id="mode-mask" onclick="setSmallTalkMode('mask')" class="st-mode-tab flex-1 sm:flex-none px-3.5 py-1.5 text-xs font-medium text-slate-600 hover:text-slate-900 rounded-lg transition">
                        遮蔽測驗
                    </button>
                </div>
            </div>

            <!-- Global Audio Speech Control Bar -->
            <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-200/80 flex flex-wrap items-center justify-between gap-2 no-print">
                <div class="flex items-center gap-2">
                    <button id="playAllBtn" onclick="togglePlayAll()" class="px-3.5 py-1.5 bg-sky-600 hover:bg-sky-700 text-white rounded-xl text-xs font-medium flex items-center gap-1.5 transition shadow-sm">
                        <i data-lucide="play" class="w-4 h-4 fill-current"></i>
                        <span id="playAllBtnText">朗讀全文</span>
                    </button>
                    <button onclick="stopSpeech()" class="p-1.5 bg-slate-100 hover:bg-red-50 hover:text-red-600 rounded-xl text-slate-600 transition" title="停止播放">
                        <i data-lucide="square" class="w-4 h-4"></i>
                    </button>
                </div>

                <div class="flex items-center gap-3">
                    <div class="flex items-center gap-1.5 text-xs text-slate-600 font-medium">
                        <span>語速:</span>
                        <select id="rateSelect" onchange="changeSpeechRate(this.value)" class="bg-slate-50 border border-slate-200 rounded-lg px-2 py-1 text-xs focus:ring-2 focus:ring-sky-500">
                            <option value="0.75">0.75x (慢速)</option>
                            <option value="1.0" selected>1.0x (標準)</option>
                            <option value="1.25">1.25x (快速)</option>
                        </select>
                    </div>

                    <label class="flex items-center gap-1.5 cursor-pointer">
                        <input type="checkbox" id="autoNextToggle" checked class="w-3.5 h-3.5 rounded text-sky-600 focus:ring-sky-500 border-slate-300">
                        <span class="text-xs text-slate-600 font-medium select-none">自動連續</span>
                    </label>
                </div>
            </div>

            <!-- Dynamic Content Display Panel -->
            <div id="contentContainer" class="space-y-4">
                <!-- Content generated dynamically -->
            </div>

        </section>
    </main>

    <!-- Modal: Import Custom PDF / Doc Text -->
    <div id="importModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-3xl shadow-xl max-w-lg w-full p-6 space-y-4 border border-slate-100">
            <div class="flex items-center justify-between border-b border-slate-100 pb-3">
                <h3 class="font-bold text-slate-800 flex items-center gap-2">
                    <i data-lucide="file-up" class="w-5 h-5 text-sky-600"></i> 匯入自訂講義特訓內容
                </h3>
                <button onclick="toggleImportModal()" class="text-slate-400 hover:text-slate-600">
                    <i data-lucide="x" class="w-5 h-5"></i>
                </button>
            </div>

            <p class="text-xs text-slate-500">請貼上您講義中的中日對照句子（格式：日文句子 | 中文翻譯），系統將自動建立專屬單元！</p>

            <div class="space-y-2">
                <input type="text" id="customUnitTitle" placeholder="自訂單元名稱 (例如: 觀光簡報講義)" class="w-full px-3 py-2 text-xs border border-slate-200 rounded-xl focus:ring-2 focus:ring-sky-500">
                <textarea id="customDocText" rows="6" placeholder="請貼上內容範例：&#10;最近はすっかり暖かくなりましたね。 | 最近完全變暖和了呢。&#10;赤崁楼は台南の有名な史跡です。 | 赤崁樓是台南著名的史蹟。" class="w-full p-3 text-xs border border-slate-200 rounded-xl focus:ring-2 focus:ring-sky-500 font-mono"></textarea>
            </div>

            <div class="flex justify-end gap-2 pt-2">
                <button onclick="toggleImportModal()" class="px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl font-medium text-xs">取消</button>
                <button onclick="processCustomImport()" class="px-4 py-2 bg-sky-600 hover:bg-sky-700 text-white rounded-xl font-medium text-xs transition">解析並開始特訓</button>
            </div>
        </div>
    </div>

    <!-- Modal: Timer & Practice Assistant -->
    <div id="timerModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-3xl shadow-xl max-w-sm w-full p-6 text-center space-y-5 border border-slate-100">
            <div class="flex items-center justify-between border-b border-slate-100 pb-3">
                <h3 class="font-bold text-slate-800 flex items-center gap-2">
                    <i data-lucide="timer" class="w-5 h-5 text-emerald-500"></i> 口譯演練計時器
                </h3>
                <button onclick="toggleTimerModal()" class="text-slate-400 hover:text-slate-600">
                    <i data-lucide="x" class="w-5 h-5"></i>
                </button>
            </div>

            <div class="py-4 bg-slate-50 rounded-2xl border border-slate-100">
                <div id="modalTimerDisplay" class="text-5xl font-mono font-bold text-slate-800 tracking-wider">02:00</div>
                <p class="text-xs text-slate-500 mt-2">目標：2 分鐘內完成視譯或速譯復述</p>
            </div>

            <div class="flex justify-center gap-3">
                <button id="startTimerBtn" onclick="startTimer()" class="flex-1 py-2.5 bg-emerald-600 hover:bg-emerald-700 text-white rounded-xl font-medium text-sm transition shadow-sm">
                    開始計時
                </button>
                <button onclick="resetTimer()" class="px-4 py-2.5 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl font-medium text-sm transition">
                    重置
                </button>
            </div>
        </div>
    </div>

    <!-- Application Javascript Logic -->
    <script>
        /* ==========================================================================
           1. PDF Special Training Database ("中日口譯必備工具叢書 2")
           ========================================================================== */
        const pdfUnitsDatabase = [
            {
                id: 'pdf-u1',
                unitTitle: "單元 1：基本練習 Quick Response (數字與名詞)",
                description: "針對口譯中最容易卡關的金額、大數字、地名與關鍵字 Quick Response 反應特訓。",
                wordList: [
                    { ja: "4兆4000億円", ruby: "<ruby>4兆<rt>よんちょう</rt></ruby>4000<ruby>億円<rt>おくえん</rt></ruby>", zh: "四兆四千億日圓" },
                    { ja: "2020年東京オリンピック", ruby: "2020<ruby>年<rt>ねん</rt></ruby><ruby>東京<rt>とうきょう</rt></ruby>オリンピック", zh: "2020年東京奧運" },
                    { ja: "赤崁楼", ruby: "<ruby>赤崁楼<rt>せきかんろう</rt></ruby>", zh: "赤崁樓" },
                    { ja: "媽祖", ruby: "<ruby>媽祖<rt>まそ</rt></ruby>", zh: "媽祖" },
                    { ja: "金鑽パイナップル", ruby: "<ruby>金鑽<rt>きんさん</rt></ruby>パイナップル", zh: "金鑽鳳梨" },
                    { ja: "前年比15%増", ruby: "<ruby>前年比<rt>ぜんねんひ</rt></ruby>15%<ruby>増<rt>ぞう</rt></ruby>", zh: "比前一年增加15%" }
                ],
                sentences: [
                    {
                        speaker: "A",
                        ja: "<ruby>昨年<rt>さくねん</rt></ruby>の<ruby>貿易<rt>ぼうえき</rt></ruby><ruby>總額<rt>そうがく</rt></ruby>は<ruby>約<rt>やく</rt></ruby>4<ruby>兆<rt>ちょう</rt></ruby>4000<ruby>億円<rt>おくえん</rt></ruby>に<ruby>達<rt>たっ</rt></ruby>しました。",
                        plainJa: "昨年の貿易総額は約4兆4000億円に達しました。",
                        zh: "去年的貿易總額達到了約四兆四千億日圓。"
                    },
                    {
                        speaker: "B",
                        ja: "これは<ruby>前年比<rt>ぜんねんひ</rt></ruby>15%<ruby>増<rt>ぞう</rt></ruby>の<ruby>大幅<rt>おおはば</rt></ruby>な<ruby>成長<rt>せいちょう</rt></ruby>です。",
                        plainJa: "これは前年比15%増の大幅な成長です。",
                        zh: "這是比前一年大幅增長15%的成績。"
                    },
                    {
                        speaker: "A",
                        ja: "<ruby>台南<rt>たいなん</rt></ruby>の<ruby>赤崁楼<rt>せきかんろう</rt></ruby>と<ruby>金鑽<rt>きんさん</rt></ruby>パイナップルは<ruby>日本人<rt>にほんじん</rt></ruby><ruby>觀光客<rt>かんこうきゃく</rt></ruby>に<ruby>大變<rt>たいへん</rt></ruby><ruby>人氣<rt>にんき</rt></ruby>があります。",
                        plainJa: "台南の赤崁楼と金鑽パイナップルは日本人観光客に大変人気があります。",
                        zh: "台南的赤崁樓與金鑽鳳梨極受日本觀光客歡迎。"
                    }
                ]
            },
            {
                id: 'pdf-u2',
                unitTitle: "單元 2：口譯課程說明與成績評分標準",
                description: "口譯課程開幕導覽、評分標準及訓練原則演練。",
                wordList: [
                    { ja: "逐次通訳", ruby: "<ruby>逐次通訳<rt>ちくじつうやく</rt></ruby>", zh: "逐步口譯" },
                    { ja: "同時通訳", ruby: "<ruby>同時通訳<rt>どうじつうやく</rt></ruby>", zh: "同步口譯" },
                    { ja: "評価基準", ruby: "<ruby>評価基準<rt>ひょうかきじゅん</rt></ruby>", zh: "評分標準" },
                    { ja: "正確性", ruby: "<ruby>正確性<rt>せいかくせい</rt></ruby>", zh: "正確性" }
                ],
                sentences: [
                    {
                        speaker: "A",
                        ja: "本<ruby>課程<rt>コース</rt></ruby>では<ruby>逐次通訳<rt>ちくじつうやく</rt></ruby>の<ruby>基礎<rt>きそ</rt></ruby>技術を<ruby>習得<rt>しゅうとく</rt></ruby>することを<ruby>目標<rt>もくひょう</rt></ruby>とします。",
                        plainJa: "本コースでは逐次通訳の基礎技術を習得することを目標とします。",
                        zh: "本課程的目標是掌握逐步口譯的基礎技巧。"
                    },
                    {
                        speaker: "B",
                        ja: "<ruby>評價<rt>ひょうか</rt></ruby><ruby>基準<rt>きじゅん</rt></ruby>は<ruby>譯文<rt>やくぶん</rt></ruby>の<ruby>正確性<rt>せいかくせい</rt></ruby>と<ruby>流暢<rt>りゅうちょう</rt></ruby>さ、そしてデリバリーの<ruby>明瞭<rt>めいりょう</rt></ruby>さです。",
                        plainJa: "評価基準は訳文の正確性と流暢さ、そしてデリバリーの明瞭さです。",
                        zh: "評分標準包括譯文的正確性、流暢度以及表達的清晰度。"
                    }
                ]
            },
            {
                id: 'pdf-u3',
                unitTitle: "單元 3：小導遊接機與接送實務",
                description: "機場接機、迎賓致詞與移動途中的 Small Talk 對話實務。",
                wordList: [
                    { ja: "到着ロビー", ruby: "<ruby>到着<rt>とうちゃく</rt></ruby>ロビー", zh: "接機大廳/到達層" },
                    { ja: "長旅", ruby: "<ruby>長旅<rt>ながたび</rt></ruby>", zh: "長途飛行/舟車勞頓" },
                    { ja: "お出迎え", ruby: "お<ruby>出迎<rt>でむか</rt></ruby>え", zh: "前來迎接" }
                ],
                sentences: [
                    {
                        speaker: "A",
                        ja: "ようこそ<ruby>台灣<rt>たいわん</rt></ruby>へ！<ruby>長旅<rt>ながたび</rt></ruby>お<ruby>疲<rt>つか</rt></ruby>れ<ruby>樣<rt>さま</rt></ruby>でございました。",
                        plainJa: "ようこそ台湾へ！長旅お疲れ様でございました。",
                        zh: "歡迎來到台灣！長途飛行辛苦了。"
                    },
                    {
                        speaker: "B",
                        ja: "<ruby>溫暖<rt>おんだん</rt></ruby>な<ruby>氣候<rt>きこう</rt></ruby>でとても<ruby>過<rt>す</rt></ruby>ごしやすそうですね。<ruby>迎<rt>むか</rt></ruby>えに<ruby>來<rt>き</rt></ruby>ていただき感謝します。",
                        plainJa: "温暖な気候でとても過ごしやすそうですね。迎えに来ていただき感謝します。",
                        zh: "氣候溫暖看起來非常舒適呢，感謝您特地來接我。"
                    }
                ]
            },
            {
                id: 'pdf-u6',
                unitTitle: "單元 6：觀光競賽簡報與企劃案",
                description: "觀光景點推廣、特色亮點企劃與發表現場口譯。",
                wordList: [
                    { ja: "観光コンテスト", ruby: "<ruby>観光<rt>かんこう</rt></ruby>コンテスト", zh: "觀光競賽" },
                    { ja: "魅力", ruby: "<ruby>魅力<rt>みりょく</rt></ruby>", zh: "魅力與特色" }
                ],
                sentences: [
                    {
                        speaker: "A",
                        ja: "<ruby>私<rt>わたし</rt></ruby>たちの<ruby>企劃<rt>きかく</rt></ruby>は<ruby>傳統<rt>でんとう</rt></ruby><ruby>文化<rt>ぶんか</rt></ruby>とモダンなグルメの<ruby>融合<rt>ゆうごう</rt></ruby>をテーマにしています。",
                        plainJa: "私たちの企画は伝統文化とモダンなグルメの融合をテーマにしています。",
                        zh: "我們的企劃案以融合傳統文化與現代美食為主題。"
                    }
                ]
            },
            {
                id: 'pdf-u7',
                unitTitle: "單元 7：手作烘焙與DIY體驗活動",
                description: "鳳梨酥、糕點手作體驗教學的現場口譯指令。",
                wordList: [
                    { ja: "パイナップルケーキ", ruby: "パイナップルケーキ", zh: "鳳梨酥" },
                    { ja: "生地", ruby: "<ruby>生地<rt>きじ</rt></ruby>", zh: "麵糰/餅皮" }
                ],
                sentences: [
                    {
                        speaker: "A",
                        ja: "まず<ruby>生地<rt>きじ</rt></ruby>を<ruby>均一<rt>きんいつ</rt></ruby>に伸ばして、<ruby>餡<rt>あん</rt></ruby>を<ruby>優<rt>やさ</rt></ruby>しく包み込んでください。",
                        plainJa: "まず生地を均一に伸ばして、餡を優しく包み込んでください。",
                        zh: "首先請將麵糰均勻桿開，接著溫柔地包入鳳梨陷。"
                    }
                ]
            }
        ];

        /* ==========================================================================
           2. Expanded Small Talk Topics Database
           ========================================================================== */
        const smallTalkDatabase = [
            {
                id: 1,
                category: "天氣話題",
                title: "季節與天氣問候",
                description: "商務拜訪或開場時最安全的破冰話題。",
                sentences: [
                    { speaker: "A", ja: "<ruby>最近<rt>さいきん</rt></ruby>はすっかり<ruby>暖<rt>あたた</rt></ruby>かくなりましたね。", plainJa: "最近はすっかり暖かくなりましたね。", zh: "最近完全變暖和了呢。" },
                    { speaker: "B", ja: "そうですね。<ruby>今週<rt>こんしゅう</rt></ruby>は<ruby>氣溫<rt>きおん</rt></ruby>が<ruby>上<rt>あ</rt></ruby>がって、<ruby>過<rt>す</rt></ruby>ごしやすくなりました。", plainJa: "そうですね。今週は気温が上がって、過ごしやすくなりました。", zh: "是的，這週氣溫上升了，過得很舒服。" }
                ]
            },
            {
                id: 2,
                category: "近況與工作",
                title: "繁忙期的出差與近況",
                description: "詢問對方近期工作狀況與出差行程。",
                sentences: [
                    { speaker: "A", ja: "お<ruby>久<rt>ひさ</rt></ruby>しぶりです。<ruby>最近<rt>さいきん</rt></ruby>はお<ruby>忙<rt>いそが</rt></ruby>しいですか。", plainJa: "お久しぶりです。最近はお忙しいですか。", zh: "好久不見！最近工作忙碌嗎？" },
                    { speaker: "B", ja: "ええ、<ruby>先週<rt>せんしゅう</rt></ruby>まで<ruby>出張<rt>しゅっちょう</rt></ruby>で<ruby>大阪<rt>おおさか</rt></ruby>にいました。", plainJa: "ええ、先週まで出張で大阪にいました。", zh: "是的，到上週為止我都一直在大阪出差。" }
                ]
            },
            {
                id: 3,
                category: "接待應酬",
                title: "機場接機與長途關懷",
                description: "代表公司迎接來訪外賓時的熱情對話。",
                sentences: [
                    { speaker: "A", ja: "<ruby>飛行機<rt>ひこうき</rt></ruby>の<ruby>旅<rt>たび</rt></ruby>はいかがでしたか。<ruby>長旅<rt>ながたび</rt></ruby>お<ruby>疲<rt>つか</rt></ruby>れではありませんか。", plainJa: "飛行機の旅はいかがでしたか。長旅お疲れではありませんか。", zh: "搭飛機過程還順利嗎？長途飛行辛苦了吧？" },
                    { speaker: "B", ja: "おかげさまで、とても<ruby>快適<rt>かいてき</rt></ruby>なフライトでした。", plainJa: "おかげさまで、とても快適なフライトでした。", zh: "託您的福，這是一趟非常舒適的飛行。" }
                ]
            },
            {
                id: 4,
                category: "美食文化",
                title: "台灣小吃與夜市推薦",
                description: "向日本客戶介紹特色美食與小吃文化。",
                sentences: [
                    { speaker: "A", ja: "<ruby>台灣<rt>たいわん</rt></ruby>で何か<ruby>食<rt>た</rt></ruby>べてみたい<ruby>料理<rt>りょうり</rt></ruby>はありますか。", plainJa: "台湾で何か食べてみたい料理はありますか。", zh: "在台灣有什麼想嚐嚐看的料理嗎？" },
                    { speaker: "B", ja: "本場の小籠包と小籠湯包を食べてみたいです。", plainJa: "本場の小籠包と小籠湯包を食べてみたいです。", zh: "我很想品嚐看看道地的道地小籠包與湯包。" }
                ]
            },
            {
                id: 5,
                category: "交通指引",
                title: "搭乘捷運與高鐵",
                description: "說明城市交通工具與行程安排。",
                sentences: [
                    { speaker: "A", ja: "<ruby>台北<rt>たいほく</rt></ruby>から<ruby>台南<rt>たいなん</rt></ruby>までは<ruby>新幹線<rt>しんかんせん</rt></ruby>で1<ruby>時間<rt>じかん</rt></ruby><ruby>半<rt>はん</rt></ruby>ほどで<ruby>到著<rt>とうちゃく</rt></ruby>します。", plainJa: "台北から台南しまでは新幹線で1時間半ほどで到着します。", zh: "從台北到台南搭高鐵大約一個半小時就會到達。" },
                    { speaker: "B", ja: "とても便利ですね。日本の新幹線と似ていますね。", plainJa: "とても便利ですね。日本の新幹線と似ていますね。", zh: "非常方便呢！跟日本的新幹線很像呢。" }
                ]
            },
            {
                id: 6,
                category: "觀光景點",
                title: "歷史史蹟與名勝介紹",
                description: "介紹台南或台北歷史景點特色。",
                sentences: [
                    { speaker: "A", ja: "<ruby>赤崁楼<rt>せきかんろう</rt></ruby>は300<ruby>年<rt>ねん</rt></ruby><ruby>以上<rt>いじょう</rt></ruby>の<ruby>歷史<rt>れきし</rt></ruby>を持つ有名な史跡です。", plainJa: "赤崁楼は300年以上の歴史を持つ有名な史跡です。", zh: "赤崁樓是擁有超過300年以上歷史的知名史蹟。" },
                    { speaker: "B", ja: "<ruby>建策<rt>けんちく</rt></ruby>の<ruby>樣式<rt>ようしき</rt></ruby>がとても趣深いですね。", plainJa: "建築の様式がとても趣深いですね。", zh: "建築樣式非常有韻味呢。" }
                ]
            },
            {
                id: 7,
                category: "商務餐會",
                title: "乾杯與感謝致辭",
                description: "晚宴開場時的互動發言與禮儀對話。",
                sentences: [
                    { speaker: "A", ja: "<ruby>今夜<rt>こんや</rt></ruby>はご<ruby>多忙<rt>たぼう</rt></ruby>のところ、お<ruby>集<rt>あつ</rt></ruby>まりいただき感謝いたします。", plainJa: "今夜はご多忙のところ、お集まりいただき感謝いたします。", zh: "今晚非常感謝大家在百忙之中抽空光臨。" },
                    { speaker: "B", ja: "こちらこそ、温かいおもてなしをありがとうございます。乾杯！", plainJa: "こちらこそ、温かいおもてなしをありがとうございます。乾杯！", zh: "我們才要感謝您熱情的款待。乾杯！" }
                ]
            },
            {
                id: 8,
                category: "休閒興趣",
                title: "週末休閒活動與運動",
                description: "聊聊高爾夫、露營或慢跑等休閒活動。",
                sentences: [
                    { speaker: "A", ja: "<ruby>週末<rt>しゅうまつ</rt></ruby>はよくゴルフやハイキングに行かれますか。", plainJa: "週末はよくゴルフやハイキングに行かれますか。", zh: "您週末常去打高爾夫球或健行嗎？" },
                    { speaker: "B", ja: "<ruby>最近<rt>さいきん</rt></ruby>は健康のためにジョギングを始めています。", plainJa: "最近は健康のためにジョギングを始めています。", zh: "最近為了健康著想，開始練習慢跑了。" }
                ]
            },
            {
                id: 9,
                category: "節慶文化",
                title: "中秋節與元宵燈會",
                description: "向外賓介紹台灣文化節慶活動。",
                sentences: [
                    { speaker: "A", ja: "<ruby>台灣<rt>たいわん</rt></ruby>では<ruby>中秋節<rt>ちゅうしゅうせつ</rt></ruby>に家族でバーベキューをする習慣があります。", plainJa: "台湾では中秋節に家族でバーベキューをする習慣があります。", zh: "在台灣中秋節有全家人一起烤肉的習慣。" },
                    { speaker: "B", ja: "それはユニークで楽しそうなお祭りですね！", plainJa: "それはユニークで楽しそうなお祭りですね！", zh: "那是個獨特又看起來很歡樂的節日呢！" }
                ]
            },
            {
                id: 10,
                category: "送別致謝",
                title: "離台送行與再次相見",
                description: "行程結束臨別時的溫馨致謝口譯對話。",
                sentences: [
                    { speaker: "A", ja: "今回の滞在が素晴らしい思い出になれば幸いです。", plainJa: "今回の滞在が素晴らしい思い出になれば幸いです。", zh: "希望這次的停留能成為您美好的回憶。" },
                    { speaker: "B", ja: "大変お世話になりました。また次回お会いできるのを楽しみにしています。", plainJa: "大変お世話になりました。また次回お会いできるのを楽しみにしています。", zh: "非常感謝您的照顧，非常期待下次能再與您相見。" }
                ]
            }
        ];

        /* State Management */
        let currentMainTab = 'pdf'; // 'pdf' | 'smalltalk'
        let activePdfUnitId = 'pdf-u1';
        let pdfStage = 1; 
        let activeSmallTalkId = 1;
        let smallTalkMode = 'standard';
        let showFurigana = true;
        let speechRate = 1.0;
        let isPlayingAll = false;
        let timerInterval = null;
        let timerSeconds = 120;
        let currentCategory = 'all';

        const synth = window.speechSynthesis;

        window.addEventListener('DOMContentLoaded', () => {
            renderPdfUnitList();
            renderSmallTalkCategories();
            renderSmallTalkTopicList();
            renderActiveContent();
            if (window.lucide) {
                lucide.createIcons();
            }
        });

        function switchMainTab(tab) {
            stopSpeech();
            currentMainTab = tab;

            const pdfBtn = document.getElementById('tab-pdf-train');
            const stBtn = document.getElementById('tab-smalltalk');
            const pdfPanel = document.getElementById('pdfUnitPanel');
            const stPanel = document.getElementById('smallTalkPanel');
            const pdfStageBar = document.getElementById('pdfStageBar');
            const stModeBar = document.getElementById('smallTalkModeBar');

            if (tab === 'pdf') {
                pdfBtn.className = "py-2 px-3 text-xs font-bold rounded-lg transition text-sky-700 bg-white shadow-sm flex items-center justify-center gap-1.5";
                stBtn.className = "py-2 px-3 text-xs font-medium text-slate-600 hover:text-slate-900 rounded-lg transition flex items-center justify-center gap-1.5";
                pdfPanel.classList.remove('hidden');
                stPanel.classList.add('hidden');
                pdfStageBar.classList.remove('hidden');
                stModeBar.classList.add('hidden');
            } else {
                stBtn.className = "py-2 px-3 text-xs font-bold rounded-lg transition text-sky-700 bg-white shadow-sm flex items-center justify-center gap-1.5";
                pdfBtn.className = "py-2 px-3 text-xs font-medium text-slate-600 hover:text-slate-900 rounded-lg transition flex items-center justify-center gap-1.5";
                stPanel.classList.remove('hidden');
                pdfPanel.classList.add('hidden');
                stModeBar.classList.remove('hidden');
                pdfStageBar.classList.add('hidden');
            }

            renderActiveContent();
        }

        function renderPdfUnitList() {
            const listEl = document.getElementById('pdfUnitList');
            document.getElementById('pdfUnitCount').innerText = pdfUnitsDatabase.length;

            listEl.innerHTML = pdfUnitsDatabase.map(u => {
                const isActive = u.id === activePdfUnitId;
                const activeStyle = isActive 
                    ? 'bg-sky-50 border-l-4 border-sky-600 text-sky-900 font-semibold' 
                    : 'hover:bg-slate-50 text-slate-700';

                return `
                    <div onclick="selectPdfUnit('${u.id}')" class="p-3.5 cursor-pointer transition flex items-start justify-between gap-2 border-b border-slate-100 ${activeStyle}">
                        <div class="space-y-1">
                            <h4 class="text-xs sm:text-sm leading-snug font-medium">${u.unitTitle}</h4>
                            <p class="text-[11px] text-slate-400 line-clamp-1">${u.description}</p>
                        </div>
                        <i data-lucide="chevron-right" class="w-4 h-4 text-slate-300 flex-shrink-0 mt-0.5"></i>
                    </div>
                `;
            }).join('');
            if (window.lucide) lucide.createIcons();
        }

        function selectPdfUnit(id) {
            stopSpeech();
            activePdfUnitId = id;
            renderPdfUnitList();
            renderActiveContent();
        }

        function setPdfStage(stage) {
            stopSpeech();
            pdfStage = stage;
            document.querySelectorAll('.pdf-stage-tab').forEach((btn, idx) => {
                if (idx + 1 === stage) {
                    btn.className = "pdf-stage-tab px-3 py-1.5 rounded-xl text-xs font-bold transition bg-sky-600 text-white shadow-sm flex items-center gap-1";
                } else {
                    btn.className = "pdf-stage-tab px-3 py-1.5 rounded-xl text-xs font-medium transition text-slate-600 hover:text-slate-900 flex items-center gap-1";
                }
            });
            renderActiveContent();
        }

        function renderActiveContent() {
            if (currentMainTab === 'pdf') {
                renderPdfStageContent();
            } else {
                renderSmallTalkContent();
            }
        }

        function renderPdfStageContent() {
            const unit = pdfUnitsDatabase.find(u => u.id === activePdfUnitId);
            if (!unit) return;

            document.getElementById('activeCategoryBadge').innerText = "《中日口譯叢書 2》";
            document.getElementById('activeDocBadge').innerText = unit.unitTitle.split('：')[0];
            document.getElementById('activeTitle').innerText = unit.unitTitle;
            document.getElementById('activeDesc').innerText = unit.description;

            const container = document.getElementById('contentContainer');

            if (pdfStage === 1) {
                container.innerHTML = `
                    <div class="bg-amber-50 border border-amber-200 p-3 rounded-2xl text-xs text-amber-800 font-medium flex items-center gap-2">
                        <i data-lucide="zap" class="w-4 h-4 text-amber-600 flex-shrink-0"></i>
                        <span>關卡 1 Quick Response：點擊卡片翻面看中文對照，點擊發音進行快速記憶反應訓練！</span>
                    </div>
                    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
                        ${unit.wordList.map((w, idx) => `
                            <div class="perspective-1000 h-36 cursor-pointer" onclick="this.querySelector('.card-inner').classList.toggle('rotate-y-180')">
                                <div class="card-inner relative w-full h-full duration-500 transform-style-3d transition-transform rounded-2xl shadow-sm border border-slate-200 bg-white">
                                    <div class="absolute inset-0 w-full h-full backface-hidden p-4 flex flex-col justify-between">
                                        <div class="flex justify-between items-center">
                                            <span class="text-[10px] px-2 py-0.5 rounded bg-sky-100 text-sky-800 font-bold">日文 (QR)</span>
                                            <button onclick="event.stopPropagation(); speakText('${w.ja}')" class="p-1 hover:bg-sky-50 rounded-lg text-sky-600">
                                                <i data-lucide="volume-2" class="w-4 h-4"></i>
                                            </button>
                                        </div>
                                        <div class="text-base font-bold text-slate-900 font-japanese">${w.ruby}</div>
                                        <span class="text-[10px] text-slate-400 text-right">點擊翻面對照</span>
                                    </div>
                                    <div class="absolute inset-0 w-full h-full backface-hidden rotate-y-180 p-4 flex flex-col justify-between bg-slate-900 text-white rounded-2xl">
                                        <span class="text-[10px] px-2 py-0.5 rounded bg-emerald-500/20 text-emerald-300 font-bold border border-emerald-500/30">中文對譯</span>
                                        <div class="text-sm font-bold text-slate-100">${w.zh}</div>
                                        <span class="text-[10px] text-slate-400 text-right">點擊翻回</span>
                                    </div>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                `;
            } else if (pdfStage === 2) {
                container.innerHTML = unit.sentences.map((s, idx) => `
                    <div id="sentence-card-${idx}" class="sentence-card bg-white p-4 sm:p-5 rounded-2xl border border-slate-200/80 shadow-sm space-y-3">
                        <div class="flex justify-between items-center border-b border-slate-100 pb-2">
                            <span class="text-xs font-bold px-2.5 py-0.5 rounded-full bg-slate-100 text-slate-700">視譯句型 #${idx + 1} (${s.speaker})</span>
                            <div class="flex gap-2">
                                <button onclick="toggleSightHide(${idx}, 'ja')" class="px-2 py-1 bg-slate-100 hover:bg-slate-200 rounded-lg text-xs font-medium text-slate-600">隱藏/顯示日文</button>
                                <button onclick="toggleSightHide(${idx}, 'zh')" class="px-2 py-1 bg-slate-100 hover:bg-slate-200 rounded-lg text-xs font-medium text-slate-600">隱藏/顯示中文</button>
                                <button onclick="speakSentence(${idx})" class="p-1.5 bg-sky-50 text-sky-600 hover:bg-sky-100 rounded-lg">
                                    <i data-lucide="volume-2" class="w-4 h-4"></i>
                                </button>
                            </div>
                        </div>
                        <div id="sight-ja-${idx}" class="text-base font-medium text-slate-900">${s.ja}</div>
                        <div id="sight-zh-${idx}" class="text-xs sm:text-sm text-slate-600 bg-slate-50 p-3 rounded-xl border border-slate-100">${s.zh}</div>
                    </div>
                `).join('');
            } else if (pdfStage === 3) {
                container.innerHTML = unit.sentences.map((s, idx) => `
                    <div id="sentence-card-${idx}" class="sentence-card bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm space-y-3">
                        <div class="flex justify-between items-center">
                            <span class="text-xs font-bold px-2.5 py-0.5 rounded-full bg-indigo-100 text-indigo-800">跟讀練習 #${idx + 1}</span>
                            <button onclick="speakSentence(${idx})" class="px-3 py-1.5 bg-sky-600 hover:bg-sky-700 text-white rounded-xl text-xs font-medium flex items-center gap-1.5">
                                <i data-lucide="volume-2" class="w-4 h-4"></i> 示範朗讀
                            </button>
                        </div>
                        <div class="text-lg font-medium text-slate-900 leading-relaxed font-japanese">${s.ja}</div>
                        <div class="text-xs text-slate-500">${s.zh}</div>
                        <div class="pt-2 border-t border-slate-100 flex items-center gap-3">
                            <button onclick="toggleRecord(${idx})" id="rec-btn-${idx}" class="px-3 py-1.5 rounded-xl border border-rose-200 bg-rose-50 text-rose-700 hover:bg-rose-100 text-xs font-medium flex items-center gap-1">
                                <i data-lucide="mic" class="w-3.5 h-3.5"></i> 錄音自我核對
                            </button>
                            <span id="rec-status-${idx}" class="text-xs text-slate-400">點擊開始錄音</span>
                        </div>
                    </div>
                `).join('');
            } else if (pdfStage === 4) {
                container.innerHTML = `
                    <div class="bg-indigo-900 text-white p-5 rounded-2xl shadow-sm space-y-4">
                        <div class="flex justify-between items-center">
                            <span class="text-xs px-2.5 py-0.5 rounded-full bg-indigo-500/30 text-indigo-200 border border-indigo-400/30 font-bold">關卡 4 即時口譯抽考測驗</span>
                            <button onclick="renderPdfStageContent()" class="text-xs bg-white/10 hover:bg-white/20 px-3 py-1 rounded-lg">換下一題</button>
                        </div>
                        <div class="space-y-2">
                            <p class="text-xs text-indigo-300">請嘗試將以下中文即時口譯翻譯為日文：</p>
                            <h3 class="text-lg font-bold">${unit.sentences[0].zh}</h3>
                        </div>
                        <div id="quizAnswerBox" class="hidden p-4 rounded-xl bg-indigo-950/80 border border-indigo-800 space-y-2">
                            <span class="text-xs font-bold text-sky-400">參考日文譯文：</span>
                            <div class="text-base text-slate-100">${unit.sentences[0].ja}</div>
                        </div>
                        <button onclick="document.getElementById('quizAnswerBox').classList.toggle('hidden')" class="w-full py-2.5 bg-sky-600 hover:bg-sky-500 rounded-xl font-bold text-xs transition">
                            揭開/隱藏參考解答
                        </button>
                    </div>
                `;
            }

            applyFuriganaVisibility();
            if (window.lucide) lucide.createIcons();
        }

        function toggleSightHide(idx, lang) {
            const el = document.getElementById(`sight-${lang}-${idx}`);
            if (el) el.classList.toggle('invisible');
        }

        function toggleRecord(idx) {
            const status = document.getElementById(`rec-status-${idx}`);
            const btn = document.getElementById(`rec-btn-${idx}`);
            if (status.innerText.includes('錄音中')) {
                status.innerText = "錄音完成！點擊播放可進行自我核對。";
                status.className = "text-xs text-emerald-600 font-medium";
                btn.className = "px-3 py-1.5 rounded-xl border border-slate-200 bg-slate-50 text-slate-700 text-xs font-medium flex items-center gap-1";
            } else {
                status.innerText = "🔴 錄音中...（請進行日口譯復述）";
                status.className = "text-xs text-rose-600 font-bold animate-pulse";
                btn.className = "px-3 py-1.5 rounded-xl border border-rose-500 bg-rose-600 text-white text-xs font-medium flex items-center gap-1";
            }
        }

        function toggleImportModal() {
            document.getElementById('importModal').classList.toggle('hidden');
        }

        function processCustomImport() {
            const title = document.getElementById('customUnitTitle').value.trim() || "自訂匯入講義單元";
            const rawText = document.getElementById('customDocText').value.trim();

            if (!rawText) return;

            const lines = rawText.split('\n').filter(l => l.trim().length > 0);
            const parsedSentences = [];

            lines.forEach((line, idx) => {
                const parts = line.split('|');
                if (parts.length >= 2) {
                    const ja = parts[0].trim();
                    const zh = parts[1].trim();
                    parsedSentences.push({
                        speaker: idx % 2 === 0 ? 'A' : 'B',
                        ja: ja,
                        plainJa: ja.replace(/<[^>]*>/g, ''),
                        zh: zh
                    });
                }
            });

            if (parsedSentences.length === 0) return;

            const newUnitId = `pdf-custom-${Date.now()}`;
            pdfUnitsDatabase.unshift({
                id: newUnitId,
                unitTitle: title,
                description: "使用者自訂匯入講義特訓單元。",
                wordList: [
                    { ja: "カスタム資料", ruby: "カスタム<ruby>資料<rt>しりょう</rt></ruby>", zh: "自訂講義內容" }
                ],
                sentences: parsedSentences
            });

            activePdfUnitId = newUnitId;
            renderPdfUnitList();
            toggleImportModal();
            switchMainTab('pdf');
        }

        function renderSmallTalkCategories() {
            const categories = ['all', ...new Set(smallTalkDatabase.map(t => t.category))];
            const container = document.getElementById('categoryContainer');
            container.innerHTML = categories.map(cat => {
                const label = cat === 'all' ? '全部' : cat;
                const active = currentCategory === cat ? 'bg-sky-600 text-white font-medium' : 'bg-slate-100 hover:bg-slate-200 text-slate-700';
                return `<button onclick="setSmallTalkCategory('${cat}')" class="px-2 py-0.5 rounded text-[11px] transition ${active}">${label}</button>`;
            }).join('');
        }

        function setSmallTalkCategory(cat) {
            currentCategory = cat;
            renderSmallTalkCategories();
            renderSmallTalkTopicList();
        }

        function filterTopics() {
            renderSmallTalkTopicList();
        }

        function renderSmallTalkTopicList() {
            const searchKeyword = document.getElementById('searchInput')?.value.toLowerCase().trim() || '';
            const listEl = document.getElementById('topicList');

            const filtered = smallTalkDatabase.filter(t => {
                const matchCategory = currentCategory === 'all' || t.category === currentCategory;
                const matchKeyword = t.title.toLowerCase().includes(searchKeyword) || t.description.toLowerCase().includes(searchKeyword);
                return matchCategory && matchKeyword;
            });

            if (filtered.length === 0) {
                listEl.innerHTML = `<div class="p-4 text-xs text-slate-400 text-center">找不到相關話題</div>`;
                return;
            }

            listEl.innerHTML = filtered.map(t => `
                <div onclick="selectSmallTalkTopic(${t.id})" class="p-3 cursor-pointer hover:bg-slate-50 transition border-b border-slate-100 flex justify-between items-center ${t.id === activeSmallTalkId ? 'bg-sky-50 font-bold text-sky-900' : 'text-slate-700'}">
                    <div>
                        <span class="text-xs block font-semibold">${t.title}</span>
                        <span class="text-[10px] text-slate-400">${t.category}</span>
                    </div>
                    <i data-lucide="chevron-right" class="w-4 h-4 text-slate-300"></i>
                </div>
            `).join('');
            if (window.lucide) lucide.createIcons();
        }

        function selectSmallTalkTopic(id) {
            stopSpeech();
            activeSmallTalkId = id;
            renderSmallTalkTopicList();
            renderActiveContent();
        }

        function setSmallTalkMode(mode) {
            smallTalkMode = mode;
            
            document.querySelectorAll('.st-mode-tab').forEach(btn => {
                if (btn.id === `mode-${mode}`) {
                    btn.className = "st-mode-tab flex-1 sm:flex-none px-3.5 py-1.5 text-xs font-semibold rounded-lg transition text-sky-700 bg-white shadow-sm";
                } else {
                    btn.className = "st-mode-tab flex-1 sm:flex-none px-3.5 py-1.5 text-xs font-medium text-slate-600 hover:text-slate-900 rounded-lg transition";
                }
            });

            renderActiveContent();
        }

        function renderSmallTalkContent() {
            const topic = smallTalkDatabase.find(t => t.id === activeSmallTalkId);
            if (!topic) return;

            document.getElementById('activeCategoryBadge').innerText = topic.category;
            document.getElementById('activeDocBadge').innerText = "Small Talk 破冰庫";
            document.getElementById('activeTitle').innerText = topic.title;
            document.getElementById('activeDesc').innerText = topic.description;

            const container = document.getElementById('contentContainer');

            if (smallTalkMode === 'standard') {
                container.innerHTML = topic.sentences.map((s, idx) => `
                    <div id="sentence-card-${idx}" class="sentence-card bg-white p-4 sm:p-5 rounded-2xl border border-slate-200/80 shadow-sm space-y-2">
                        <div class="flex items-center justify-between border-b border-slate-100 pb-2">
                            <span class="px-2.5 py-0.5 rounded-full text-xs font-bold bg-sky-100 text-sky-800">話者 ${s.speaker}</span>
                            <button onclick="speakSentence(${idx})" class="p-1.5 bg-slate-50 text-slate-600 hover:text-sky-600 rounded-lg">
                                <i data-lucide="volume-2" class="w-4 h-4"></i>
                            </button>
                        </div>
                        <div class="text-base font-medium text-slate-900 leading-relaxed font-japanese">${s.ja}</div>
                        <div class="text-xs sm:text-sm text-slate-600 bg-slate-50 p-2.5 rounded-xl border border-slate-100">${s.zh}</div>
                    </div>
                `).join('');
            } else if (smallTalkMode === 'flashcard') {
                container.innerHTML = `
                    <div class="bg-sky-50 border border-sky-200 p-3 rounded-2xl text-xs text-sky-800 font-medium flex items-center gap-2">
                        <i data-lucide="refresh-cw" class="w-4 h-4 text-sky-600 flex-shrink-0"></i>
                        <span>3D 翻轉卡模式：點擊卡片翻面進行日/中文對譯心算反應練習。</span>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        ${topic.sentences.map((s, idx) => `
                            <div class="perspective-1000 h-44 cursor-pointer" onclick="this.querySelector('.card-inner').classList.toggle('rotate-y-180')">
                                <div class="card-inner relative w-full h-full duration-500 transform-style-3d transition-transform rounded-2xl shadow-sm border border-slate-200 bg-white">
                                    <div class="absolute inset-0 w-full h-full backface-hidden p-5 flex flex-col justify-between">
                                        <div class="flex justify-between items-center">
                                            <span class="text-xs px-2.5 py-0.5 rounded-full bg-sky-100 text-sky-800 font-bold">話者 ${s.speaker} (日文)</span>
                                            <button onclick="event.stopPropagation(); speakSentence(${idx})" class="p-1.5 hover:bg-sky-50 rounded-lg text-sky-600">
                                                <i data-lucide="volume-2" class="w-4 h-4"></i>
                                            </button>
                                        </div>
                                        <div class="text-base font-bold text-slate-900 font-japanese leading-relaxed">${s.ja}</div>
                                        <span class="text-[10px] text-slate-400 text-right">點擊翻看中文</span>
                                    </div>
                                    <div class="absolute inset-0 w-full h-full backface-hidden rotate-y-180 p-5 flex flex-col justify-between bg-slate-900 text-white rounded-2xl">
                                        <div class="flex justify-between items-center">
                                            <span class="text-xs px-2.5 py-0.5 rounded-full bg-emerald-500/20 text-emerald-300 font-bold border border-emerald-500/30">中文對譯</span>
                                        </div>
                                        <div class="text-sm font-bold text-slate-100 leading-relaxed">${s.zh}</div>
                                        <span class="text-[10px] text-slate-400 text-right">點擊翻回日文</span>
                                    </div>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                `;
            } else if (smallTalkMode === 'mask') {
                container.innerHTML = `
                    <div class="bg-indigo-50 border border-indigo-200 p-3 rounded-2xl text-xs text-indigo-800 font-medium flex items-center gap-2">
                        <i data-lucide="eye-off" class="w-4 h-4 text-indigo-600 flex-shrink-0"></i>
                        <span>遮蔽測驗模式：點擊黑塊可解除遮蔽，考驗日文視譯與句子構詞能力。</span>
                    </div>
                    ${topic.sentences.map((s, idx) => `
                        <div id="sentence-card-${idx}" class="sentence-card bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm space-y-3">
                            <div class="flex items-center justify-between border-b border-slate-100 pb-2">
                                <span class="px-2.5 py-0.5 rounded-full text-xs font-bold bg-indigo-100 text-indigo-800">話者 ${s.speaker}</span>
                                <button onclick="speakSentence(${idx})" class="p-1.5 bg-slate-50 text-slate-600 hover:text-sky-600 rounded-lg">
                                    <i data-lucide="volume-2" class="w-4 h-4"></i>
                                </button>
                            </div>
                            <div class="text-base font-medium text-slate-900 leading-relaxed font-japanese">
                                <span onclick="this.classList.toggle('bg-slate-900'); this.classList.toggle('text-transparent');" class="bg-slate-900 text-transparent rounded px-1 cursor-pointer transition select-none" title="點擊顯示日文">${s.ja}</span>
                            </div>
                            <div class="text-xs sm:text-sm text-slate-600 bg-slate-50 p-3 rounded-xl border border-slate-100">${s.zh}</div>
                        </div>
                    `).join('')}
                `;
            }

            applyFuriganaVisibility();
            if (window.lucide) lucide.createIcons();
        }

        function toggleFurigana() {
            showFurigana = !showFurigana;
            document.getElementById('furiganaBtnText').innerText = showFurigana ? '隱藏假名' : '顯示假名';
            applyFuriganaVisibility();
        }

        function applyFuriganaVisibility() {
            const container = document.getElementById('contentContainer');
            if (showFurigana) {
                container.classList.remove('hide-furigana');
            } else {
                container.classList.add('hide-furigana');
            }
        }

        function changeSpeechRate(rate) {
            speechRate = parseFloat(rate);
        }

        function stopSpeech() {
            if (synth && synth.speaking) synth.cancel();
            isPlayingAll = false;
            clearHighlights();
            document.getElementById('playAllBtnText').innerText = '朗讀全文';
        }

        function clearHighlights() {
            document.querySelectorAll('.sentence-card').forEach(card => card.classList.remove('sentence-speaking'));
        }

        function speakText(text) {
            stopSpeech();
            if (!synth) return;
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'ja-JP';
            utterance.rate = speechRate;
            synth.speak(utterance);
        }

        function speakSentence(index) {
            stopSpeech();
            if (!synth) return;

            let sentences = [];
            if (currentMainTab === 'pdf') {
                const u = pdfUnitsDatabase.find(item => item.id === activePdfUnitId);
                if (u) sentences = u.sentences;
            } else {
                const t = smallTalkDatabase.find(item => item.id === activeSmallTalkId);
                if (t) sentences = t.sentences;
            }

            if (!sentences[index]) return;

            const card = document.getElementById(`sentence-card-${index}`);
            if (card) {
                card.classList.add('sentence-speaking');
                card.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
            }

            const utterance = new SpeechSynthesisUtterance(sentences[index].plainJa || sentences[index].ja.replace(/<[^>]*>/g, ''));
            utterance.lang = 'ja-JP';
            utterance.rate = speechRate;

            utterance.onend = () => {
                clearHighlights();
                const autoNext = document.getElementById('autoNextToggle').checked;
                if (isPlayingAll && autoNext && index + 1 < sentences.length) {
                    speakSentence(index + 1);
                } else {
                    stopSpeech();
                }
            };

            synth.speak(utterance);
        }

        function togglePlayAll() {
            if (isPlayingAll || (synth && synth.speaking)) {
                stopSpeech();
            } else {
                isPlayingAll = true;
                document.getElementById('playAllBtnText').innerText = '停止朗讀';
                speakSentence(0);
            }
        }

        function toggleTimerModal() {
            document.getElementById('timerModal').classList.toggle('hidden');
        }

        function startTimer() {
            const btn = document.getElementById('startTimerBtn');
            if (timerInterval) {
                clearInterval(timerInterval);
                timerInterval = null;
                btn.innerText = '繼續計時';
                return;
            }
            btn.innerText = '暫停';
            timerInterval = setInterval(() => {
                if (timerSeconds > 0) {
                    timerSeconds--;
                    updateTimerDisplays();
                } else {
                    clearInterval(timerInterval);
                    timerInterval = null;
                    btn.innerText = '開始計時';
                }
            }, 1000);
        }

        function resetTimer() {
            if (timerInterval) clearInterval(timerInterval);
            timerInterval = null;
            timerSeconds = 120;
            updateTimerDisplays();
            document.getElementById('startTimerBtn').innerText = '開始計時';
        }

        function updateTimerDisplays() {
            const mins = String(Math.floor(timerSeconds / 60)).padStart(2, '0');
            const secs = String(timerSeconds % 60).padStart(2, '0');
            document.getElementById('headerTimerDisplay').innerText = `${mins}:${secs}`;
            document.getElementById('modalTimerDisplay').innerText = `${mins}:${secs}`;
        }
    </script>
</body>
</html>
