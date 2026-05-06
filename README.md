
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Fashion Editor - Senior Dev Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background: #0f172a; color: #f8fafc; }
        .glass { background: rgba(30, 41, 59, 0.7); backdrop-filter: blur(12px); border: 1px solid rgba(255,255,255,0.1); }
        .result-box { border: 2px dashed #334155; min-height: 300px; display: flex; align-items: center; justify-content: center; overflow: hidden; }
        img { max-width: 100%; height: auto; border-radius: 12px; }
    </style>
</head>
<body class="p-4 md:p-10">

    <div class="max-w-5xl mx-auto">
        <div class="text-center mb-8">
            <h1 class="text-3xl font-bold bg-gradient-to-r from-blue-400 to-emerald-400 bg-clip-text text-transparent">
                AI FASHION EDITOR
            </h1>
            <p class="text-gray-400 text-sm mt-2">Berekspresi Tanpa Batas / <span class="italic">Express Without Limits</span></p>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
            <div class="glass p-6 rounded-3xl shadow-xl">
                <div class="space-y-6">
                    <div>
                        <label class="block text-sm font-medium mb-2 uppercase tracking-wider text-blue-400">1. Unggah Foto / Upload Photo</label>
                        <input type="file" id="imgInp" accept="image/*" class="block w-full text-sm text-slate-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-blue-600 file:text-white hover:file:bg-blue-500 cursor-pointer">
                    </div>

                    <div>
                        <label class="block text-sm font-medium mb-2 uppercase tracking-wider text-blue-400">2. Instruksi Gaya / Style Prompt</label>
                        <textarea id="prompt" rows="3" class="w-full bg-slate-800 border border-slate-700 rounded-xl p-4 focus:ring-2 focus:ring-blue-500 outline-none placeholder:text-slate-600" placeholder="Contoh: Ubah kaos saya menjadi kemeja batik sutra warna emas..."></textarea>
                    </div>

                    <button onclick="prosesAI()" class="w-full bg-gradient-to-r from-blue-600 to-blue-700 hover:from-blue-500 hover:to-blue-600 text-white font-bold py-4 rounded-xl shadow-lg transition-all active:scale-95">
                        PROSES SEKARANG / PROCESS NOW
                    </button>
                </div>
            </div>

            <div class="glass p-6 rounded-3xl shadow-xl">
                <h2 class="text-sm font-medium mb-4 uppercase tracking-wider text-emerald-400 italic">Preview & Result</h2>
                
                <div id="outputContainer" class="result-box rounded-2xl relative">
                    <div id="placeholder" class="text-slate-500 text-center p-10">
                        <p>Gambar akan muncul di sini</p>
                        <p class="text-xs italic">Image will appear here</p>
                    </div>
                    <img id="mainImg" src="" class="hidden">
                    
                    <div id="loader" class="hidden absolute inset-0 bg-slate-900 bg-opacity-80 flex flex-col items-center justify-center">
                        <div class="animate-spin rounded-full h-12 w-12 border-t-2 border-blue-500 mb-4"></div>
                        <p class="text-sm animate-pulse">Menghubungkan ke Gemini AI...</p>
                    </div>
                </div>

                <div id="extraTools" class="hidden mt-6 grid grid-cols-2 gap-4">
                    <button onclick="getPalette()" class="bg-slate-700 hover:bg-slate-600 py-2 rounded-lg text-xs font-bold uppercase">Palette Warna</button>
                    <button onclick="getTips()" class="bg-slate-700 hover:bg-slate-600 py-2 rounded-lg text-xs font-bold uppercase">Saran Gaya</button>
                </div>
                
                <div id="paletteArea" class="flex gap-2 mt-4 justify-center"></div>
                <div id="tipsArea" class="mt-4 text-sm text-center text-slate-300 italic"></div>
            </div>
        </div>
    </div>

    <script>
        const imgInp = document.getElementById('imgInp');
        const mainImg = document.getElementById('mainImg');
        const loader = document.getElementById('loader');
        const placeholder = document.getElementById('placeholder');
        const extraTools = document.getElementById('extraTools');

        // RESIZER & PREVIEW
        imgInp.onchange = evt => {
            const [file] = imgInp.files;
            if (file) {
                const reader = new FileReader();
                reader.onload = e => {
                    const img = new Image();
                    img.onload = () => {
                        const canvas = document.createElement('canvas');
                        const MAX_WIDTH = 1000;
                        let scale = MAX_WIDTH / img.width;
                        canvas.width = MAX_WIDTH;
                        canvas.height = img.height * scale;
                        
                        const ctx = canvas.getContext('2d');
                        ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
                        
                        mainImg.src = canvas.toDataURL('image/jpeg', 0.8);
                        mainImg.classList.remove('hidden');
                        placeholder.classList.add('hidden');
                        extraTools.classList.add('hidden');
                        document.getElementById('paletteArea').innerHTML = '';
                        document.getElementById('tipsArea').innerText = '';
                    };
                    img.src = e.target.result;
                }
                reader.readAsDataURL(file);
            }
        }

        // SIMULASI PROSES AI (Karena Gemini 2.5 butuh Server-Side Key)
        function prosesAI() {
            if(!imgInp.files[0]) return alert("Upload foto dulu!");
            
            loader.classList.remove('hidden');
            
            setTimeout(() => {
                loader.classList.add('hidden');
                extraTools.classList.remove('hidden');
                mainImg.style.filter = "saturate(1.2) brightness(1.05)"; // Simulasi edit
                alert("Simulasi: AI sedang memproses gaya baju baru berdasarkan prompt: " + document.getElementById('prompt').value);
            }, 2500);
        }

        // FUNGSI TAMBAHAN
        function getPalette() {
            const colors = ['#1e293b', '#3b82f6', '#10b981', '#f59e0b', '#ffffff'];
            const area = document.getElementById('paletteArea');
            area.innerHTML = '';
            colors.forEach(c => {
                const d = document.createElement('div');
                d.className = "w-10 h-10 rounded-full border-2 border-white shadow-lg";
                d.style.backgroundColor = c;
                area.appendChild(d);
            });
        }

        function getTips() {
            const tips = [
                "ID: Padukan dengan celana slim-fit gelap.\nEN: Pair with dark slim-fit trousers.",
                "ID: Gunakan aksesoris jam tangan silver.\nEN: Use silver watch accessories.",
                "ID: Warna ini cocok untuk acara semi-formal.\nEN: This color is perfect for semi-formal events."
            ];
            document.getElementById('tipsArea').innerText = tips[Math.floor(Math.random()*tips.length)];
        }
    </script>
</body>
</html>
