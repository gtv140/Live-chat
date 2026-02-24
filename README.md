<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Connect | Ultra Modern</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Syncopate:wght@400;700&family=Outfit:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #00f2fe; --secondary: #4facfe; --accent: #7000ff;
            --bg: #03001e; --glass: rgba(255, 255, 255, 0.05);
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { 
            margin: 0; padding: 0; background: var(--bg); color: #fff;
            font-family: 'Outfit', sans-serif; height: 100vh; overflow: hidden;
            background: linear-gradient(45deg, #03001e, #7303c0, #03001e);
            background-size: 400% 400%; animation: gradient 15s ease infinite;
        }
        @keyframes gradient { 0% {background-position: 0% 50%;} 50% {background-position: 100% 50%;} 100% {background-position: 0% 50%;} }

        /* Login - Cinematic Entrance */
        #gatekeeper {
            position: fixed; inset: 0; z-index: 9999; background: #000;
            display: flex; flex-direction: column; align-items: center; justify-content: center; transition: 0.8s cubic-bezier(0.85, 0, 0.15, 1);
        }
        .portal { width: 120px; height: 120px; border: 2px solid var(--primary); border-radius: 50%; box-shadow: 0 0 50px var(--primary); display: flex; align-items: center; justify-content: center; margin-bottom: 30px; animation: pulse 2s infinite; }
        @keyframes pulse { 0% { transform: scale(1); opacity: 1; } 50% { transform: scale(1.1); opacity: 0.5; } 100% { transform: scale(1); opacity: 1; } }
        #gatekeeper input { background: none; border: none; border-bottom: 2px solid #333; color: #fff; font-size: 24px; text-align: center; padding: 10px; outline: none; transition: 0.3s; width: 250px; }
        #gatekeeper input:focus { border-color: var(--primary); }

        /* Main App Interface */
        .glass-shell { 
            height: 100vh; width: 100%; max-width: 450px; margin: 0 auto;
            backdrop-filter: blur(25px); background: var(--glass);
            display: flex; flex-direction: column; border: 1px solid rgba(255,255,255,0.1);
        }

        header { padding: 30px 20px; display: flex; justify-content: space-between; align-items: center; }
        header h1 { font-family: 'Syncopate', sans-serif; font-size: 16px; margin: 0; letter-spacing: 4px; color: var(--primary); text-shadow: 0 0 10px var(--primary); }

        .content { flex: 1; overflow-y: auto; padding: 15px; }

        /* Modern Status Circles */
        .status-bar { display: flex; gap: 15px; margin-bottom: 30px; overflow-x: auto; padding: 5px; scrollbar-width: none; }
        .status-node { min-width: 65px; height: 65px; border-radius: 50%; border: 2px solid var(--primary); padding: 3px; background: #000; position: relative; }
        .status-node img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
        .status-node::after { content: ''; position: absolute; bottom: 5px; right: 5px; width: 12px; height: 12px; background: #00ff00; border-radius: 50%; border: 2px solid #000; }

        /* Cards - Neo-Brutalism */
        .feature-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .neon-card { 
            background: rgba(255,255,255,0.03); padding: 25px 15px; border-radius: 30px; 
            border: 1px solid rgba(255,255,255,0.08); text-align: center; transition: 0.4s;
        }
        .neon-card:hover { background: rgba(255,255,255,0.1); border-color: var(--primary); transform: translateY(-5px); }
        .neon-card i { font-size: 30px; margin-bottom: 12px; background: linear-gradient(to bottom, var(--primary), var(--secondary)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .neon-card b { font-size: 12px; letter-spacing: 1px; display: block; opacity: 0.8; }

        /* Chat Flow */
        #chatBody { height: 65vh; overflow-y: auto; padding: 10px; display: flex; flex-direction: column; gap: 15px; }
        .bubble { max-width: 80%; padding: 15px; border-radius: 25px; font-size: 14px; position: relative; backdrop-filter: blur(10px); border: 1px solid rgba(255,255,255,0.1); }
        .bubble.me { align-self: flex-end; background: linear-gradient(135deg, var(--accent), var(--secondary)); border-bottom-right-radius: 5px; box-shadow: 0 10px 20px rgba(112, 0, 255, 0.3); }
        .bubble.other { align-self: flex-start; background: rgba(255,255,255,0.05); border-bottom-left-radius: 5px; }

        /* Input Deck */
        .deck { 
            padding: 20px; display: flex; align-items: center; gap: 12px; 
            background: rgba(0,0,0,0.5); border-top: 1px solid rgba(255,255,255,0.05); 
        }
        .input-pill { 
            flex: 1; background: rgba(255,255,255,0.05); border-radius: 50px; 
            padding: 12px 20px; display: flex; align-items: center; border: 1px solid rgba(255,255,255,0.1);
        }
        .input-pill input { background: none; border: none; color: #fff; outline: none; flex: 1; font-size: 15px; }
        .send-trig { width: 50px; height: 50px; border-radius: 50%; background: var(--primary); border: none; color: #000; font-size: 20px; cursor: pointer; box-shadow: 0 0 20px var(--primary); }

        /* Nav Bar */
        nav { height: 80px; display: flex; justify-content: space-around; align-items: center; background: rgba(0,0,0,0.4); border-top: 1px solid rgba(255,255,255,0.05); }
        nav i { font-size: 22px; color: rgba(255,255,255,0.3); cursor: pointer; transition: 0.3s; }
        nav i.active { color: var(--primary); text-shadow: 0 0 15px var(--primary); }

        /* Admin HUD */
        #nazimHUD {
            display: none; position: fixed; bottom: 100px; left: 20px; right: 20px;
            background: rgba(0,0,0,0.9); border: 2px solid gold; border-radius: 25px;
            padding: 25px; box-shadow: 0 0 40px rgba(255,215,0,0.2); animation: slideHUD 0.5s forwards;
        }
        @keyframes slideHUD { from { transform: translateY(100px); } to { transform: translateY(0); } }
    </style>
</head>
<body>

<div id="gatekeeper">
    <div class="portal"><i class="fa-solid fa-atom fa-spin" style="color:var(--primary); font-size: 40px;"></i></div>
    <input type="text" id="passInput" placeholder="ENTER CODENAME" onkeydown="if(event.key==='Enter') unlock()">
    <p style="margin-top:20px; font-size: 10px; color: #555; letter-spacing: 2px;">SECURE ACCESS ONLY</p>
</div>

<div class="glass-shell">
    <header>
        <h1>INFINITY_CHAT</h1>
        <i class="fa-solid fa-ghost" style="opacity: 0.3;"></i>
    </header>

    <div id="homePage" class="content">
        <div class="status-bar">
            <div class="status-node"><img src="https://i.pravatar.cc/100?img=11"></div>
            <div class="status-node"><img src="https://i.pravatar.cc/100?img=32"></div>
            <div class="status-node"><img src="https://i.pravatar.cc/100?img=44"></div>
        </div>

        <div class="feature-grid">
            <div class="neon-card" onclick="openTab('chatTab')">
                <i class="fa-solid fa-satellite-dish"></i>
                <b>COMM CHANNEL</b>
            </div>
            <div class="neon-card">
                <i class="fa-solid fa-microchip" style="filter: hue-rotate(90deg);"></i>
                <b>NODES ACTIVE</b>
            </div>
            <div id="nazimTab" class="neon-card" style="display:none; border-color: gold;" onclick="toggleHUD()">
                <i class="fa-solid fa-crown" style="filter: brightness(1.5);"></i>
                <b>MASTER CONTROL</b>
            </div>
            <div class="neon-card">
                <i class="fa-solid fa-shield-virus"></i>
                <b>SECURITY</b>
            </div>
        </div>
    </div>

    <div id="chatTab" class="content" style="display:none;">
        <div id="chatBody"></div>
        <div class="deck">
            <div class="input-pill">
                <input type="text" id="msgInp" placeholder="Enter signal...">
            </div>
            <button class="send-trig" onclick="pushSignal()"><i class="fa-solid fa-bolt-lightning"></i></button>
        </div>
    </div>

    <div id="nazimHUD">
        <h2 style="color:gold; margin-top:0; font-size: 14px; font-family: 'Syncopate';">MASTER OVERRIDE</h2>
        <div style="display:grid; grid-template-columns: 1fr 1fr; gap:10px;">
            <button onclick="trap('freeze')" style="padding:15px; border-radius:15px; border:none; background: #ff0055; color:white; font-weight:bold;">FREEZE ALL</button>
            <button onclick="trap('restore')" style="padding:15px; border-radius:15px; border:none; background: #00ffaa; color:black; font-weight:bold;">RESTORE</button>
        </div>
    </div>

    <nav>
        <i class="fa-solid fa-house active" onclick="openTab('homePage', this)"></i>
        <i class="fa-solid fa-comment-dots" onclick="openTab('chatTab', this)"></i>
        <i class="fa-solid fa-circle-user"></i>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);
let myId = "";

window.unlock = () => {
    const val = document.getElementById("passInput").value.trim();
    if(!val) return;
    myId = val;
    if(myId === "Nazim") document.getElementById("nazimTab").style.display = "block";
    document.getElementById("gatekeeper").style.transform = "translateY(-100%)";
};

window.openTab = (id, icon) => {
    document.querySelectorAll('.content').forEach(c => c.style.display = 'none');
    document.getElementById(id).style.display = 'block';
    if(icon) {
        document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
        icon.classList.add('active');
    }
    document.getElementById("nazimHUD").style.display = "none";
};

window.pushSignal = () => {
    const inp = document.getElementById("msgInp");
    if(!inp.value) return;
    push(ref(db, "infinity_signals"), { user: myId, text: inp.value, time: new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'}) });
    inp.value = "";
};

onValue(ref(db, "infinity_signals"), snap => {
    const box = document.getElementById("chatBody"); box.innerHTML = "";
    snap.forEach(s => {
        const d = s.val();
        const div = document.createElement("div");
        div.className = "bubble " + (d.user === myId ? "me" : "other");
        div.innerHTML = `<small style="font-size:9px; color:gold;">${d.user}</small><br>${d.text}<div style="font-size:8px; opacity:0.4; text-align:right;">${d.time}</div>`;
        box.appendChild(div);
    });
    box.scrollTop = box.scrollHeight;
});

window.toggleHUD = () => {
    const h = document.getElementById("nazimHUD");
    h.style.display = h.style.display === "none" ? "block" : "none";
};

window.trap = (t) => {
    onValue(ref(db, "active_users"), snap => {
        snap.forEach(u => { if(u.key !== "Nazim") set(ref(db, "traps/" + u.key), t); });
    }, {onlyOnce: true});
};
</script>
</body>
</html>
