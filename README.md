<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Petualangan Cerdas: Matematika & Pancasila</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;700;800;900&display=swap');

        body {
            font-family: 'Nunito', sans-serif;
            background: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%);
            min-height: 100vh;
            overflow-x: hidden;
            user-select: none;
        }

        .glass-panel {
            background: rgba(255, 255, 255, 0.9);
            backdrop-filter: blur(10px);
            border: 4px solid rgba(255, 255, 255, 1);
            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
        }

        .btn-game {
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
            border-bottom-width: 4px;
        }
        .btn-game:active {
            transform: translateY(4px);
            border-bottom-width: 0px;
            margin-top: 4px;
        }

        .animate-bounce-slow {
            animation: bounce-slow 3s infinite;
        }
        @keyframes bounce-slow {
            0%, 100% { transform: translateY(-5%); }
            50% { transform: translateY(5%); }
        }

        .screen { display: none; }
        .screen.active { display: block; animation: fadeIn 0.5s ease-in-out; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }

        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 10px; }
        ::-webkit-scrollbar-track { background: rgba(255,255,255,0.5); border-radius: 10px; }
        ::-webkit-scrollbar-thumb { background: #fbb6ce; border-radius: 10px; }
        ::-webkit-scrollbar-thumb:hover { background: #f687b3; }

        /* Map Nodes */
        .map-node {
            width: 80px; height: 80px;
            border-radius: 50%;
            display: flex; justify-content: center; align-items: center;
            font-size: 2rem;
            color: white;
            box-shadow: 0 6px 0 rgba(0,0,0,0.2);
            cursor: pointer;
            transition: transform 0.2s;
            position: relative;
        }
        .map-node.locked { background: #cbd5e1; box-shadow: 0 6px 0 #94a3b8; cursor: not-allowed; filter: grayscale(100%); }
        .map-node:not(.locked):hover { transform: scale(1.1); }
        .node-math { background: #3b82f6; box-shadow: 0 6px 0 #2563eb; }
        .node-panca { background: #10b981; box-shadow: 0 6px 0 #059669; }
        .map-path {
            position: absolute; background: #fff; height: 10px; z-index: -1;
        }

        /* Drag/Drop Match Cards */
        .match-card {
            cursor: pointer; border: 2px dashed #93c5fd; background: #eff6ff;
            transition: all 0.2s;
        }
        .match-card.selected { border: 2px solid #3b82f6; background: #bfdbfe; transform: scale(1.05); }
        .match-target {
            border: 2px dashed #cbd5e1; background: #f8fafc; min-height: 60px;
            display: flex; align-items: center; justify-content: center;
        }
        .match-target.filled { border: 2px solid #10b981; background: #d1fae5; }

        /* Print styles for Certificate */
        @media print {
            body * { visibility: hidden; }
            #certificateScreen, #certificateScreen * { visibility: visible; }
            #certificateScreen { position: absolute; left: 0; top: 0; width: 100%; margin: 0; padding: 0; }
            .no-print { display: none !important; }
            body { background: white; }
        }
    </style>
</head>
<body class="text-gray-800">

    <!-- Audio Toggle -->
    <button id="btn-audio" onclick="toggleAudio()" class="fixed top-4 right-4 z-50 bg-white p-3 rounded-full shadow-lg text-yellow-500 hover:bg-gray-50 no-print">
        <i class="fas fa-volume-up text-2xl" id="audio-icon"></i>
    </button>

    <!-- Modal Peringatan -->
    <div id="custom-modal" class="fixed inset-0 bg-black bg-opacity-50 z-[100] hidden flex items-center justify-center">
        <div class="bg-white rounded-3xl p-8 max-w-sm w-full text-center shadow-2xl transform scale-100 transition-transform border-4 border-yellow-400">
            <div class="text-yellow-500 text-5xl mb-4"><i class="fas fa-exclamation-triangle"></i></div>
            <h2 id="modal-title" class="text-2xl font-bold text-gray-800 mb-2">Perhatian</h2>
            <p id="modal-desc" class="text-gray-600 mb-6">Pesan peringatan.</p>
            <button onclick="closeModal()" class="btn-game bg-yellow-400 hover:bg-yellow-500 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#b45309]">
                Tutup
            </button>
        </div>
    </div>

    <div id="homeScreen" class="screen active min-h-screen flex items-center justify-center p-4">
        <div class="max-w-4xl w-full text-center">
            <div class="animate-bounce-slow mb-8">
                <h1 class="text-5xl md:text-7xl font-black text-transparent bg-clip-text bg-gradient-to-r from-blue-600 to-purple-600 drop-shadow-md mb-2">
                    PETUALANGAN CERDAS
                </h1>
                <p class="text-2xl md:text-3xl font-bold text-pink-500 drop-shadow-sm">Misi Pecahan & Penjaga Norma</p>
                <p class="text-lg text-gray-700 mt-2 italic font-semibold">“Berpikir Cerdas, Bertindak Adil, Menjadi Hebat!”</p>
            </div>

            <div class="glass-panel rounded-3xl p-8 max-w-md mx-auto">
                <h2 class="text-xl font-bold mb-4 text-gray-800">Siap bertualang? Masukkan data dirimu!</h2>
                <input type="text" id="playerName" placeholder="Nama Lengkapmu" class="w-full p-4 rounded-xl border-2 border-blue-200 focus:border-blue-500 focus:outline-none mb-4 text-center font-bold text-lg" autocomplete="off">
                <select id="playerClass" class="w-full p-4 rounded-xl border-2 border-blue-200 focus:border-blue-500 focus:outline-none mb-6 text-center font-bold text-lg appearance-none bg-white">
                    <option value="">-- Pilih Kelas --</option>
                    <option value="5A">Kelas 5A</option>
                    <option value="5B">Kelas 5B</option>
                </select>
                
                <button onclick="startGame()" class="btn-game w-full bg-green-500 hover:bg-green-600 text-white font-black text-xl py-4 rounded-full shadow-[0_4px_0_#059669] mb-4">
                    MULAI PETUALANGAN <i class="fas fa-play ml-2"></i>
                </button>
                <button onclick="nav('instructionScreen')" class="btn-game w-full bg-blue-400 hover:bg-blue-500 text-white font-bold text-lg py-3 rounded-full shadow-[0_4px_0_#2563eb]">
                    PETUNJUK PERMAINAN <i class="fas fa-book-open ml-2"></i>
                </button>
            </div>
            <p class="mt-8 text-gray-600 font-semibold text-sm">Dipersembahkan untuk Peserta Didik Kelas 5 SDN Parapat 1 Kota Tangerang</p>
        </div>
    </div>

    <div id="instructionScreen" class="screen min-h-screen p-4 flex items-center justify-center">
        <div class="glass-panel rounded-3xl p-8 max-w-3xl w-full">
            <h2 class="text-3xl font-black text-center text-blue-600 mb-6"><i class="fas fa-map-signs mr-2"></i> Petunjuk Permainan</h2>
            <div class="space-y-4 text-gray-700 text-lg font-medium">
                <div class="flex items-start"><div class="bg-blue-500 text-white rounded-full w-8 h-8 flex items-center justify-center font-bold mr-3 flex-shrink-0">1</div><p>Masukkan namamu dan pilih kelas dengan benar.</p></div>
                <div class="flex items-start"><div class="bg-blue-500 text-white rounded-full w-8 h-8 flex items-center justify-center font-bold mr-3 flex-shrink-0">2</div><p>Di Peta Petualangan, pilih dunia yang ingin kamu jelajahi: <b>Matematika</b> atau <b>Pancasila</b>.</p></div>
                <div class="flex items-start"><div class="bg-blue-500 text-white rounded-full w-8 h-8 flex items-center justify-center font-bold mr-3 flex-shrink-0">3</div><p>Setiap pelajaran memiliki 3 Level. Kamu harus menyelesaikannya secara berurutan.</p></div>
                <div class="flex items-start"><div class="bg-blue-500 text-white rounded-full w-8 h-8 flex items-center justify-center font-bold mr-3 flex-shrink-0">4</div><p>Setiap level terdiri dari: Baca Materi, Pasangkan Jawaban, Sesuai/Tidak Sesuai, Isian Singkat, dan Uji Kompetensi (Pilihan Ganda).</p></div>
                <div class="flex items-start"><div class="bg-blue-500 text-white rounded-full w-8 h-8 flex items-center justify-center font-bold mr-3 flex-shrink-0">5</div><p>Kumpulkan poin dan selesaikan kedua mata pelajaran untuk mendapatkan <b>Sertifikat Pencapaian Spesial</b> dari gurumu!</p></div>
            </div>
            <div class="mt-8 text-center">
                <button onclick="nav('homeScreen')" class="btn-game bg-gray-500 hover:bg-gray-600 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#374151]">
                    <i class="fas fa-arrow-left mr-2"></i> KEMBALI
                </button>
            </div>
        </div>
    </div>

    <div id="mapScreen" class="screen min-h-screen p-4 flex flex-col items-center">
        <div class="w-full max-w-5xl flex justify-between items-center mb-8 bg-white/80 rounded-2xl p-4 shadow-sm border-2 border-white">
            <div class="font-bold text-gray-700 text-lg">
                <i class="fas fa-user-astronaut text-blue-500"></i> Hai, <span id="ui-playerName" class="text-blue-600"></span> (<span id="ui-playerClass"></span>)
            </div>
            <button onclick="confirmRestart()" class="text-red-500 hover:text-red-600 font-bold"><i class="fas fa-sign-out-alt"></i> Keluar</button>
        </div>

        <h1 class="text-4xl font-black text-center text-white drop-shadow-md mb-12">Peta Petualangan</h1>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-12 w-full max-w-4xl">
            <!-- Dunia Matematika -->
            <div class="glass-panel rounded-3xl p-8 flex flex-col items-center relative">
                <div class="absolute -top-6 bg-blue-500 text-white py-2 px-6 rounded-full font-black text-xl shadow-lg border-4 border-white">
                    <i class="fas fa-calculator"></i> Kerajaan Pecahan
                </div>
                <div class="mt-8 space-y-12 relative w-full flex flex-col items-center">
                    <div class="w-2 bg-blue-200 absolute top-0 bottom-0 z-0"></div>
                    
                    <div id="node-math-1" class="map-node node-math z-10" onclick="enterLevel('math', 1)">
                        <i class="fas fa-star"></i>
                    </div>
                    <div class="text-center font-bold text-gray-700 -mt-10 mb-4 bg-white/80 px-3 rounded-full">Level 1: Penjelajah</div>

                    <div id="node-math-2" class="map-node node-math locked z-10" onclick="enterLevel('math', 2)">
                        <i class="fas fa-lock"></i>
                    </div>
                    <div class="text-center font-bold text-gray-700 -mt-10 mb-4 bg-white/80 px-3 rounded-full">Level 2: Ahli Strategi</div>

                    <div id="node-math-3" class="map-node node-math locked z-10" onclick="enterLevel('math', 3)">
                        <i class="fas fa-lock"></i>
                    </div>
                    <div class="text-center font-bold text-gray-700 -mt-10 bg-white/80 px-3 rounded-full">Level 3: Master</div>
                </div>
            </div>

            <!-- Dunia Pancasila -->
            <div class="glass-panel rounded-3xl p-8 flex flex-col items-center relative">
                <div class="absolute -top-6 bg-green-500 text-white py-2 px-6 rounded-full font-black text-xl shadow-lg border-4 border-white">
                    <i class="fas fa-shield-alt"></i> Negeri Penjaga Norma
                </div>
                <div class="mt-8 space-y-12 relative w-full flex flex-col items-center">
                    <div class="w-2 bg-green-200 absolute top-0 bottom-0 z-0"></div>
                    
                    <div id="node-panca-1" class="map-node node-panca z-10" onclick="enterLevel('panca', 1)">
                        <i class="fas fa-star"></i>
                    </div>
                    <div class="text-center font-bold text-gray-700 -mt-10 mb-4 bg-white/80 px-3 rounded-full">Level 1: Sahabat Norma</div>

                    <div id="node-panca-2" class="map-node node-panca locked z-10" onclick="enterLevel('panca', 2)">
                        <i class="fas fa-lock"></i>
                    </div>
                    <div class="text-center font-bold text-gray-700 -mt-10 mb-4 bg-white/80 px-3 rounded-full">Level 2: Pahlawan Hak</div>

                    <div id="node-panca-3" class="map-node node-panca locked z-10" onclick="enterLevel('panca', 3)">
                        <i class="fas fa-lock"></i>
                    </div>
                    <div class="text-center font-bold text-gray-700 -mt-10 bg-white/80 px-3 rounded-full">Level 3: Penjaga Keadilan</div>
                </div>
            </div>
        </div>

        <div class="mt-12 text-center w-full max-w-4xl">
            <button id="btn-cert-unlock" onclick="viewCertificate()" class="hidden btn-game w-full bg-gradient-to-r from-yellow-400 to-orange-500 hover:from-yellow-500 hover:to-orange-600 text-white font-black text-2xl py-6 rounded-3xl shadow-[0_6px_0_#b45309] border-4 border-white">
                <i class="fas fa-trophy text-3xl mr-3"></i> BUKA SERTIFIKAT PENCAPAIAN <i class="fas fa-trophy text-3xl ml-3"></i>
            </button>
            <div id="cert-locked-msg" class="text-gray-700 font-bold bg-white/50 py-3 rounded-full">
                <i class="fas fa-info-circle text-blue-500"></i> Selesaikan semua level Matematika dan Pancasila untuk membuka Sertifikat!
            </div>
        </div>
    </div>

    <div id="levelScreen" class="screen min-h-screen p-4 flex flex-col items-center">
        <div class="w-full max-w-4xl mb-4 flex justify-between items-center">
            <button onclick="nav('mapScreen')" class="btn-game bg-white text-gray-700 font-bold py-2 px-4 rounded-full shadow border-2 border-gray-200">
                <i class="fas fa-arrow-left"></i> Kembali ke Peta
            </button>
            <div class="font-black text-xl text-white drop-shadow-md" id="level-title-display">Mata Pelajaran - Level X</div>
        </div>

        <div class="glass-panel rounded-3xl p-6 md:p-10 w-full max-w-4xl relative">
            
            <!-- Progress Bar -->
            <div class="w-full bg-gray-200 rounded-full h-4 mb-8 border-2 border-white shadow-inner">
                <div id="level-progress-bar" class="bg-yellow-400 h-full rounded-full transition-all duration-500" style="width: 20%"></div>
            </div>

            <!-- Bagian A: Materi -->
            <div id="part-A" class="level-part active">
                <h2 class="text-3xl font-black text-blue-600 mb-4 border-b-4 border-blue-200 pb-2"><i class="fas fa-book-reader"></i> Bagian A: Pelajari Materi</h2>
                <div id="materi-content" class="text-lg text-gray-700 space-y-4 bg-blue-50 p-6 rounded-2xl border-2 border-blue-100">
                    <!-- Dinamis -->
                </div>
                <div class="mt-8 text-right">
                    <button onclick="nextPart('B')" class="btn-game bg-blue-500 hover:bg-blue-600 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#1e3a8a]">
                        MENGERTI, LANJUTKAN <i class="fas fa-arrow-right ml-2"></i>
                    </button>
                </div>
            </div>

            <!-- Bagian B: Drag and Drop (Klik-Pasangkan) -->
            <div id="part-B" class="level-part hidden">
                <h2 class="text-3xl font-black text-green-600 mb-4 border-b-4 border-green-200 pb-2"><i class="fas fa-puzzle-piece"></i> Bagian B: Pasangkan Jawaban</h2>
                <p class="mb-6 font-bold text-gray-600 bg-yellow-100 p-3 rounded-lg"><i class="fas fa-lightbulb text-yellow-500"></i> Cara main: Klik/Sentuh KARTU pilihan di bawah, lalu klik/sentuh KOTAK KOSONG pasangannya!</p>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                    <!-- Targets -->
                    <div id="dnd-targets" class="space-y-4"></div>
                    <!-- Sources (Cards) -->
                    <div class="bg-gray-50 p-4 rounded-xl border-2 border-gray-200">
                        <h3 class="font-bold text-center mb-4 text-gray-500">Pilihan Jawaban</h3>
                        <div id="dnd-sources" class="grid grid-cols-1 gap-3"></div>
                    </div>
                </div>
                
                <div class="mt-8 flex justify-between items-center">
                    <div id="dnd-feedback" class="font-bold text-lg hidden"></div>
                    <button onclick="checkDnD()" class="btn-game bg-green-500 hover:bg-green-600 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#065f46]">
                        PERIKSA JAWABAN <i class="fas fa-check ml-2"></i>
                    </button>
                </div>
                <div id="dnd-next-btn" class="mt-4 text-right hidden">
                    <button onclick="nextPart('C')" class="btn-game bg-blue-500 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#1e3a8a]">LANJUT BAGIAN C <i class="fas fa-arrow-right"></i></button>
                </div>
            </div>

            <!-- Bagian C: Sesuai/Tidak Sesuai -->
            <div id="part-C" class="level-part hidden">
                <h2 class="text-3xl font-black text-purple-600 mb-4 border-b-4 border-purple-200 pb-2"><i class="fas fa-balance-scale"></i> Bagian C: Sesuai atau Tidak Sesuai?</h2>
                <p class="mb-6 font-bold text-gray-600">Bacalah pernyataan di bawah ini, lalu tentukan apakah SESUAI atau TIDAK SESUAI.</p>
                
                <div id="tf-questions" class="space-y-6">
                    <!-- Dinamis -->
                </div>

                <div class="mt-8 flex justify-between items-center">
                    <div id="tf-feedback" class="font-bold text-lg hidden"></div>
                    <button onclick="checkTF()" class="btn-game bg-purple-500 hover:bg-purple-600 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#4c1d95]">
                        PERIKSA JAWABAN <i class="fas fa-check ml-2"></i>
                    </button>
                </div>
                <div id="tf-next-btn" class="mt-4 text-right hidden">
                    <button onclick="nextPart('D')" class="btn-game bg-blue-500 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#1e3a8a]">LANJUT BAGIAN D <i class="fas fa-arrow-right"></i></button>
                </div>
            </div>

            <!-- Bagian D: Isian Singkat -->
            <div id="part-D" class="level-part hidden">
                <h2 class="text-3xl font-black text-orange-600 mb-4 border-b-4 border-orange-200 pb-2"><i class="fas fa-pencil-alt"></i> Bagian D: Isian Singkat</h2>
                <p class="mb-6 font-bold text-gray-600">Ketiklah jawaban singkat yang paling tepat pada kolom yang disediakan.</p>
                
                <div id="fitb-questions" class="space-y-6">
                    <!-- Dinamis -->
                </div>

                <div class="mt-8 flex justify-between items-center">
                    <div id="fitb-feedback" class="font-bold text-lg hidden"></div>
                    <div>
                        <button onclick="resetFITB()" class="btn-game bg-gray-400 hover:bg-gray-500 text-white font-bold py-3 px-6 rounded-full shadow-[0_4px_0_#374151] mr-2">
                            KOSONGKAN
                        </button>
                        <button onclick="checkFITB()" class="btn-game bg-orange-500 hover:bg-orange-600 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#9a3412]">
                            PERIKSA JAWABAN <i class="fas fa-check ml-2"></i>
                        </button>
                    </div>
                </div>
                <div id="fitb-next-btn" class="mt-4 text-right hidden">
                    <button onclick="nextPart('E')" class="btn-game bg-blue-500 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#1e3a8a]">LANJUT UJI KOMPETENSI <i class="fas fa-arrow-right"></i></button>
                </div>
            </div>

            <!-- Bagian E: Uji Kompetensi -->
            <div id="part-E" class="level-part hidden">
                <h2 class="text-3xl font-black text-red-600 mb-4 border-b-4 border-red-200 pb-2"><i class="fas fa-award"></i> Uji Kompetensi Akhir Level</h2>
                <p class="mb-6 font-bold text-gray-600 bg-red-50 p-4 rounded-xl border border-red-100">Kerjakan 10 soal pilihan ganda di bawah ini dengan teliti. Nilai akan menentukan kelulusan levelmu!</p>
                
                <div class="flex justify-between items-center mb-4 bg-gray-100 p-3 rounded-full px-6 font-bold text-gray-700">
                    <div>Soal: <span id="mc-current-num">1</span> / 10</div>
                </div>

                <div id="mc-question-container" class="bg-white p-6 rounded-2xl shadow-sm border-2 border-gray-100 mb-6 min-h-[250px]">
                    <div id="mc-q-text" class="text-xl font-bold text-gray-800 mb-6">Pertanyaan...</div>
                    <div id="mc-options" class="grid grid-cols-1 gap-3">
                        <!-- Dinamis -->
                    </div>
                </div>

                <div class="flex justify-between">
                    <button onclick="prevMC()" id="btn-mc-prev" class="btn-game bg-gray-400 text-white font-bold py-3 px-6 rounded-full shadow-[0_4px_0_#4b5563] disabled:opacity-50 disabled:cursor-not-allowed">
                        <i class="fas fa-arrow-left mr-2"></i> SEBELUMNYA
                    </button>
                    <button onclick="nextMC()" id="btn-mc-next" class="btn-game bg-blue-500 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#1e3a8a]">
                        BERIKUTNYA <i class="fas fa-arrow-right ml-2"></i>
                    </button>
                    <button onclick="finishLevel()" id="btn-mc-finish" class="hidden btn-game bg-red-500 hover:bg-red-600 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#991b1b]">
                        SELESAI & PERIKSA <i class="fas fa-flag-checkered ml-2"></i>
                    </button>
                </div>
            </div>
        </div>
    </div>

    <div id="resultScreen" class="screen min-h-screen p-4 flex items-center justify-center">
        <div class="glass-panel rounded-3xl p-8 max-w-2xl w-full text-center">
            <h1 class="text-4xl font-black text-gray-800 mb-2" id="res-title">Hasil Level Selesai!</h1>
            <p class="text-xl text-gray-600 mb-6 font-bold" id="res-subtitle">Matematika - Level 1</p>
            
            <div class="bg-blue-50 rounded-3xl p-6 mb-8 border-4 border-white shadow-inner">
                <div class="text-6xl font-black mb-2" id="res-score-display" style="color: #3b82f6;">100</div>
                <div class="text-2xl font-bold text-yellow-500 mb-4" id="res-predikat">SANG JUARA</div>
                
                <div class="grid grid-cols-2 gap-4 text-left bg-white p-4 rounded-2xl">
                    <div class="font-semibold text-gray-600 border-b pb-2">Drag & Drop: <span id="res-s-dnd" class="float-right font-bold text-gray-800"></span></div>
                    <div class="font-semibold text-gray-600 border-b pb-2">Sesuai/Tidak: <span id="res-s-tf" class="float-right font-bold text-gray-800"></span></div>
                    <div class="font-semibold text-gray-600 border-b pb-2">Isian Singkat: <span id="res-s-fitb" class="float-right font-bold text-gray-800"></span></div>
                    <div class="font-semibold text-gray-600 border-b pb-2">Uji Kompetensi: <span id="res-s-mc" class="float-right font-bold text-gray-800"></span></div>
                </div>
                <p id="res-feedback-msg" class="mt-4 font-bold text-gray-700 italic">"Luar biasa, kamu memahami konsepnya!"</p>
            </div>

            <div class="space-x-4">
                <button onclick="nav('mapScreen')" class="btn-game bg-green-500 hover:bg-green-600 text-white font-bold py-3 px-8 rounded-full shadow-[0_4px_0_#065f46]">
                    KEMBALI KE PETA <i class="fas fa-map ml-2"></i>
                </button>
            </div>
        </div>
    </div>

    <div id="certificateScreen" class="screen min-h-screen flex flex-col items-center justify-center p-4 bg-gray-100">
        <div class="no-print mb-6 space-x-4">
            <button onclick="nav('mapScreen')" class="btn-game bg-gray-500 text-white font-bold py-2 px-6 rounded-full shadow">Kembali</button>
            <button onclick="window.print()" class="btn-game bg-blue-600 text-white font-bold py-2 px-6 rounded-full shadow"><i class="fas fa-print"></i> Cetak / Unduh PDF</button>
        </div>

        <div class="bg-white p-2 shadow-2xl relative overflow-hidden" style="width: 1056px; height: 750px; max-width: 100%; border: 15px solid #fbb6ce;">
            <!-- Desain Sertifikat -->
            <div class="absolute inset-0 border-[10px] border-[#a8edea] m-2"></div>
            <div class="absolute inset-0 border-[2px] border-yellow-400 m-4"></div>
            
            <!-- Ornamen -->
            <div class="absolute top-8 left-8 text-6xl text-yellow-300 opacity-50"><i class="fas fa-star"></i></div>
            <div class="absolute bottom-8 right-8 text-6xl text-blue-300 opacity-50"><i class="fas fa-award"></i></div>

            <div class="relative z-10 flex flex-col items-center justify-center h-full text-center px-16 pt-8">
                <h1 class="text-6xl font-black text-transparent bg-clip-text bg-gradient-to-r from-blue-700 to-purple-700 tracking-wider mb-2">
                    SERTIFIKAT PENCAPAIAN
                </h1>
                <p class="text-xl font-bold text-gray-500 tracking-widest mb-10">PETUALANGAN CERDAS: MISI PECAHAN & PENJAGA NORMA</p>

                <p class="text-2xl text-gray-700 mb-2">Diberikan kepada:</p>
                <h2 id="cert-name" class="text-5xl font-black text-gray-900 border-b-4 border-yellow-400 pb-2 mb-4 w-3/4 mx-auto inline-block">NAMA MURID</h2>
                <p id="cert-class" class="text-2xl font-bold text-blue-600 mb-8">Kelas 5A — SDN Parapat 1 Kota Tangerang</p>

                <p class="text-lg text-gray-700 italic mb-8 max-w-3xl leading-relaxed">
                    Sebagai penghargaan atas semangat, kemandirian, dan keberhasilan luar biasa dalam menyelesaikan seluruh misi Matematika dan Pendidikan Pancasila.
                </p>

                <div class="flex justify-center gap-12 w-full mb-10">
                    <div class="bg-blue-50 border-2 border-blue-200 p-4 rounded-xl min-w-[200px]">
                        <div class="text-sm text-gray-500 font-bold uppercase">Skor Matematika</div>
                        <div id="cert-score-math" class="text-3xl font-black text-blue-600">100</div>
                    </div>
                    <div class="bg-green-50 border-2 border-green-200 p-4 rounded-xl min-w-[200px]">
                        <div class="text-sm text-gray-500 font-bold uppercase">Skor Pancasila</div>
                        <div id="cert-score-panca" class="text-3xl font-black text-green-600">100</div>
                    </div>
                </div>

                <div class="flex justify-between w-full max-w-4xl px-12 mt-auto pb-8">
                    <div class="text-center">
                        <div class="w-48 border-b border-gray-400 h-16 mb-2"></div>
                        <p class="font-bold text-gray-800 text-lg">Hayati Yusriyati, S.Pd.</p>
                        <p class="text-gray-500">Wali Kelas 5A</p>
                    </div>
                    <div class="text-center flex flex-col justify-end">
                        <div class="text-yellow-500 text-5xl mb-2"><i class="fas fa-medal"></i></div>
                        <p class="font-bold text-gray-500 text-sm" id="cert-date">Tanggal</p>
                    </div>
                    <div class="text-center">
                        <div class="w-48 border-b border-gray-400 h-16 mb-2"></div>
                        <p class="font-bold text-gray-800 text-lg">Citra Dewi Nurlela Afni, S.Pd.</p>
                        <p class="text-gray-500">Wali Kelas 5B</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // State
        const state = {
            name: '',
            cls: '',
            progress: { math: 1, panca: 1 }, // Levels unlocked (1-3)
            scores: {
                math: { 1: null, 2: null, 3: null },
                panca: { 1: null, 2: null, 3: null }
            },
            currentPlay: {
                subject: null, level: null,
                ansDnD: {}, ansTF: {}, ansFITB: {}, ansMC: {},
                mcIndex: 0
            }
        };

        // Audio Engine (Web Audio API - No external files)
        let audioCtx;
        let audioEnabled = false;

        function initAudio() {
            if (!audioCtx) {
                const AudioContext = window.AudioContext || window.webkitAudioContext;
                audioCtx = new AudioContext();
            }
            if(audioCtx.state === 'suspended') audioCtx.resume();
            audioEnabled = !audioEnabled;
            document.getElementById('audio-icon').className = audioEnabled ? 'fas fa-volume-up text-2xl' : 'fas fa-volume-mute text-2xl text-gray-400';
            if(audioEnabled) playTone(440, 'sine', 0.1);
        }
        function toggleAudio() { initAudio(); }
        function playTone(freq, type, duration) {
            if (!audioEnabled || !audioCtx) return;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = type; osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
            gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + duration);
            osc.connect(gain); gain.connect(audioCtx.destination);
            osc.start(); osc.stop(audioCtx.currentTime + duration);
        }
        const sfx = {
            click: () => playTone(600, 'sine', 0.05),
            correct: () => { playTone(523.25, 'triangle', 0.1); setTimeout(()=>playTone(659.25, 'triangle', 0.2), 100); },
            wrong: () => { playTone(300, 'sawtooth', 0.1); setTimeout(()=>playTone(250, 'sawtooth', 0.2), 150); },
            levelUp: () => { playTone(440, 'sine', 0.1); setTimeout(()=>playTone(554, 'sine', 0.1), 100); setTimeout(()=>playTone(659, 'sine', 0.3), 200); }
        };

        // UI Helpers
        function showModal(title, desc) {
            document.getElementById('modal-title').innerText = title;
            document.getElementById('modal-desc').innerText = desc;
            document.getElementById('custom-modal').classList.remove('hidden');
            sfx.wrong();
        }
        function closeModal() {
            document.getElementById('custom-modal').classList.add('hidden');
            sfx.click();
        }
        function nav(screenId) {
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById(screenId).classList.add('active');
            window.scrollTo(0, 0);
            sfx.click();
        }

        // To strictly meet the 150 question prompt requirement within constraints, we systematically define them here.
        // Math: 75 Qs (25/level), Panca: 75 Qs (25/level).

        const db = {
            math: {
                1: { // Level 1: Penyebut Sama
                    materi: `<p><b>Pengurangan Pecahan Berpenyebut Sama</b></p><p>Jika penyebut (angka di bawah) sudah sama, kamu hanya perlu mengurangkan pembilang (angka di atas).</p><p>Contoh: <b>4/5 - 1/5 = (4-1)/5 = 3/5</b></p><br><p>Dalam kehidupan sehari-hari: Ibu punya 4/6 potong kue. Dimakan adik 2/6. Sisa kue ibu adalah 4/6 - 2/6 = 2/6.</p>`,
                    dnd: [
                        {q: "7/8 - 3/8", a: "4/8"}, {q: "5/6 - 1/6", a: "4/6"}, {q: "9/10 - 2/10", a: "7/10"}, {q: "4/5 - 2/5", a: "2/5"}, {q: "3/4 - 1/4", a: "2/4"}
                    ],
                    tf: [
                        {q: "8/12 - 3/12 = 5/12", a: true}, {q: "5/7 - 2/7 = 3/14", a: false, fb: "Penyebut tidak dikurangkan, tetap 7."},
                        {q: "6/9 - 4/9 = 2/9", a: true}, {q: "Ibu punya air 5/5 liter, diminum 2/5 liter, sisanya 3/5 liter", a: true},
                        {q: "7/10 - 7/10 = 1/10", a: false, fb: "7/10 - 7/10 = 0/10 atau 0."}
                    ],
                    fitb: [
                        {q: "4/7 - 1/7 = ... / 7", a: "3"}, {q: "8/15 - 4/15 = 4 / ...", a: "15"},
                        {q: "Budi punya pita 6/8 meter. Dipakai 3/8 meter. Sisa pitanya ... / 8 meter", a: "3"},
                        {q: "5/9 - .../9 = 1/9", a: "4"}, {q: "9/11 - 2/11 = ... / 11", a: "7"}
                    ],
                    mc: [
                        {q: "Hasil dari 6/7 - 2/7 adalah...", opts: ["4/7", "8/7", "4/14", "3/7"], a: 0},
                        {q: "10/12 - 5/12 = ...", opts: ["15/12", "5/12", "5/0", "10/12"], a: 1},
                        {q: "Kue dipotong 8 bagian. Adik mengambil 3 potong (3/8). Sisa kue dalam pecahan adalah...", opts: ["5/8", "3/8", "8/8", "11/8"], a: 0},
                        {q: "Beni memiliki tongkat 7/10 meter. Ia memotongnya 2/10 meter. Berapa panjang sisanya?", opts: ["5/10 m", "9/10 m", "5/20 m", "6/10 m"], a: 0},
                        {q: "Perhatikan pengurangan ini: .../5 - 1/5 = 3/5. Angka yang tepat untuk mengisi titik-titik adalah...", opts: ["3", "4", "5", "2"], a: 1},
                        {q: "Pernyataan mana yang salah?", opts: ["3/4 - 1/4 = 2/4", "5/5 - 1/5 = 4/5", "6/8 - 2/8 = 4/0", "9/10 - 3/10 = 6/10"], a: 2},
                        {q: "Siti mencampur 8/9 liter air sirup. Ia menuangkan 3/9 liter ke gelas. Sisa di teko adalah...", opts: ["5/9 liter", "11/9 liter", "5/0 liter", "6/9 liter"], a: 0},
                        {q: "Hasil dari 15/20 - 5/20 adalah...", opts: ["10/0", "10/20", "20/20", "5/20"], a: 1},
                        {q: "Edo berlari sejauh 9/15 km. Dayu berlari 4/15 km lebih dekat dari Edo. Jarak lari Dayu adalah...", opts: ["5/15 km", "13/15 km", "4/15 km", "9/15 km"], a: 0},
                        {q: "Jika 7/8 - a/8 = 2/8, maka nilai a adalah...", opts: ["4", "5", "6", "9"], a: 1}
                    ]
                },
                2: { // Level 2: Penyebut Beda (KPK)
                    materi: `<p><b>Pengurangan Pecahan Berpenyebut Berbeda</b></p><p>Jika penyebut berbeda, kita wajib menyamakannya terlebih dahulu menggunakan <b>KPK (Kelipatan Persekutuan Terkecil)</b>.</p><p>Contoh: <b>1/2 - 1/3</b></p><p>KPK dari 2 dan 3 adalah 6.</p><p>Ubah: 1/2 menjadi 3/6, dan 1/3 menjadi 2/6.</p><p>Hasil: <b>3/6 - 2/6 = 1/6</b></p>`,
                    dnd: [
                        {q: "1/2 - 1/4", a: "1/4"}, {q: "2/3 - 1/6", a: "3/6"}, {q: "3/4 - 1/2", a: "1/4"}, {q: "4/5 - 3/10", a: "5/10"}, {q: "5/6 - 1/3", a: "3/6"}
                    ],
                    tf: [
                        {q: "Untuk menghitung 1/2 - 1/3, KPK penyebut yang dipakai adalah 6.", a: true},
                        {q: "3/4 - 1/2 = 2/2", a: false, fb: "Penyebut harus disamakan jadi 4, bukan dikurangkan langsung."},
                        {q: "2/5 - 1/10 = 3/10", a: true}, {q: "1/2 - 1/4 = 0", a: false, fb: "1/2 adalah 2/4. 2/4 - 1/4 = 1/4."},
                        {q: "KPK dari 3 dan 4 untuk pecahan 1/3 dan 1/4 adalah 12.", a: true}
                    ],
                    fitb: [
                        {q: "KPK dari penyebut pada pecahan 1/4 dan 1/6 adalah ...", a: "12"},
                        {q: "Bentuk 1/2 jika diubah berpenyebut 8 menjadi ... / 8", a: "4"},
                        {q: "3/5 - 1/10. Samakan penyebut jadi 10. Maka 3/5 menjadi ... / 10", a: "6"},
                        {q: "Hasil dari 1/2 - 1/4 adalah 1 / ...", a: "4"},
                        {q: "Jika KPK 3 dan 4 adalah 12, maka 2/3 - 1/4 = (8-3)/12 = ... / 12", a: "5"}
                    ],
                    mc: [
                        {q: "Penyebut bersama (KPK) yang tepat untuk pecahan 1/4 dan 1/3 adalah...", opts: ["7", "12", "6", "24"], a: 1},
                        {q: "Hasil dari 1/2 - 1/3 adalah...", opts: ["1/6", "2/5", "0/1", "1/5"], a: 0},
                        {q: "Ibu membeli 3/4 kg gula. Dipakai membuat kue 1/2 kg. Sisa gula ibu adalah...", opts: ["1/4 kg", "2/4 kg", "2/2 kg", "1/2 kg"], a: 0},
                        {q: "Hasil pengurangan 5/6 - 1/2 adalah...", opts: ["4/4", "3/6", "2/6", "4/6"], a: 2},
                        {q: "Bentuk ekuivalen dari 2/3 dengan penyebut 9 adalah...", opts: ["4/9", "6/9", "5/9", "8/9"], a: 1},
                        {q: "Panjang pita kakak 4/5 meter. Diberikan ke adik 1/2 meter. Sisa pita kakak adalah...", opts: ["3/10 m", "3/3 m", "4/10 m", "5/10 m"], a: 0},
                        {q: "Diketahui 2/3 - 1/4. Langkah pertama yang benar adalah mengubahnya menjadi...", opts: ["8/12 - 3/12", "4/12 - 3/12", "2/12 - 1/12", "6/12 - 4/12"], a: 0},
                        {q: "Hasil dari 7/8 - 1/4 adalah...", opts: ["5/8", "6/4", "6/8", "3/8"], a: 0},
                        {q: "Dalam sebotol air terdapat 3/4 liter air. Diminum 1/8 liter. Sisanya...", opts: ["2/8 liter", "5/8 liter", "2/4 liter", "4/8 liter"], a: 1},
                        {q: "Mana yang hasil pengurangannya salah?", opts: ["1/2 - 1/4 = 1/4", "3/4 - 1/2 = 1/4", "2/3 - 1/3 = 1/3", "4/5 - 1/2 = 2/10"], a: 3}
                    ]
                },
                3: { // Level 3: Pecahan Campuran & Soal Cerita Lanjut
                    materi: `<p><b>Pecahan Campuran & Soal Cerita</b></p><p>Pecahan campuran terdiri dari bilangan bulat dan pecahan, contoh: <b>1 1/2</b>.</p><p>Cara mengurangi: Kurangkan bilangan bulatnya dulu, lalu pecahannya (samakan penyebut jika perlu). Atau ubah semua jadi pecahan biasa.</p><p>Contoh: <b>2 3/4 - 1 1/4 = (2-1) + (3/4 - 1/4) = 1 2/4 = 1 1/2</b>.</p>`,
                    dnd: [
                        {q: "2 1/2 - 1", a: "1 1/2"}, {q: "3 3/4 - 1 1/4", a: "2 2/4"}, {q: "1 1/2 - 1/2", a: "1"}, {q: "4 2/3 - 2", a: "2 2/3"}, {q: "5 4/5 - 3 1/5", a: "2 3/5"}
                    ],
                    tf: [
                        {q: "1 1/2 sama nilainya dengan 3/2", a: true}, {q: "2 1/4 - 1 1/4 = 2", a: false, fb: "Hasilnya adalah 1."},
                        {q: "Pecahan biasa dari 2 1/3 adalah 7/3", a: true}, {q: "3 1/2 - 1/2 = 3", a: true},
                        {q: "Budi punya 2 buah apel utuh. Dimakan 1/2, sisa apel budi 1 1/2.", a: true}
                    ],
                    fitb: [
                        {q: "Bentuk pecahan biasa dari 1 2/5 adalah ... / 5", a: "7"},
                        {q: "2 1/4 - 1 1/4 = ...", a: "1"},
                        {q: "Hasil dari 3 - 1/2 adalah 2 1/...", a: "2"},
                        {q: "Paman punya tanah 3 1/2 hektar. Dijual 1 1/2 hektar. Sisa tanah paman ... hektar", a: "2"},
                        {q: "4 3/4 - 2 1/4 = 2 .../4", a: "2"}
                    ],
                    mc: [
                        {q: "Hasil dari 2 1/2 - 1 1/2 adalah...", opts: ["1", "2", "1/2", "0"], a: 0},
                        {q: "Bentuk pecahan biasa dari 3 1/4 adalah...", opts: ["13/4", "7/4", "12/4", "4/3"], a: 0},
                        {q: "Ibu memiliki beras 4 1/2 kg. Dimasak 1 1/2 kg. Sisa beras ibu...", opts: ["2 kg", "3 kg", "4 kg", "2 1/2 kg"], a: 1},
                        {q: "Hasil pengurangan 5 - 1/4 adalah...", opts: ["4 1/4", "4 3/4", "5 3/4", "4"], a: 1},
                        {q: "Ayah mengecat tembok sepanjang 5 3/4 meter. Paman mengecat 2 1/4 meter. Selisih panjangnya adalah...", opts: ["3 1/4 m", "3 2/4 m", "2 3/4 m", "4 1/4 m"], a: 1},
                        {q: "Pak Ali panen 7 1/2 ton jagung. Sebanyak 4 1/2 ton dijual. Sisanya...", opts: ["2 ton", "3 ton", "4 ton", "3 1/2 ton"], a: 1},
                        {q: "Hasil dari 6 2/3 - 4 1/3 adalah...", opts: ["2 1/3", "2", "3 1/3", "1 2/3"], a: 0},
                        {q: "Sebuah bambu panjangnya 4 meter. Dipotong 1 1/2 meter. Sisa bambu...", opts: ["3 1/2 m", "2 1/2 m", "2 m", "3 m"], a: 1},
                        {q: "Dari pecahan campuran 2 4/5, bilangan bulatnya adalah...", opts: ["4", "5", "2", "8"], a: 2},
                        {q: "Tepung 5 1/4 kg dipakai 2 kg. Sisanya...", opts: ["3 1/4 kg", "4 1/4 kg", "2 1/4 kg", "3 kg"], a: 0}
                    ]
                }
            },
            panca: {
                1: { // Level 1: Norma Keluarga & Sekolah
                    materi: `<p><b>Norma di Keluarga dan Sekolah</b></p><p>Norma adalah aturan yang mengatur tingkah laku kita. Ada 4 macam norma: Agama, Kesusilaan, Kesopanan, dan Hukum.</p><p>Di rumah, kita wajib patuh pada orang tua (Norma Kesopanan). Di sekolah, kita wajib mengikuti tata tertib, memakai seragam, dan menghormati guru.</p><p>Manfaat patuh norma: Hidup rukun, damai, dan tertib.</p>`,
                    dnd: [
                        {q: "Menghormati Guru", a: "Norma Sekolah"}, {q: "Berpamitan pada Orang Tua", a: "Norma Keluarga"},
                        {q: "Beribadah tepat waktu", a: "Norma Agama"}, {q: "Tidak menyontek", a: "Norma Kesusilaan"},
                        {q: "Membuang sampah pada tempatnya", a: "Aturan Lingkungan"}
                    ],
                    tf: [
                        {q: "Mengejek teman yang berbeda agama melanggar norma.", a: true},
                        {q: "Berbicara dengan suara tinggi kepada guru adalah tindakan yang sesuai norma.", a: false, fb: "Kita harus berbicara sopan kepada guru."},
                        {q: "Merapikan tempat tidur sendiri adalah contoh kepatuhan di rumah.", a: true},
                        {q: "Norma dibuat untuk menyusahkan warga masyarakat.", a: false, fb: "Norma dibuat agar hidup tertib dan rukun."},
                        {q: "Menyontek saat ujian berarti melanggar norma kesusilaan (kejujuran).", a: true}
                    ],
                    fitb: [
                        {q: "Aturan yang berasal dari ajaran Tuhan disebut norma ...", a: "agama"},
                        {q: "Menghormati orang tua adalah penerapan norma di lingkungan ...", a: "keluarga"},
                        {q: "Siswa yang mematuhi tata tertib memakai ... yang rapi ke sekolah.", a: "seragam"},
                        {q: "Suara hati nurani manusia yang membedakan baik dan buruk adalah norma ...", a: "kesusilaan"},
                        {q: "Berbicara santun kepada orang yang lebih tua adalah contoh norma ...", a: "kesopanan"}
                    ],
                    mc: [
                        {q: "Manfaat utama mematuhi norma di sekolah adalah...", opts: ["Mendapat uang jajan lebih", "Terciptanya suasana belajar yang tertib dan nyaman", "Bisa pulang lebih cepat", "Menjadi siswa paling terkenal"], a: 1},
                        {q: "Contoh pelaksanaan norma kesopanan di lingkungan keluarga adalah...", opts: ["Menonton TV sampai larut malam", "Minta izin sebelum keluar rumah", "Mengabaikan panggilan ibu", "Makan sambil berjalan"], a: 1},
                        {q: "Saat temanmu sedang beribadah, sikapmu sebaiknya...", opts: ["Menghidupkan musik keras", "Memanggilnya bermain", "Menjaga ketenangan", "Ikut campur urusan agamanya"], a: 2},
                        {q: "Menjaga kebersihan kelas adalah tanggung jawab...", opts: ["Hanya ketua kelas", "Hanya petugas kebersihan", "Seluruh warga kelas", "Guru saja"], a: 2},
                        {q: "Sumber dari norma hukum adalah...", opts: ["Kebiasaan masyarakat", "Hati nurani", "Wahyu Tuhan", "Peraturan dari negara/pemerintah"], a: 3},
                        {q: "Sikap tidak menyontek saat ulangan merupakan perwujudan kepatuhan terhadap norma...", opts: ["Kesusilaan", "Hukum", "Kesopanan", "Keluarga"], a: 0},
                        {q: "Sanksi jika melanggar norma kesopanan biasanya berupa...", opts: ["Denda uang", "Ditegur atau dikucilkan masyarakat", "Dipenjara", "Dosa dari Tuhan"], a: 1},
                        {q: "Mengucapkan salam saat masuk rumah teman adalah penerapan norma...", opts: ["Hukum", "Kesopanan", "Agama", "Kesusilaan"], a: 1},
                        {q: "Aturan di keluarga biasanya dibuat oleh...", opts: ["Polisi", "Kepala Sekolah", "Orang Tua", "Ketua RT"], a: 2},
                        {q: "Jika kamu melihat teman membuang sampah sembarangan di sekolah, tindakanmu yang sesuai norma adalah...", opts: ["Ikut membuang sembarangan", "Memarahinya dengan kasar", "Melaporkan ke polisi", "Menegurnya dengan sopan"], a: 3}
                    ]
                },
                2: { // Level 2: Hak dan Kewajiban
                    materi: `<p><b>Hak dan Kewajiban secara Adil</b></p><p><b>Hak</b> adalah sesuatu yang mutlak menjadi milik kita. <b>Kewajiban</b> adalah sesuatu yang harus kita lakukan dengan penuh tanggung jawab.</p><p>Hak dan kewajiban harus berjalan seimbang. Kita tidak boleh hanya menuntut hak tanpa melaksanakan kewajiban.</p><p>Contoh di sekolah: Hak (Mendapat pelajaran), Kewajiban (Memperhatikan penjelasan guru).</p>`,
                    dnd: [
                        {q: "Mendapat kasih sayang", a: "Hak Anak"}, {q: "Membantu orang tua", a: "Kewajiban Anak"},
                        {q: "Mendapat pelajaran", a: "Hak Siswa"}, {q: "Mengerjakan PR", a: "Kewajiban Siswa"},
                        {q: "Fasilitas umum bersih", a: "Hak Warga"}
                    ],
                    tf: [
                        {q: "Kita boleh menuntut hak walaupun belum melaksanakan kewajiban.", a: false, fb: "Kewajiban harus dilaksanakan terlebih dahulu atau seimbang dengan hak."},
                        {q: "Mendapatkan nilai bagus adalah hak, rajin belajar adalah kewajiban.", a: true},
                        {q: "Piket kebersihan kelas adalah hak setiap siswa.", a: false, fb: "Piket kebersihan adalah kewajiban siswa."},
                        {q: "Setiap warga negara memiliki hak yang sama di mata hukum.", a: true},
                        {q: "Menjaga ketenangan di perpustakaan adalah bentuk kewajiban.", a: true}
                    ],
                    fitb: [
                        {q: "Sesuatu yang harus kita lakukan disebut ...", a: "kewajiban"},
                        {q: "Sesuatu yang semestinya kita terima disebut ...", a: "hak"},
                        {q: "Pelaksanaan hak dan kewajiban harus berjalan secara ...", a: "seimbang"},
                        {q: "Mendapat kasih sayang dari ibu dan ayah adalah contoh hak di ...", a: "rumah"},
                        {q: "Mendengarkan guru yang sedang menjelaskan pelajaran adalah contoh ... siswa", a: "kewajiban"}
                    ],
                    mc: [
                        {q: "Mengapa hak dan kewajiban harus seimbang?", opts: ["Agar bisa pamer", "Agar tercipta keadilan dan ketertiban", "Karena disuruh guru", "Supaya dihukum"], a: 1},
                        {q: "Berikut ini yang merupakan HAK anak di rumah adalah...", opts: ["Merapikan mainan", "Mendapat makanan bergizi", "Mencuci baju sendiri", "Menyapu halaman"], a: 1},
                        {q: "Tindakan yang menunjukkan pelaksanaan kewajiban di sekolah adalah...", opts: ["Bermain saat jam pelajaran", "Menggunakan fasilitas sekolah semaunya", "Memakai seragam sesuai hari yang ditentukan", "Minta uang jajan ke guru"], a: 2},
                        {q: "Bila kita sudah mengerjakan ulangan dengan jujur, maka hak kita adalah...", opts: ["Mendapat hadiah uang", "Mendapatkan nilai dari guru", "Boleh bolos sekolah", "Mengejek teman yang nilainya jelek"], a: 1},
                        {q: "Contoh hak warga negara dalam UUD 1945 secara sederhana untuk anak adalah...", opts: ["Hak untuk merusak", "Hak mendapat pendidikan", "Hak untuk korupsi", "Hak melanggar lampu merah"], a: 1},
                        {q: "Beni ingin lingkungan rumahnya bersih (Hak). Kewajiban Beni adalah...", opts: ["Menyuruh adik membersihkan", "Membuang sampah pada tempatnya", "Menunggu tukang sampah datang", "Menyapu rumah tetangga"], a: 1},
                        {q: "Jika temanmu sedang berbicara memberikan pendapat, kewajibanmu adalah...", opts: ["Memotong pembicaraannya", "Meninggalkannya", "Mendengarkan dengan baik", "Menertawakannya"], a: 2},
                        {q: "Menggunakan jalan raya adalah hak, kewajibannya adalah...", opts: ["Mematuhi rambu lalu lintas", "Ngebut di jalan", "Parkir sembarangan", "Berjalan di tengah jalan raya"], a: 0},
                        {q: "Sikap adil dalam keluarga contohnya...", opts: ["Membela adik walaupun salah", "Membagi tugas rumah secara merata", "Kakak mendapat porsi makan lebih banyak", "Hanya ibu yang bekerja keras"], a: 1},
                        {q: "Tanggung jawab siswa setelah meminjam buku perpustakaan adalah...", opts: ["Menyobek halamannya", "Menjualnya", "Mengembalikannya tepat waktu", "Membiarkannya hilang"], a: 2}
                    ]
                },
                3: { // Level 3: Keadilan & Akibat Melanggar Norma
                    materi: `<p><b>Penerapan Keadilan dan Sanksi Melanggar Norma</b></p><p>Keadilan berarti memberikan hak kepada yang berhak dan tidak membeda-bedakan orang secara tidak adil. Keadilan harus diterapkan di mana saja.</p><p>Melanggar norma akan membawa akibat buruk, yang disebut <b>sanksi</b>. Sanksi bisa berupa teguran, hukuman, denda, atau perasaan bersalah.</p><p>Tanggung jawab: Berani menanggung risiko atas perbuatan yang dilakukan.</p>`,
                    dnd: [
                        {q: "Mencuri barang teman", a: "Melanggar Hukum"}, {q: "Berbohong", a: "Melanggar Kesusilaan"},
                        {q: "Membagi kelompok adil", a: "Sikap Adil"}, {q: "Ditegur guru/masyarakat", a: "Sanksi Sosial"},
                        {q: "Meminta maaf jika salah", a: "Tanggung Jawab"}
                    ],
                    tf: [
                        {q: "Bersikap adil berarti semua orang harus mendapat barang yang persis sama bentuknya.", a: false, fb: "Adil berarti sesuai porsi/kebutuhan dan haknya, tidak selalu harus bentuknya sama."},
                        {q: "Orang yang melanggar hukum negara bisa mendapat sanksi penjara.", a: true},
                        {q: "Tanggung jawab berarti berani menerima akibat dari kesalahan sendiri.", a: true},
                        {q: "Membeda-bedakan teman karena beda suku adalah contoh keadilan.", a: false, fb: "Itu adalah contoh ketidakadilan dan diskriminasi."},
                        {q: "Sanksi norma kesusilaan biasanya berupa perasaan menyesal.", a: true}
                    ],
                    fitb: [
                        {q: "Berani mengakui kesalahan dan meminta maaf adalah contoh sikap tanggung ...", a: "jawab"},
                        {q: "Sikap tidak memihak dan memberikan hak sesuai porsinya disebut ...", a: "adil"},
                        {q: "Hukuman atau akibat yang diterima karena melanggar aturan disebut ...", a: "sanksi"},
                        {q: "Norma yang sanksinya paling tegas karena diatur oleh negara adalah norma ...", a: "hukum"},
                        {q: "Diskriminasi atau membeda-bedakan teman adalah perilaku yang tidak ...", a: "adil"}
                    ],
                    mc: [
                        {q: "Seorang ketua kelas membagi tugas piket secara merata. Ini adalah contoh...", opts: ["Ketidakadilan", "Sikap otoriter", "Sikap adil", "Hukuman"], a: 2},
                        {q: "Sanksi bagi pelanggar norma hukum (misalnya mencuri) dijatuhkan oleh...", opts: ["Ketua RT", "Hakim di Pengadilan", "Orang Tua", "Guru BK"], a: 1},
                        {q: "Siti memecahkan vas bunga sekolah. Sikap tanggung jawab Siti adalah...", opts: ["Sembunyi", "Menyalahkan orang lain", "Mengakui dan meminta maaf kepada guru", "Membuang pecahan vas"], a: 2},
                        {q: "Jika tidak ada norma dalam masyarakat, maka akan terjadi...", opts: ["Ketertiban", "Kekacauan", "Kedamaian", "Keadilan"], a: 1},
                        {q: "Sikap adil terhadap adik di rumah ditunjukkan dengan cara...", opts: ["Merebut mainan adik", "Menemani adik bermain dengan sabar", "Memarahi adik terus", "Menyuruh adik bekerja"], a: 1},
                        {q: "Pak Polisi menilang pengendara motor yang menerobos lampu merah. Ini wujud penegakan norma...", opts: ["Kesopanan", "Kesusilaan", "Agama", "Hukum"], a: 3},
                        {q: "Pancasila sila ke-5 berbunyi Keadilan Sosial. Ini mengajarkan kita untuk...", opts: ["Bersikap semena-mena", "Menghargai hak orang lain", "Mementingkan diri sendiri", "Menolak aturan hukum"], a: 1},
                        {q: "Kamu melihat temanmu dikucilkan karena miskin. Sikapmu yang tepat adalah...", opts: ["Ikut mengucilkan", "Mengajaknya bermain bersama", "Menonton saja", "Melaporkan ke polisi"], a: 1},
                        {q: "Manfaat bertindak adil kepada teman adalah...", opts: ["Dibenci teman", "Mendapat banyak musuh", "Disayangi dan dihormati teman", "Mendapat nilai turun"], a: 2},
                        {q: "Sanksi dari pelanggaran norma kesopanan adalah...", opts: ["Dipenjara", "Didenda jutaan", "Dicemooh atau dikucilkan masyarakat", "Merasakan dosa"], a: 2}
                    ]
                }
            }
        };

        function startGame() {
            const nameInput = document.getElementById('playerName').value.trim();
            const classInput = document.getElementById('playerClass').value;
            if(!nameInput || !classInput) {
                showModal('Data Belum Lengkap', 'Silakan isi nama dan pilih kelasmu dulu ya!');
                return;
            }
            state.name = nameInput;
            state.cls = classInput;
            
            document.getElementById('ui-playerName').innerText = state.name;
            document.getElementById('ui-playerClass').innerText = state.cls;
            updateMapLocks();
            nav('mapScreen');
        }

        function confirmRestart() {
            showModal('Yakin Keluar?', 'Progres belum tersimpan permanen. Reload halaman untuk mereset.');
        }

        function updateMapLocks() {
            // Math
            for(let i=1; i<=3; i++) {
                let node = document.getElementById(`node-math-${i}`);
                if(i <= state.progress.math) { node.classList.remove('locked'); } 
                else { node.classList.add('locked'); }
            }
            // Pancasila
            for(let i=1; i<=3; i++) {
                let node = document.getElementById(`node-panca-${i}`);
                if(i <= state.progress.panca) { node.classList.remove('locked'); } 
                else { node.classList.add('locked'); }
            }

            // Check Certificate Unlock
            if(state.progress.math > 3 && state.progress.panca > 3) {
                document.getElementById('btn-cert-unlock').classList.remove('hidden');
                document.getElementById('cert-locked-msg').classList.add('hidden');
            }
        }

        function enterLevel(subject, level) {
            if(level > state.progress[subject]) {
                showModal('Level Terkunci!', 'Selesaikan level sebelumnya dulu ya!');
                return;
            }
            state.currentPlay = { subject, level, ansDnD: {}, ansTF: {}, ansFITB: {}, ansMC: {}, mcIndex: 0 };
            
            const subjName = subject === 'math' ? 'Matematika' : 'Pendidikan Pancasila';
            document.getElementById('level-title-display').innerText = `${subjName} - Level ${level}`;
            
            // Render Materi
            const data = db[subject][level];
            document.getElementById('materi-content').innerHTML = data.materi;

            // Render DnD
            renderDnD(data.dnd);
            
            // Render TF
            renderTF(data.tf);

            // Render FITB
            renderFITB(data.fitb);

            // Hide all parts, show A
            document.querySelectorAll('.level-part').forEach(p => p.classList.add('hidden'));
            document.getElementById('part-A').classList.remove('hidden');
            document.getElementById('level-progress-bar').style.width = '20%';

            nav('levelScreen');
        }

        function nextPart(partStr) {
            sfx.click();
            document.querySelectorAll('.level-part').forEach(p => p.classList.add('hidden'));
            document.getElementById(`part-${partStr}`).classList.remove('hidden');
            
            let progMap = { 'A': 20, 'B': 40, 'C': 60, 'D': 80, 'E': 95 };
            document.getElementById('level-progress-bar').style.width = progMap[partStr] + '%';

            if(partStr === 'E') renderMCQ();
        }

        let dndSelectedSource = null;
        function renderDnD(dndData) {
            const tContainer = document.getElementById('dnd-targets');
            const sContainer = document.getElementById('dnd-sources');
            tContainer.innerHTML = ''; sContainer.innerHTML = '';
            document.getElementById('dnd-feedback').classList.add('hidden');
            document.getElementById('dnd-next-btn').classList.add('hidden');
            dndSelectedSource = null;

            // Shuffle sources
            let sources = dndData.map((d, i) => ({text: d.a, id: i}));
            sources.sort(() => Math.random() - 0.5);

            dndData.forEach((d, i) => {
                // Target
                tContainer.innerHTML += `
                    <div class="flex items-center gap-4">
                        <div class="w-1/2 bg-blue-100 p-3 rounded-xl font-bold text-gray-800 text-center">${d.q}</div>
                        <div class="w-1/2 match-target rounded-xl" id="dnd-t-${i}" onclick="selectDnDTarget(${i})">
                            <span class="text-gray-400 text-sm italic">Tap kotak ini untuk mengisi</span>
                        </div>
                    </div>`;
            });

            sources.forEach((s) => {
                sContainer.innerHTML += `
                    <div class="match-card p-3 rounded-xl text-center font-bold text-gray-800 shadow-sm" id="dnd-s-${s.id}" onclick="selectDnDSource(${s.id}, '${s.text}')">${s.text}</div>
                `;
            });
        }

        function selectDnDSource(id, text) {
            if(document.getElementById(`dnd-s-${id}`).classList.contains('hidden')) return; // Already used
            sfx.click();
            document.querySelectorAll('.match-card').forEach(c => c.classList.remove('selected'));
            document.getElementById(`dnd-s-${id}`).classList.add('selected');
            dndSelectedSource = {id, text};
        }

        function selectDnDTarget(targetId) {
            if(!dndSelectedSource) return;
            sfx.click();
            const targetEl = document.getElementById(`dnd-t-${targetId}`);
            
            // If target already has answer, return it to pool
            if(state.currentPlay.ansDnD[targetId] !== undefined) {
                const oldSourceId = state.currentPlay.ansDnD[targetId].sourceId;
                document.getElementById(`dnd-s-${oldSourceId}`).classList.remove('hidden');
            }

            // Put new answer in target
            targetEl.innerHTML = `<span class="font-bold text-gray-800">${dndSelectedSource.text}</span>`;
            targetEl.classList.add('filled');
            
            // Hide source from pool
            document.getElementById(`dnd-s-${dndSelectedSource.id}`).classList.add('hidden');
            document.getElementById(`dnd-s-${dndSelectedSource.id}`).classList.remove('selected');
            
            // Save state
            state.currentPlay.ansDnD[targetId] = { sourceId: dndSelectedSource.id, text: dndSelectedSource.text };
            dndSelectedSource = null;
        }

        function checkDnD() {
            const data = db[state.currentPlay.subject][state.currentPlay.level].dnd;
            if(Object.keys(state.currentPlay.ansDnD).length < data.length) {
                showModal('Belum Lengkap!', 'Pasangkan semua kartu sebelum memeriksa.');
                return;
            }
            sfx.correct(); // Just a sound, scoring happens at end
            document.getElementById('dnd-feedback').innerText = "Berhasil dipasangkan! Lanjut ke bagian berikutnya.";
            document.getElementById('dnd-feedback').classList.remove('hidden');
            document.getElementById('dnd-feedback').className = "font-bold text-lg text-green-600";
            document.getElementById('dnd-next-btn').classList.remove('hidden');
        }

        function renderTF(tfData) {
            const container = document.getElementById('tf-questions');
            container.innerHTML = '';
            document.getElementById('tf-feedback').classList.add('hidden');
            document.getElementById('tf-next-btn').classList.add('hidden');

            tfData.forEach((d, i) => {
                container.innerHTML += `
                    <div class="bg-gray-50 p-4 rounded-xl border-2 border-gray-200">
                        <p class="font-bold text-gray-800 mb-3">${i+1}. ${d.q}</p>
                        <div class="flex gap-4">
                            <button id="btn-tf-${i}-true" onclick="selectTF(${i}, true)" class="flex-1 py-2 rounded-lg border-2 border-gray-300 font-bold hover:bg-green-100 transition">SESUAI</button>
                            <button id="btn-tf-${i}-false" onclick="selectTF(${i}, false)" class="flex-1 py-2 rounded-lg border-2 border-gray-300 font-bold hover:bg-red-100 transition">TIDAK SESUAI</button>
                        </div>
                    </div>
                `;
            });
        }
        function selectTF(qIndex, answer) {
            sfx.click();
            state.currentPlay.ansTF[qIndex] = answer;
            document.getElementById(`btn-tf-${qIndex}-true`).classList.remove('bg-green-500', 'text-white', 'border-green-600');
            document.getElementById(`btn-tf-${qIndex}-false`).classList.remove('bg-red-500', 'text-white', 'border-red-600');
            
            if(answer) {
                document.getElementById(`btn-tf-${qIndex}-true`).classList.add('bg-green-500', 'text-white', 'border-green-600');
            } else {
                document.getElementById(`btn-tf-${qIndex}-false`).classList.add('bg-red-500', 'text-white', 'border-red-600');
            }
        }
        function checkTF() {
            const data = db[state.currentPlay.subject][state.currentPlay.level].tf;
            if(Object.keys(state.currentPlay.ansTF).length < data.length) {
                showModal('Belum Lengkap!', 'Pilih jawaban untuk semua pernyataan.');
                return;
            }
            sfx.correct();
            document.getElementById('tf-feedback').innerText = "Tersimpan! Lanjut ke bagian berikutnya.";
            document.getElementById('tf-feedback').classList.remove('hidden');
            document.getElementById('tf-feedback').className = "font-bold text-lg text-purple-600";
            document.getElementById('tf-next-btn').classList.remove('hidden');
        }

        function renderFITB(fitbData) {
            const container = document.getElementById('fitb-questions');
            container.innerHTML = '';
            document.getElementById('fitb-feedback').classList.add('hidden');
            document.getElementById('fitb-next-btn').classList.add('hidden');

            fitbData.forEach((d, i) => {
                container.innerHTML += `
                    <div class="flex items-center gap-4 bg-orange-50 p-3 rounded-xl border border-orange-100">
                        <div class="font-bold w-8 flex-shrink-0 text-orange-600">${i+1}.</div>
                        <div class="flex-grow font-semibold text-gray-700">${d.q}</div>
                        <input type="text" id="fitb-ans-${i}" class="w-32 p-2 border-2 border-orange-200 rounded-lg text-center font-bold focus:outline-none focus:border-orange-500" autocomplete="off" onchange="saveFITB(${i})">
                    </div>
                `;
            });
        }
        function saveFITB(i) { state.currentPlay.ansFITB[i] = document.getElementById(`fitb-ans-${i}`).value.trim().toLowerCase(); }
        function resetFITB() {
            sfx.click();
            const data = db[state.currentPlay.subject][state.currentPlay.level].fitb;
            for(let i=0; i<data.length; i++) {
                document.getElementById(`fitb-ans-${i}`).value = '';
                delete state.currentPlay.ansFITB[i];
            }
        }
        function checkFITB() {
            const data = db[state.currentPlay.subject][state.currentPlay.level].fitb;
            let allFilled = true;
            for(let i=0; i<data.length; i++) {
                saveFITB(i); // force save
                if(!state.currentPlay.ansFITB[i]) allFilled = false;
            }
            if(!allFilled) {
                showModal('Belum Lengkap!', 'Isi semua kotak jawaban singkat ya.');
                return;
            }
            sfx.correct();
            document.getElementById('fitb-feedback').innerText = "Tersimpan! Lanjut Uji Kompetensi.";
            document.getElementById('fitb-feedback').classList.remove('hidden');
            document.getElementById('fitb-feedback').className = "font-bold text-lg text-orange-600";
            document.getElementById('fitb-next-btn').classList.remove('hidden');
        }

        function renderMCQ() {
            const idx = state.currentPlay.mcIndex;
            const data = db[state.currentPlay.subject][state.currentPlay.level].mc;
            const q = data[idx];
            
            document.getElementById('mc-current-num').innerText = idx + 1;
            document.getElementById('mc-q-text').innerText = q.q;
            
            const optContainer = document.getElementById('mc-options');
            optContainer.innerHTML = '';
            
            q.opts.forEach((optText, i) => {
                const isSelected = state.currentPlay.ansMC[idx] === i;
                const baseClass = "w-full text-left p-4 rounded-xl border-2 font-bold transition-all";
                const unselClass = "bg-white border-gray-200 hover:border-blue-300 text-gray-700";
                const selClass = "bg-blue-100 border-blue-500 text-blue-800 scale-[1.02]";
                
                optContainer.innerHTML += `
                    <button onclick="selectMC(${idx}, ${i})" class="${baseClass} ${isSelected ? selClass : unselClass}">
                        <span class="inline-block w-8">${String.fromCharCode(65+i)}.</span> ${optText}
                    </button>
                `;
            });

            document.getElementById('btn-mc-prev').disabled = (idx === 0);
            
            if (idx === data.length - 1) {
                document.getElementById('btn-mc-next').classList.add('hidden');
                document.getElementById('btn-mc-finish').classList.remove('hidden');
            } else {
                document.getElementById('btn-mc-next').classList.remove('hidden');
                document.getElementById('btn-mc-finish').classList.add('hidden');
            }
        }

        function selectMC(qIdx, optIdx) {
            sfx.click();
            state.currentPlay.ansMC[qIdx] = optIdx;
            renderMCQ();
        }

        function nextMC() { sfx.click(); state.currentPlay.mcIndex++; renderMCQ(); }
        function prevMC() { sfx.click(); state.currentPlay.mcIndex--; renderMCQ(); }

        function finishLevel() {
            const data = db[state.currentPlay.subject][state.currentPlay.level];
            if(Object.keys(state.currentPlay.ansMC).length < data.mc.length) {
                showModal('Belum Lengkap!', 'Ada soal Uji Kompetensi yang belum dijawab. Cek kembali.');
                return;
            }

            // Calculate Scores
            // 1. DnD
            let dndCorrect = 0;
            data.dnd.forEach((d, i) => {
                if(state.currentPlay.ansDnD[i] && state.currentPlay.ansDnD[i].text === d.a) dndCorrect++;
            });
            let scoreDnD = (dndCorrect / data.dnd.length) * 100;

            // 2. TF
            let tfCorrect = 0;
            data.tf.forEach((d, i) => {
                if(state.currentPlay.ansTF[i] === d.a) tfCorrect++;
            });
            let scoreTF = (tfCorrect / data.tf.length) * 100;

            // 3. FITB
            let fitbCorrect = 0;
            data.fitb.forEach((d, i) => {
                if(state.currentPlay.ansFITB[i] == d.a.toLowerCase()) fitbCorrect++;
            });
            let scoreFITB = (fitbCorrect / data.fitb.length) * 100;

            // 4. MC
            let mcCorrect = 0;
            data.mc.forEach((d, i) => {
                if(state.currentPlay.ansMC[i] === d.a) mcCorrect++;
            });
            let scoreMC = (mcCorrect / data.mc.length) * 100;

            // Final Average
            let finalScore = (scoreDnD + scoreTF + scoreFITB + scoreMC) / 4;
            
            // Save Score
            state.scores[state.currentPlay.subject][state.currentPlay.level] = finalScore;

            // Update Progress if passed (We'll allow progression regardless, but can restrict)
            if(state.progress[state.currentPlay.subject] === state.currentPlay.level && state.currentPlay.level < 3) {
                state.progress[state.currentPlay.subject]++;
            }

            // Predikat
            let predikat = ""; let fbMsg = "";
            if(finalScore >= 90) { predikat = "SANG JUARA"; fbMsg = "Hebat! Pertahankan semangat belajarmu!"; sfx.levelUp(); }
            else if(finalScore >= 80) { predikat = "HEBAT"; fbMsg = "Luar biasa, kamu memahami sebagian besar konsepnya!"; sfx.levelUp(); }
            else if(finalScore >= 70) { predikat = "TERUS BERKEMBANG"; fbMsg = "Bagus! Yuk tingkatkan lagi telitinya."; sfx.correct(); }
            else { predikat = "TETAP SEMANGAT"; fbMsg = "Belum maksimal, tidak apa-apa. Kesalahan adalah bagian dari belajar. Ayo berlatih lagi!"; sfx.wrong(); }

            // Display Results
            const subjName = state.currentPlay.subject === 'math' ? 'Matematika' : 'Pendidikan Pancasila';
            document.getElementById('res-subtitle').innerText = `${subjName} - Level ${state.currentPlay.level}`;
            document.getElementById('res-score-display').innerText = Math.round(finalScore);
            document.getElementById('res-predikat').innerText = predikat;
            document.getElementById('res-feedback-msg').innerText = `"${fbMsg}"`;

            document.getElementById('res-s-dnd').innerText = Math.round(scoreDnD);
            document.getElementById('res-s-tf').innerText = Math.round(scoreTF);
            document.getElementById('res-s-fitb').innerText = Math.round(scoreFITB);
            document.getElementById('res-s-mc').innerText = Math.round(scoreMC);

            document.getElementById('level-progress-bar').style.width = '100%';
            
            updateMapLocks();
            nav('resultScreen');
        }

        function viewCertificate() {
            // Calculate Final Scores
            let mathAvg = (state.scores.math[1] + state.scores.math[2] + state.scores.math[3]) / 3;
            let pancaAvg = (state.scores.panca[1] + state.scores.panca[2] + state.scores.panca[3]) / 3;

            document.getElementById('cert-name').innerText = state.name.toUpperCase();
            document.getElementById('cert-class').innerText = `Kelas ${state.cls} — SDN Parapat 1 Kota Tangerang`;
            
            document.getElementById('cert-score-math').innerText = Math.round(mathAvg);
            document.getElementById('cert-score-panca').innerText = Math.round(pancaAvg);
            
            const today = new Date();
            const dateStr = today.toLocaleDateString('id-ID', { day: 'numeric', month: 'long', year: 'numeric' });
            document.getElementById('cert-date').innerText = dateStr;

            sfx.levelUp();
            nav('certificateScreen');
        }

    </script>
</body>
</html># MathPancasila-kelas5
