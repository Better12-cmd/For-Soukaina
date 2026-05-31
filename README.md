```python
import http.server
import socketserver

HTML = '''<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🌸 For Soukaina - Love You Sister 🌸</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
        }

        body {
            min-height: 100vh;
            background: linear-gradient(145deg, #ffe6f0 0%, #ffc0d4 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Segoe UI', 'Quicksand', system-ui, 'Comic Neue', cursive;
            overflow-x: hidden;
            position: relative;
            cursor: pointer;
        }

        .flower-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 10;
            overflow: hidden;
        }

        .floating-flower {
            position: absolute;
            font-size: 28px;
            opacity: 0.7;
            animation: floatFlower linear infinite;
            pointer-events: none;
            filter: drop-shadow(0 4px 6px rgba(255, 105, 180, 0.2));
        }

        @keyframes floatFlower {
            0% { transform: translateY(100vh) rotate(0deg); opacity: 0; }
            10% { opacity: 0.8; }
            90% { opacity: 0.8; }
            100% { transform: translateY(-20vh) rotate(360deg); opacity: 0; }
        }

        .stage {
            position: relative;
            z-index: 30;
            text-align: center;
            width: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .box-wrapper {
            position: relative;
            width: 360px;
            height: 300px;
            margin: 0 auto;
            cursor: pointer;
            filter: drop-shadow(0 20px 25px -8px rgba(0,0,0,0.2));
            transition: transform 0.2s ease;
        }

        .box-wrapper:active {
            transform: scale(0.98);
        }

        .gift-box {
            position: absolute;
            bottom: 0;
            left: 0;
            width: 100%;
            height: 180px;
            background: #ff99bb;
            border-radius: 28px 28px 24px 24px;
            box-shadow: 0 15px 0 #b34e6e, inset 0 1px 4px rgba(255,220,240,0.8);
            z-index: 2;
        }

        .box-lid {
            position: absolute;
            bottom: 160px;
            left: -8px;
            width: calc(100% + 16px);
            height: 70px;
            background: #ffb7cf;
            border-radius: 32px 32px 28px 28px;
            box-shadow: 0 6px 0 #9e4a65;
            transform-origin: bottom center;
            transition: transform 0.75s cubic-bezier(0.34, 1.2, 0.55, 1);
            z-index: 6;
            cursor: pointer;
        }

        .lid-ribbon {
            position: absolute;
            width: 20px;
            height: 30px;
            background: #f06292;
            left: 50%;
            transform: translateX(-50%);
            top: 18px;
            border-radius: 20px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.1);
        }

        .cat-on-lid {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            width: 100px;
            height: 80px;
            z-index: 12;
            pointer-events: none;
            transition: all 0.3s ease;
        }

        .cat-body {
            position: relative;
            width: 85px;
            height: 55px;
            background: #fff9f2;
            border-radius: 50% 50% 45% 45% / 60% 60% 40% 40%;
            box-shadow: 0 6px 10px rgba(0,0,0,0.05), inset 0 1px 3px white;
            margin: 0 auto;
        }

        .cat-ears {
            position: absolute;
            top: -20px;
            left: 10px;
            width: 0;
            height: 0;
            border-left: 15px solid transparent;
            border-right: 15px solid transparent;
            border-bottom: 22px solid #fff2e6;
            filter: drop-shadow(0 1px 1px rgba(0,0,0,0.1));
        }

        .cat-ears.right-ear {
            left: auto;
            right: 10px;
        }

        .cat-face {
            position: relative;
            display: flex;
            justify-content: center;
            gap: 16px;
            top: 18px;
        }

        .eye {
            width: 8px;
            height: 10px;
            background: #59473c;
            border-radius: 50%;
            position: relative;
        }

        .eye::after {
            content: "•";
            color: white;
            font-size: 6px;
            position: absolute;
            top: 1px;
            left: 1px;
        }

        .nose {
            width: 10px;
            height: 7px;
            background: #f7a1b5;
            border-radius: 50%;
            position: relative;
            top: 6px;
            left: 2px;
        }

        .whisker {
            position: absolute;
            width: 20px;
            height: 1px;
            background: #bba38e;
            top: 28px;
        }

        .whisker-left {
            left: -22px;
        }

        .whisker-right {
            right: -22px;
        }

        .tail {
            position: absolute;
            bottom: 8px;
            right: -18px;
            width: 25px;
            height: 12px;
            background: #fff0e0;
            border-radius: 30px;
            transform: rotate(25deg);
        }

        .box-wrapper.open .cat-on-lid {
            opacity: 0;
            transform: translateX(-50%) translateY(-40px) scale(0.8);
            transition: opacity 0.4s, transform 0.6s;
        }

        .box-wrapper.open .box-lid {
            transform: rotateX(-70deg) translateY(-10px);
        }

        .box-content {
            position: absolute;
            bottom: 20px;
            left: 0;
            width: 100%;
            height: 140px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 3;
            opacity: 0;
            transform: scale(0.7);
            transition: opacity 0.3s ease, transform 0.5s cubic-bezier(0.34, 1.2, 0.64, 1);
            pointer-events: none;
        }

        .box-wrapper.open .box-content {
            opacity: 1;
            transform: scale(1);
            transition-delay: 0.2s;
        }

        .message-card {
            background: rgba(255, 245, 250, 0.95);
            backdrop-filter: blur(3px);
            padding: 16px 25px;
            border-radius: 68px;
            box-shadow: 0 12px 24px rgba(245, 85, 125, 0.3), inset 0 1px 2px white;
            text-align: center;
            border: 1px solid #ffc0e0;
        }

        .heart-message {
            font-size: 2rem;
            font-weight: bold;
            color: #e84393;
            text-shadow: 2px 2px 10px #ffb7c5;
            letter-spacing: 2px;
        }

        .sub-message {
            font-size: 1.2rem;
            color: #ff6f91;
            font-weight: 600;
            margin-top: 5px;
        }

        .sorry-message {
            font-size: 0.9rem;
            color: #b84c6c;
            margin-top: 8px;
            font-style: italic;
        }

        .inner-cat-surprise {
            margin-top: 8px;
            display: flex;
            justify-content: center;
            gap: 4px;
            font-size: 32px;
            filter: drop-shadow(0 2px 5px pink);
        }

        .ribbon-cross {
            position: absolute;
            width: 28px;
            height: 28px;
            background: #f27c9e;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%) rotate(45deg);
            z-index: 5;
            border-radius: 6px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.1);
            pointer-events: none;
        }

        .ribbon-cross:after, .ribbon-cross:before {
            content: "";
            position: absolute;
            background: #f27c9e;
        }

        .ribbon-cross:before {
            width: 28px;
            height: 8px;
            top: 10px;
            left: 0;
        }

        .ribbon-cross:after {
            width: 8px;
            height: 28px;
            top: 0;
            left: 10px;
        }

        .click-hint {
            margin-top: 40px;
            font-size: 1rem;
            background: rgba(255,255,240,0.7);
            backdrop-filter: blur(6px);
            display: inline-block;
            padding: 8px 22px;
            border-radius: 60px;
            color: #b84c6c;
            font-weight: 600;
            box-shadow: 0 4px 12px rgba(255,105,150,0.2);
            pointer-events: none;
        }

        .floating-heart {
            position: fixed;
            font-size: 28px;
            pointer-events: none;
            z-index: 200;
            animation: heartFloatUp 1.8s ease-out forwards;
        }

        @keyframes heartFloatUp {
            0% { opacity: 1; transform: translateY(0) scale(0.5); }
            100% { opacity: 0; transform: translateY(-150px) scale(1.2); }
        }

        @media (max-width: 500px) {
            .box-wrapper { width: 280px; height: 260px; }
            .gift-box { height: 150px; }
            .box-lid { bottom: 130px; height: 60px; }
            .message-card { padding: 10px 18px; }
            .heart-message { font-size: 1.3rem; }
            .sub-message { font-size: 0.9rem; }
            .cat-on-lid .cat-body { width: 70px; height: 45px; }
        }
    </style>
</head>
<body>
<div class="flower-overlay" id="flowerOverlay"></div>
<div class="stage">
    <div class="box-wrapper" id="giftBoxWrapper">
        <div class="gift-box"></div>
        <div class="box-lid" id="boxLid">
            <div class="lid-ribbon"></div>
        </div>
        <div class="cat-on-lid" id="sleepingCat">
            <div class="cat-body">
                <div class="cat-ears"></div>
                <div class="cat-ears right-ear"></div>
                <div class="cat-face">
                    <div class="eye"></div>
                    <div class="nose"></div>
                    <div class="eye"></div>
                </div>
                <div class="whisker whisker-left"></div>
                <div class="whisker whisker-right"></div>
                <div class="tail"></div>
            </div>
        </div>
        <div class="ribbon-cross"></div>
        <div class="box-content" id="boxContent">
            <div class="message-card">
                <div class="heart-message">💖 I LOVE YOU SOUKAINA 💖</div>
                <div class="sub-message">You're the best sister ever!</div>
                <div class="sorry-message">🌸 So sorry I couldn't help with the VPN... but this is for you! 🌸</div>
                <div class="inner-cat-surprise">🐱💕✨🐾🌸</div>
            </div>
        </div>
    </div>
    <div class="click-hint" id="hintText">🌸 Click the box to open your surprise! 🌸</div>
</div>

<script>
(function() {
    const wrapper = document.getElementById('giftBoxWrapper');
    const lid = document.getElementById('boxLid');
    const hint = document.getElementById('hintText');
    let isOpened = false;
    
    function createFloatingFlower() {
        const flowers = ['🌸', '🌷', '🌺', '🌼', '🌸', '🌹', '💮', '🏵️', '🌻', '🌸', '🌷', '🌸', '💖', '🌺'];
        const randomFlower = flowers[Math.floor(Math.random() * flowers.length)];
        const flowerDiv = document.createElement('div');
        flowerDiv.className = 'floating-flower';
        flowerDiv.innerHTML = randomFlower;
        const size = Math.floor(Math.random() * 20 + 18);
        flowerDiv.style.fontSize = size + 'px';
        flowerDiv.style.left = Math.random() * 100 + '%';
        const duration = Math.random() * 8 + 6;
        flowerDiv.style.animationDuration = duration + 's';
        flowerDiv.style.animationDelay = Math.random() * 5 + 's';
        flowerDiv.style.opacity = Math.random() * 0.6 + 0.3;
        document.getElementById('flowerOverlay').appendChild(flowerDiv);
        setTimeout(() => { if(flowerDiv && flowerDiv.remove) flowerDiv.remove(); }, duration * 1000);
    }
    
    for(let i = 0; i < 20; i++) {
        setTimeout(() => createFloatingFlower(), i * 300);
    }
    setInterval(() => { createFloatingFlower(); }, 1000);
    
    function burstPetals(x, y) {
        for(let i = 0; i < 15; i++) {
            const petal = document.createElement('div');
            petal.innerHTML = Math.random() > 0.5 ? '🌸' : '🌼';
            petal.style.position = 'fixed';
            petal.style.left = (x + (Math.random() - 0.5) * 80) + 'px';
            petal.style.top = (y + (Math.random() - 0.5) * 70) + 'px';
            petal.style.fontSize = (Math.random() * 18 + 12) + 'px';
            petal.style.pointerEvents = 'none';
            petal.style.zIndex = '999';
            petal.style.opacity = '0.9';
            petal.style.transition = 'all 0.8s ease-out';
            document.body.appendChild(petal);
            requestAnimationFrame(() => {
                petal.style.transform = `translateY(${Math.random() * -120 - 40}px) translateX(${(Math.random() - 0.5) * 100}px) rotate(${Math.random() * 200}deg)`;
                petal.style.opacity = '0';
            });
            setTimeout(() => petal.remove(), 900);
        }
    }
    
    function heartExplosion() {
        for(let i = 0; i < 50; i++) {
            const heart = document.createElement('div');
            heart.className = 'floating-heart';
            heart.innerHTML = ['❤️', '💖', '💗', '🌸', '💕', '💘', '💝', '🐱', '🌷', '💓'][Math.floor(Math.random()*10)];
            heart.style.left = Math.random() * window.innerWidth + 'px';
            heart.style.bottom = '20px';
            heart.style.fontSize = (Math.random() * 24 + 18) + 'px';
            heart.style.animation = `heartFloatUp 1.4s ease-out forwards`;
            document.body.appendChild(heart);
            setTimeout(() => heart.remove(), 1500);
        }
    }
    
    function openBox(e) {
        if(isOpened) return;
        isOpened = true;
        wrapper.classList.add('open');
        hint.innerHTML = '💞 Soukaina! You opened it! I love you so much sister! 💞';
        hint.style.background = '#ffecf0';
        hint.style.color = '#d43f6b';
        heartExplosion();
        const rect = wrapper.getBoundingClientRect();
        const centerX = rect.left + rect.width / 2;
        const centerY = rect.top + rect.height / 2;
        burstPetals(centerX, centerY);
        
        setTimeout(() => {
            for(let i = 0; i < 35; i++) {
                const extraFlower = document.createElement('div');
                extraFlower.innerHTML = ['🌸','🌷','🌼','🌺','🌸','💖','🌹','💗'][Math.floor(Math.random()*8)];
                extraFlower.style.position = 'fixed';
                extraFlower.style.left = Math.random() * window.innerWidth + 'px';
                extraFlower.style.top = '-30px';
                extraFlower.style.fontSize = (Math.random() * 20 + 15) + 'px';
                extraFlower.style.pointerEvents = 'none';
                extraFlower.style.zIndex = '100';
                extraFlower.style.animation = `floatFlower 1.6s linear forwards`;
                extraFlower.style.animationDuration = Math.random() * 1.8 + 1.2 + 's';
                document.body.appendChild(extraFlower);
                setTimeout(() => extraFlower.remove(), 2000);
            }
        }, 200);
        
        setTimeout(() => {
            const msg = document.querySelector('.sorry-message');
            if(msg) {
                msg.style.animation = 'heartFloatUp 0.5s ease-out';
                setTimeout(() => { if(msg) msg.style.animation = ''; }, 500);
            }
        }, 300);
    }
    
    wrapper.addEventListener('click', openBox);
    lid.addEventListener('click', openBox);
    
    let extraLoveInterval = null;
    function startPostOpenLove() {
        if(extraLoveInterval) clearInterval(extraLoveInterval);
        extraLoveInterval = setInterval(() => {
            if(isOpened && Math.random() < 0.6) {
                for(let i = 0; i < 8; i++) {
                    const heartItem = document.createElement('div');
                    heartItem.innerHTML = ['💖', '💗', '🌸', '💕', '🌷'][Math.floor(Math.random()*5)];
                    heartItem.style.position = 'fixed';
                    heartItem.style.left = Math.random() * window.innerWidth + 'px';
                    heartItem.style.bottom = '10px';
                    heartItem.style.fontSize = '24px';
                    heartItem.style.pointerEvents = 'none';
                    heartItem.style.zIndex = '99';
                    heartItem.style.animation = 'heartFloatUp 1.2s ease-out forwards';
                    document.body.appendChild(heartItem);
                    setTimeout(() => heartItem.remove(), 1200);
                }
            }
        }, 5000);
    }
    
    const observer = new MutationObserver((mutations) => {
        mutations.forEach((mut) => {
            if(mut.attributeName === 'class' && wrapper.classList.contains('open') && !extraLoveInterval) {
                startPostOpenLove();
            }
        });
    });
    observer.observe(wrapper, { attributes: true });
    
    setTimeout(() => {
        for(let i = 0; i < 40; i++) {
            createFloatingFlower();
        }
    }, 600);
})();
</script>
</body>
</html>'''

PORT = 8080

class Handler(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/html')
        self.end_headers()
        self.wfile.write(HTML.encode())

print("🌸 For Soukaina - Love You Sister 🌸")
print(f"Server running at http://localhost:{PORT}")
print("Press Ctrl+C to stop")
socketserver.TCPServer(("", PORT), Handler).serve_forever()
```
