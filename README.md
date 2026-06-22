<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Binomo AI Signal Predictor</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background-color: #0b0e14; color: #fff; overflow-x: hidden; display: flex; flex-direction: column; align-items: center; padding: 20px; }
        
        /* Header */
        h1 { color: #ff9900; margin-bottom: 20px; font-size: 24px; text-shadow: 0 0 10px rgba(255, 153, 0, 0.5); }

        /* Notification Settings */
        #notification-box { position: fixed; top: -100px; left: 50%; transform: translateX(-50%); background: rgba(255, 153, 0, 0.9); color: black; padding: 15px 30px; border-radius: 30px; font-weight: bold; font-size: 16px; transition: top 0.5s ease; z-index: 1000; box-shadow: 0 5px 15px rgba(255,153,0,0.4); }

        /* Robot Container */
        .robot-container { display: flex; flex-direction: column; align-items: center; margin-bottom: 20px; }
        .robot { width: 80px; height: 80px; background: linear-gradient(45deg, #1e2633, #ff5500); border-radius: 50%; display: flex; justify-content: center; align-items: center; border: 3px solid #ff9900; box-shadow: 0 0 20px #ff5500; animation: float 3s ease-in-out infinite; }
        .robot-eyes { width: 40px; height: 10px; display: flex; justify-content: space-around; }
        .eye { width: 12px; height: 12px; background-color: #00ffff; border-radius: 50%; box-shadow: 0 0 10px #00ffff; }
        .robot-text { margin-top: 10px; font-size: 14px; color: #00ffff; font-weight: bold; }

        /* Controls */
        .controls { margin-bottom: 15px; display: flex; gap: 10px; }
        select { background: #1e2633; color: white; border: 1px solid #ff9900; padding: 8px 15px; border-radius: 5px; outline: none; }

        /* Chart Container */
        .chart-wrapper { width: 100%; max-width: 600px; height: 300px; background: #121824; border: 2px solid #232d3f; border-radius: 10px; relative; overflow: hidden; display: flex; align-items: flex-end; padding: 20px; gap: 8px; position: relative; }
        .candle { flex: 1; border-radius: 2px; transition: height 0.5s ease; position: relative; }
        .candle.green { background: #00e676; box-shadow: 0 0 10px rgba(0,230,118,0.5); }
        .candle.red { background: #ff1744; box-shadow: 0 0 10px rgba(255,23,68,0.5); }

        /* Fire Scanner Overlay */
        .scanner { position: absolute; top: 0; left: 0; width: 100%; height: 5px; background: linear-gradient(to bottom, #ff1744, #ff5500, transparent); box-shadow: 0 0 20px #ff5500, 0 0 30px #ff1744; display: none; z-index: 5; }
        @keyframes scan {
            0% { top: 0; }
            50% { top: 100%; }
            100% { top: 0; }
        }

        /* Action Button */
        .btn-container { margin-top: 25px; width: 100%; max-width: 600px; display: flex; justify-content: center; }
        .signal-btn { background: linear-gradient(90deg, #ff5500, #ff9900); color: white; border: none; padding: 15px 40px; font-size: 18px; font-weight: bold; border-radius: 30px; cursor: pointer; box-shadow: 0 0 15px rgba(255,85,0,0.4); transition: 0.3s; width: 80%; }
        .signal-btn:hover { transform: scale(1.05); box-shadow: 0 0 25px rgba(255,85,0,0.8); }

        /* Signal Display */
        #result-display { margin-top: 20px; font-size: 24px; font-weight: bold; text-transform: uppercase; }
        .up-signal { color: #00e676; text-shadow: 0 0 15px #00e676; }
        .down-signal { color: #ff1744; text-shadow: 0 0 15px #ff1744; }

        /* Animations */
        @keyframes float {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }
    </style>
</head>
<body>

    <div id="notification-box">নতুন সিগন্যাল রেডি! দ্রুত এন্ট্রি নিন।</div>

    <h1>BINOMO AI ROBOT SIGNAL V1</h1>

    <div class="robot-container">
        <div class="robot">
            <div class="robot-eyes">
                <div class="eye"></div>
                <div class="eye"></div>
            </div>
        </div>
        <div id="robot-status" class="robot-text">আমি রোবট, সিগন্যাল দিতে প্রস্তুত!</div>
    </div>

    <div class="controls">
        <select id="market-select">
            <option value="crypto">Crypto IDX (1 Min)</option>
            <option value="eurusd">EUR/USD (1 Min)</option>
            <option value="gbpusd">GBP/USD (1 Min)</option>
        </select>
    </div>

    <div class="chart-wrapper">
        <div class="scanner" id="fire-scanner"></div>
        <div id="chart-candles" style="display:flex; width:100%; height:100%; align-items:flex-end; gap:8px;"></div>
    </div>

    <div class="btn-container">
        <button class="signal-btn" onclick="generateSignal()">GET SIGNAL</button>
    </div>

    <div id="result-display"></div>

    <script>
        // Generate Live Fake Candles for Binomo Look
        const chartCandles = document.getElementById('chart-candles');
        function createChart() {
            chartCandles.innerHTML = '';
            for(let i=0; i<15; i++) {
                let height = Math.floor(Math.random() * 80) + 10;
                let type = Math.random() > 0.5 ? 'green' : 'red';
                let candle = document.createElement('div');
                candle.className = `candle ${type}`;
                candle.style.height = `${height}%`;
                chartCandles.appendChild(candle);
            }
        }
        createChart();
        setInterval(createChart, 3000); // Chart updates every 3 seconds

        // Voice Notification System (Bangla)
        function speakBangla(text) {
            if ('speechSynthesis' in window) {
                let speech = new SpeechSynthesisUtterance();
                speech.text = text;
                speech.lang = 'bn-BD'; // Bangladesh Bangla Accent
                speech.rate = 0.9; 
                window.speechSynthesis.speak(speech);
            }
        }

        // Auto-dismiss Notification function
        function showNotification(text) {
            let box = document.getElementById('notification-box');
            box.innerText = text;
            box.style.top = '20px';
            setTimeout(() => {
                box.style.top = '-100px'; // Automatically slides away
            }, 4000);
        }

        // Signal Logic with Fire Scan Animation
        function generateSignal() {
            let scanner = document.getElementById('fire-scanner');
            let robotStatus = document.getElementById('robot-status');
            let resultDisplay = document.getElementById('result-display');
            
            // Start Fire Scanning Animation
            scanner.style.display = 'block';
            scanner.style.animation = 'scan 1.5s linear infinite';
            robotStatus.innerText = "বাজার বিশ্লেষণ করা হচ্ছে...";
            resultDisplay.innerText = "";
            
            speakBangla("বাজার বিশ্লেষণ করা হচ্ছে, একটু অপেক্ষা করুন।");

            setTimeout(() => {
                // Stop Scanner
                scanner.style.display = 'none';
                scanner.style.animation = '';

                // Random Signal Generator (Algorithm Simulation)
                let isUp = Math.random() > 0.5;
                if(isUp) {
                    robotStatus.innerText = "বিশ্লেষণ সম্পন্ন!";
                    resultDisplay.innerHTML = "<span class='up-signal'>▲ CALL (UP) - 1 MIN</span>";
                    showNotification("সিগন্যাল: আপ ট্রেন্ড! ১ মিনিটের জন্য উপরে ট্রেড করুন।");
                    speakBangla("সিগন্যাল পেয়েছি। এক মিনিটের জন্য উপরে ট্রেড করুন।");
                } else {
                    robotStatus.innerText = "বিশ্লেষণ সম্পন্ন!";
                    resultDisplay.innerHTML = "<span class='down-signal'>▼ PUT (DOWN) - 1 MIN</span>";
                    showNotification("সিগন্যাল: ডাউন ট্রেন্ড! ১ মিনিটের জন্য নিচে ট্রেড করুন।");
                    speakBangla("সিগন্যাল পেয়েছি। এক মিনিটের জন্য নিচে ট্রেড করুন।");
                }

            }, 4000); // 4 Seconds Scanning time
        }
    </script>
</body>
</html>
