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
            filter: drop-shadow(0 4px 6px rgba(255,105,180,0.2));
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
            transition: transform 0.75s cubic-bezier(0.34,1.2,0.55,1);
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
        .cat-ears.right-ear { left: auto; right: 10px; }
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
        .whisker-left { left: -22px; }
        .whisker-right { right: -22px; }
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
            transition: opacity 0.3s ease, transform 0.5s cubic-bezier(0.34,1.2,0.64,1);
            pointer-events: none;
        }
        .box-wrapper.open .box-content {
            opacity: 1;
            transform: scale(1);
            transition-delay: 0.2s;
        }
        .message-card {
            background: rgba(255,245,250,0.95);
            backdrop-filter: blur(3px);
            padding: 16px 25px;
            border-radius: 68px;
            box-shadow: 0 12px 24px rgba(245,85,125,0.3), inset 0 1px 2px white;
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
            transform: translate(-50%,-50%) rotate(45deg);
            z-index: 5;
            border-radius: 6px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.1);
            pointer-events: none;
        }
        .ribbon-cross:before, .ribbon-cross:after {
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
        <div class="box-lid" id="boxLid"><div class="lid-ribbon"></div></div>
        <div class="cat-on-lid">
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
        <div class="box-content">
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
(function(){
    const wrapper = document.getElementById('giftBoxWrapper');
    const lid = document.getElementById('boxLid');
    const hint = document.getElementById('hintText');
    let isOpened = false;
    function createFlower(){
        const f = ['🌸','🌷','🌺','🌼','🌸','🌹','💮','🏵️','🌻','💖'];
        const div = document.createElement('div');
        div.className = 'floating-flower';
        div.innerHTML = f[Math.floor(Math.random()*f.length)];
        div.style.fontSize = (Math.random()*20+18)+'px';
        div.style.left = Math.random()*100+'%';
        const dur = Math.random()*8+6;
        div.style.animationDuration = dur+'s';
        div.style.animationDelay = Math.random()*5+'s';
        document.getElementById('flowerOverlay').appendChild(div);
        setTimeout(()=>div.remove(), dur*1000);
    }
    for(let i=0;i<20;i++) setTimeout(()=>createFlower(), i*300);
    setInterval(()=>createFlower(), 1000);
    function burst(x,y){
        for(let i=0;i<15;i++){
            const p = document.createElement('div');
            p.innerHTML = Math.random()>0.5?'🌸':'🌼';
            p.style.position = 'fixed';
            p.style.left = (x+(Math.random()-0.5)*80)+'px';
            p.style.top = (y+(Math.random()-0.5)*70)+'px';
            p.style.fontSize = (Math.random()*18+12)+'px';
            p.style.pointerEvents = 'none';
            p.style.zIndex = '999';
            p.style.opacity = '0.9';
            p.style.transition = 'all 0.8s ease-out';
            document.body.appendChild(p);
            requestAnimationFrame(()=>{
                p.style.transform = `translateY(${Math.random()*-120-40}px) translateX(${(Math.random()-0.5)*100}px) rotate(${Math.random()*200}deg)`;
                p.style.opacity = '0';
            });
            setTimeout(()=>p.remove(),900);
        }
    }
    function hearts(){
        for(let i=0;i<50;i++){
            const h = document.createElement('div');
            h.className = 'floating-heart';
            h.innerHTML = ['❤️','💖','💗','🌸','💕','💘','💝','🐱','🌷','💓'][Math.floor(Math.random()*10)];
            h.style.left = Math.random()*window.innerWidth+'px';
            h.style.bottom = '20px';
            h.style.fontSize = (Math.random()*24+18)+'px';
            document.body.appendChild(h);
            setTimeout(()=>h.remove(),1500);
        }
    }
    function openBox(){
        if(isOpened) return;
        isOpened = true;
        wrapper.classList.add('open');
        hint.innerHTML = '💞 Soukaina! You opened it! I love you so much sister! 💞';
        hint.style.background = '#ffecf0';
        hint.style.color = '#d43f6b';
        hearts();
        const rect = wrapper.getBoundingClientRect();
        burst(rect.left+rect.width/2, rect.top+rect.height/2);
        setTimeout(()=>{
            for(let i=0;i<35;i++){
                const ef = document.createElement('div');
                ef.innerHTML = ['🌸','🌷','🌼','🌺','💖','🌹','💗'][Math.floor(Math.random()*7)];
                ef.style.position = 'fixed';
                ef.style.left = Math.random()*window.innerWidth+'px';
                ef.style.top = '-30px';
                ef.style.fontSize = (Math.random()*20+15)+'px';
                ef.style.pointerEvents = 'none';
                ef.style.zIndex = '100';
                ef.style.animation = 'floatFlower 1.6s linear forwards';
                ef.style.animationDuration = Math.random()*1.8+1.2+'s';
                document.body.appendChild(ef);
                setTimeout(()=>ef.remove(),2000);
            }
        },200);
    }
    wrapper.addEventListener('click', openBox);
    lid.addEventListener('click', openBox);
    let loveInterval = null;
    const observer = new MutationObserver(()=>{
        if(wrapper.classList.contains('open') && !loveInterval){
            loveInterval = setInterval(()=>{
                if(isOpened && Math.random()<0.6){
                    for(let i=0;i<8;i++){
                        const hi = document.createElement('div');
                        hi.innerHTML = ['💖','💗','🌸','💕','🌷'][Math.floor(Math.random()*5)];
                        hi.style.position = 'fixed';
                        hi.style.left = Math.random()*window.innerWidth+'px';
                        hi.style.bottom = '10px';
                        hi.style.fontSize = '24px';
                        hi.style.pointerEvents = 'none';
                        hi.style.zIndex = '99';
                        hi.style.animation = 'heartFloatUp 1.2s ease-out forwards';
                        document.body.appendChild(hi);
                        setTimeout(()=>hi.remove(),1200);
                    }
                }
            },5000);
        }
    });
    observer.observe(wrapper, {attributes:true});
    setTimeout(()=>{for(let i=0;i<40;i++) createFlower();},600);
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
