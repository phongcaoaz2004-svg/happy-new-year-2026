<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Happy New Year 2026 - Final Fix</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary-color: #00f2ff;
            --secondary-color: #ff0078;
        }

        body {
            margin: 0;
            background: #000;
            color: #fff;
            font-family: 'Poppins', sans-serif;
            overflow: hidden;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }

        #canvas { position: absolute; top: 0; left: 0; z-index: 0; }

        #start-btn {
            position: absolute;
            z-index: 100;
            padding: 20px 40px;
            font-size: 1.8rem;
            font-weight: bold;
            background: transparent;
            border: 2px solid var(--primary-color);
            color: var(--primary-color);
            border-radius: 10px;
            cursor: pointer;
            text-transform: uppercase;
            letter-spacing: 5px;
            transition: 0.5s;
            box-shadow: 0 0 15px var(--primary-color);
        }
        #start-btn:hover {
            background: var(--primary-color);
            color: #000;
            box-shadow: 0 0 50px var(--primary-color);
        }

        .container {
            display: none;
            position: relative;
            z-index: 1;
            text-align: center;
            background: rgba(0, 0, 0, 0.6);
            padding: 60px;
            border-radius: 30px;
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        h2 { font-size: 2.5em; letter-spacing: 8px; margin-bottom: 30px; color: var(--primary-color); }
        .countdown { display: flex; gap: 40px; }
        .countdown div { display: flex; flex-direction: column; align-items: center; }
        .countdown span { font-size: 5em; font-weight: 900; filter: drop-shadow(0 0 15px var(--primary-color)); }
        .countdown b { font-size: 1rem; color: var(--primary-color); margin-top: 10px; }

        #happy-new-year {
            display: none;
            font-size: 6em;
            font-weight: 900;
            text-shadow: 0 0 20px #fff, 0 0 40px var(--secondary-color);
            animation: celebrate 1s infinite alternate;
        }

        @keyframes celebrate { from { transform: scale(1); } to { transform: scale(1.1); } }
    </style>
</head>
<body>

    <button id="start-btn">MỞ QUÀ 2026</button>

    <audio id="bg-music" loop>
        <source src="Happy New Year.mp3" type="audio/mpeg">
    </audio>
    
    <audio id="firework-sfx">
        <source src="https://assets.mixkit.co/active_storage/sfx/2867/2867-preview.mp3" type="audio/mpeg">
    </audio>

    <canvas id="canvas"></canvas>

    <div class="container" id="main-ui">
        <h2 id="title">COUNTDOWN TO 2026</h2>
        <div class="countdown" id="timer">
            <div><span id="days">00</span><b>DAYS</b></div>
            <div><span id="hours">00</span><b>HOURS</b></div>
            <div><span id="minutes">00</span><b>MINS</b></div>
            <div><span id="seconds">00</span><b>SECS</b></div>
        </div>
        <div id="happy-new-year">HAPPY NEW YEAR 2026</div>
    </div>

    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        const startBtn = document.getElementById('start-btn');
        const mainUI = document.getElementById('main-ui');
        const bgMusic = document.getElementById('bg-music');
        const fireworkSfx = document.getElementById('firework-sfx');
        
        let particles = [];
        let stars = [];
        let isNewYear = false;

        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        // Tạo sao nền
        for(let i=0; i<200; i++) {
            stars.push({ x: Math.random() * canvas.width, y: Math.random() * canvas.height, size: Math.random() * 2, opacity: Math.random() });
        }

        // --- THAO TÁC KHI NHẤN NÚT ---
        startBtn.addEventListener('click', () => {
            startBtn.style.display = 'none';
            mainUI.style.display = 'block';
            
            // Phát nhạc
            bgMusic.play().catch(e => {
                alert("Lưu ý: Bạn cần kéo file 'Happy New Year.mp3' vào thư mục project trong VS Code!");
            });

            animate();
        });

        class Particle {
            constructor(x, y, color, velocityX, velocityY) {
                this.x = x; this.y = y; this.color = color;
                this.velocity = { x: velocityX, y: velocityY };
                this.alpha = 1; this.friction = 0.96;
            }
            draw() {
                ctx.save();
                ctx.globalAlpha = this.alpha;
                ctx.beginPath();
                ctx.arc(this.x, this.y, 2.5, 0, Math.PI * 2);
                ctx.fillStyle = this.color;
                ctx.fill();
                ctx.restore();
            }
            update() {
                this.velocity.x *= this.friction; this.velocity.y *= this.friction;
                this.velocity.y += 0.07; this.x += this.velocity.x; this.y += this.velocity.y;
                this.alpha -= 0.012;
            }
        }

        function createFirework(x, y) {
            const sfx = fireworkSfx.cloneNode();
            sfx.volume = 0.5;
            sfx.play();
            const colors = ['#00f2ff', '#ff0078', '#ffd700', '#ffffff', '#00ff00'];
            const color = colors[Math.floor(Math.random() * colors.length)];
            for (let i = 0; i < 80; i++) {
                const angle = (Math.PI * 2 / 80) * i;
                const speed = Math.random() * 8 + 2;
                particles.push(new Particle(x, y, color, Math.cos(angle) * speed, Math.sin(angle) * speed));
            }
        }

        const targetTime = new Date("Jan 1, 2026 00:00:00").getTime();
        setInterval(() => {
            const now = new Date().getTime();
            const diff = targetTime - now;
            if (diff <= 0) {
                if(!isNewYear) {
                    isNewYear = true;
                    document.getElementById('timer').style.display = 'none';
                    document.getElementById('title').style.display = 'none';
                    document.getElementById('happy-new-year').style.display = 'block';
                }
                return;
            }
            document.getElementById('days').innerText = Math.floor(diff / (1000 * 60 * 60 * 24)).toString().padStart(2, '0');
            document.getElementById('hours').innerText = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60)).toString().padStart(2, '0');
            document.getElementById('minutes').innerText = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60)).toString().padStart(2, '0');
            document.getElementById('seconds').innerText = Math.floor((diff % (1000 * 60)) / 1000).toString().padStart(2, '0');
        }, 1000);

        function animate() {
            requestAnimationFrame(animate);
            ctx.fillStyle = 'rgba(0, 0, 0, 0.15)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            stars.forEach(s => {
                ctx.fillStyle = `rgba(255, 255, 255, ${s.opacity})`;
                ctx.fillRect(s.x, s.y, s.size, s.size);
            });
            particles.forEach((p, i) => {
                if (p.alpha > 0) { p.update(); p.draw(); }
                else { particles.splice(i, 1); }
            });
            if (isNewYear && Math.random() < 0.1) {
                createFirework(Math.random() * canvas.width, Math.random() * canvas.height * 0.4);
            }
        }

        window.addEventListener('mousedown', (e) => createFirework(e.clientX, e.clientY));
    </script>
</body>
</html>
