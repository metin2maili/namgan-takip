<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <title>Namgan Farm Takip Pro</title>
    <style>
        body { 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
            background-color: #121212; 
            color: #fff; 
            margin: 8px; 
            padding: 0;
        }
        h1 { 
            font-size: 16px; 
            text-align: center; 
            color: #ff9900; 
            margin: 6px 0;
            text-shadow: 1px 1px #000; 
        }
        .status-box {
            background-color: #770000;
            text-align: center;
            padding: 8px;
            border-radius: 4px;
            font-size: 11px;
            font-weight: bold;
            margin-bottom: 8px;
            cursor: pointer;
            border: 1px solid #ff3333;
            transition: 0.3s;
        }
        .grid-container { 
            display: flex;
            flex-direction: column;
            gap: 6px; 
            max-width: 320px; 
            margin: 0 auto;
        }
        .ch-box { 
            background-color: #1e1e1e; 
            border: 1px solid #444; 
            border-radius: 6px; 
            padding: 6px 10px; 
        }
        .ch-title { 
            font-size: 13px; 
            font-weight: bold; 
            color: #ff9900; 
            margin-bottom: 4px;
            border-bottom: 1px solid #444;
            padding-bottom: 2px;
            text-align: center;
        }
        .timer-row { 
            display: flex; 
            justify-content: space-between; 
            align-items: center; 
            margin-bottom: 4px; 
            background: #262626; 
            padding: 4px 8px; 
            border-radius: 4px; 
            transition: background 0.2s; 
        }
        .timer-label { font-size: 12px; font-weight: 550; } 
        .timer-display { 
            font-family: monospace; 
            font-size: 14px; 
            color: #00ff00; 
            font-weight: bold; 
            margin-left: auto;
            margin-right: 10px;
        }
        .btn { 
            background-color: #ff9900; 
            color: #000; 
            border: none; 
            padding: 4px 8px; 
            font-size: 11px;
            font-weight: bold; 
            border-radius: 3px; 
            cursor: pointer; 
        }
        .btn:hover { background-color: #cc7a00; }
        
        /* Gelişmiş Ses Menü Tasarımı */
        .sound-menu-box {
            background-color: #1e1e1e;
            border: 1px solid #ff9900;
            max-width: 300px;
            margin: 6px auto;
            padding: 8px;
            border-radius: 6px;
            font-size: 12px;
        }
        .menu-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 6px;
        }
        .menu-row:last-child { margin-bottom: 0; }
        select {
            background: #333;
            color: #fff;
            border: 1px solid #555;
            font-size: 11px;
            padding: 3px;
            border-radius: 4px;
            width: 140px;
            cursor: pointer;
        }
        .btn-test {
            background-color: #28a745;
            color: white;
            font-size: 10px;
            padding: 3px 6px;
            border: none;
            border-radius: 3px;
            cursor: pointer;
            font-weight: bold;
            margin-left: 5px;
        }
        .btn-test:hover { background-color: #218838; }
        
        @keyframes alarm-flash {
            0% { background-color: #262626; }
            50% { background-color: #770000; }
            100% { background-color: #262626; }
        }
        .alarm-active { animation: alarm-flash 0.8s infinite; }
        .alarm-active .timer-display { color: #ff3333; }
    </style>
</head>
<body>

    <h1>⚔️ Namgan Farm Takip Pro</h1>
    
    <!-- 🚨 SESİ AKTİF ETME ŞERİDİ -->
    <div class="status-box" id="auth-panel" onclick="unlockAudioPro()">
        🚨 SES KİLİDİNİ AÇMAK İÇİN BURAYA TIKLA!
    </div>
    
    <!-- 🔊 SENİN SEÇTİĞİN 10 MUZİKLİ MENÜ -->
    <div class="sound-menu-box">
        <div class="menu-row">
            <span>💎 Metin Sesi:</span>
            <select id="select-metin">
                <option value="sarimsak">Taşköprü Sarımsak</option>
                <option value="ezikuzi">Ezi Kuzi Ciye Te</option>
                <option value="ramiz">Ramiz Dayı Alarm</option>
                <option value="aykut">Aykut Elmas Geliyor</option>
                <option value="indian">Indian Song</option>
                <option value="oguz">Oğuz Uyan Oğuz</option>
                <option value="kemal">Ben Kemal Geliyorum</option>
                <option value="undertaker">The Undertaker Bell</option>
                <option value="takipci">Takipçi Sesiaaaaa</option>
                <option value="cinsellik">Yürüyen Cinsellik</option>
            </select>
            <button class="btn-test" onclick="previewSoundPro('metin')">🔊 Test</button>
        </div>
        <div class="menu-row">
            <span>👹 Boss Sesi:</span>
            <select id="select-boss">
                <option value="undertaker">The Undertaker Bell</option>
                <option value="sarimsak">Taşköprü Sarımsak</option>
                <option value="ezikuzi">Ezi Kuzi Ciye Te</option>
                <option value="ramiz">Ramiz Dayı Alarm</option>
                <option value="aykut">Aykut Elmas Geliyor</option>
                <option value="indian">Indian Song</option>
                <option value="oguz">Oğuz Uyan Oğuz</option>
                <option value="kemal">Ben Kemal Geliyorum</option>
                <option value="takipci">Takipçi Sesiaaaaa</option>
                <option value="cinsellik">Yürüyen Cinsellik</option>
            </select>
            <button class="btn-test" onclick="previewSoundPro('boss')">🔊 Test</button>
        </div>
    </div>
    
    <div class="grid-container" id="chContainer"></div>

    <script>
        const totalCH = 6;
        const container = document.getElementById('chContainer');
        let intervals = {};
        let audioCtxPro = null;
        
        const soundUrls = {
            sarimsak: "https://myinstants.com",
            ezikuzi: "https://myinstants.com",
            ramiz: "https://myinstants.com",
            aykut: "https://myinstants.com",
            indian: "https://myinstants.com",
            oguz: "https://myinstants.com",
            kemal: "https://myinstants.com",
            undertaker: "https://myinstants.com",
            takipci: "https://myinstants.com",
            cinsellik: "https://myinstants.com"
        };

        // Ses motoru kilidini açan ana fonksiyon
        function unlockAudioPro() {
            try {
                if (!audioCtxPro) {
                    audioCtxPro = new (window.AudioContext || window.webkitAudioContext)();
                }
                if (audioCtxPro.state === 'suspended') {
                    audioCtxPro.resume();
                }
                
                const panel = document.getElementById("auth-panel");
                panel.innerText = "✅ SES MOTORU AKTİF! KİLİT AÇILDI.";
                panel.style.backgroundColor = "#28a745";
                panel.style.borderColor = "#218838";
                
                // İlk kilidi açınca ufak bir bildirim sesi verir
                let osc = audioCtxPro.createOscillator();
                let gain = audioCtxPro.createGain();
                osc.frequency.setValueAtTime(523.25, audioCtxPro.currentTime);
                gain.gain.setValueAtTime(0.1, audioCtxPro.currentTime);
                osc.connect(gain);
                gain.connect(audioCtxPro.destination);
                osc.start();
                osc.stop(audioCtxPro.currentTime + 0.1);
            } catch (e) {
                console.log(e);
            }
        }

        function previewSoundPro(type) {
            unlockAudioPro();
            const selected = document.getElementById(`select-${type}`).value;
            playCustomSoundPro(selected);
        }

        function playCustomSoundPro(soundKey) {
            const url = soundUrls[soundKey];
            if(url) {
                const audio = new Audio(url);
                audio.volume = 0.6;
                audio.play().catch(e => {
                    console.log("Ses çalınamadı, lütfen önce üstteki kırmızı kutuya tıklayın.");
                });
            }
        }

        for (let i = 1; i <= totalCH; i++) {
            const chDiv = document.createElement('div');
            chDiv.className = 'ch-box';
            chDiv.innerHTML = `
                <div class="ch-title">CH ${i}</div>
                <div class="timer-row" id="row-metin-${i}">
                    <span class="timer-label">💎 Metin:</span>
                    <span class="timer-display" id="metin-time-${i}">30:00</span>
                    <button class="btn" onclick="startTimer(${i}, 'metin', 1800)">OK</button>
                </div>
                <div class="timer-row" id="row-boss-${i}">
                    <span class="timer-label">👹 Boss:</span>
                    <span class="timer-display" id="boss-time-${i}">60:00</span>
                    <button class="btn" onclick="startTimer(${i}, 'boss', 3600)">OK</button>
                </div>
            `;
            container.appendChild(chDiv);
        }

