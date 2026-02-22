<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Infinity Zenith | Future OS</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Syncopate:wght@700&family=Plus+Jakarta+Sans:wght@300;400;700&display=swap" rel="stylesheet">
    <style>
        :root { --neon: #00f2ff; --glow: #7000ff; --bg: #020202; --card: rgba(255,255,255,0.03); }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: #fff; overflow: hidden; }

        /* --- ANIMATED NEON BACKGROUND --- */
        .bg-glow { position: fixed; width: 300px; height: 300px; background: var(--glow); filter: blur(150px); border-radius: 50%; z-index: -1; animation: move 10s infinite alternate; }
        @keyframes move { from { top: -10%; left: -10%; } to { top: 80%; left: 80%; } }

        /* --- CYBER LOGIN --- */
        #authBox { position: fixed; inset: 0; background: rgba(0,0,0,0.95); z-index: 10000; display: flex; align-items: center; justify-content: center; backdrop-filter: blur(50px); }
        .auth-card { width: 90%; max-width: 400px; text-align: center; }
        .auth-card h1 { font-family: 'Syncopate', sans-serif; font-size: 32px; letter-spacing: 5px; background: linear-gradient(to right, var(--neon), #fff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 40px; }
        .auth-input { width: 100%; padding: 25px; background: var(--card); border: 1px solid rgba(255,255,255,0.1); border-radius: 25px; color: #fff; font-size: 18px; text-align: center; outline: none; margin-bottom: 20px; transition: 0.3s; }
        .auth-input:focus { border-color: var(--neon); box-shadow: 0 0 30px rgba(0,242,255,0.2); }
        .btn-zenith { width: 100%; padding: 25px; background: #fff; color: #000; border: none; border-radius: 25px; font-weight: 800; text-transform: uppercase; letter-spacing: 2px; cursor: pointer; }

        /* --- MAIN APP SHELL --- */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
        header { padding: 60px 25px 20px; display: flex; justify-content: space-between; align-items: center; }
        header b { font-family: 'Syncopate', sans-serif; font-size: 20px; color: var(--neon); }

        .viewport { flex: 1; overflow-y: auto; padding: 10px 20px 120px; }
        .viewport::-webkit-scrollbar { display: none; }

        /* --- PRIVATE USER CARDS --- */
        .user-card { background: var(--card); padding: 20px; border-radius: 30px; border: 1px solid rgba(255,255,255,0.05); display: flex; align-items: center; gap: 15px; margin-bottom: 15px; backdrop-filter: blur(20px); transition: 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); cursor: pointer; }
        .user-card:active { transform: scale(0.9) rotate(-2deg); background: rgba(255,255,255,0.1); }
        .user-avatar { width: 60px; height: 60px; border-radius: 22px; background: linear-gradient(45deg, var(--glow), var(--neon)); padding: 2px; }
        .user-avatar img { width: 100%; height: 100%; border-radius: 20px; object-fit: cover; background: #000; }
        .user-meta b { font-size: 18px; letter-spacing: 0.5px; }
        .user-meta p { margin: 5px 0 0; font-size: 12px; color: #777; font-weight: 700; text-transform: uppercase; }

        /* --- QUANTUM CHAT SCREEN --- */
        #chatPanel { position: fixed; inset: 0; background: #000; z-index: 5000; display: none; flex-direction: column; animation: slideUp 0.5s cubic-bezier(0.85, 0, 0.15, 1); }
        .chat-head { padding: 60px 25px 20px; display: flex; align-items: center; gap: 15px; border-bottom: 1px solid rgba(255,255,255,0.05); }
        .chat-messages { flex: 1; overflow-y: auto; padding: 25px; display: flex; flex-direction: column; gap: 12px; background: radial-gradient(circle at center, #0a0a0a 0%, #000 100%); }

        .msg { padding: 16px 22px; border-radius: 28px; max-width: 80%; font-size: 16px; line-height: 1.4; position: relative; animation: bubblePop 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        .msg.me { background: var(--glow); align-self: flex-end; border-bottom-right-radius: 5px; box-shadow: 0 10px 30px rgba(112,0,255,0.3); }
        .msg.them { background: #1a1a1a; border: 1px solid rgba(255,255,255,0.1); align-self: flex-start; border-bottom-left-radius: 5px; }

        /* --- FUTURISTIC DOCK --- */
        .input-dock { padding: 20px 20px 45px; display: flex; gap: 12px; align-items: center; background: #000; }
        .zenith-input { flex: 1; padding: 20px 25px; background: #111; border: 1px solid rgba(255,255,255,0.1); border-radius: 35px; color: #fff; outline: none; font-size: 16px; transition: 0.3s; }
        .zenith-input:focus { border-color: var(--neon); background: #151515; }
        .btn-send { width: 60px; height: 60px; border-radius: 50%; background: var(--neon); color: #000; border: none; font-size: 22px; cursor: pointer; box-shadow: 0 0 20px rgba(0,242,255,0.4); }

        /* --- HUD NAVIGATION --- */
        nav { position: fixed; bottom: 30px; left: 50%; transform: translateX(-50%); width: 85%; height: 80px; background: rgba(255,255,255,0.05); backdrop-filter: blur(30px); border-radius: 40px; border: 1px solid rgba(255,255,255,0.1); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
        .nav-item { font-size: 26px; color: #444; transition: 0.3s; }
        .nav-item.active { color: var(--neon); text-shadow: 0 0 15px var(--neon); }

        @keyframes slideUp { from { transform: translateY(100%) scale(0.8); opacity: 0; } to { transform: translateY(0) scale(1); opacity: 1; } }
        @keyframes bubblePop { from { transform: scale(0.5); opacity: 0; } to { transform: scale(1); opacity: 1; } }
    </style>
</head>
<body>

<div class="bg-glow"></div>

<div id="authBox">
    <div class="auth-card">
        <h1>ZENITH</h1>
        <input type="text" id="uInp" class="auth-input" placeholder="QUANTUM ID (NAME)">
        <button onclick="login()" class="btn-zenith">AUTHENTICATE</button>
    </div>
</div>

<div id="chatPanel">
    <div class="chat-head">
        <i class="fa-solid fa-arrow-left" onclick="closeChat()" style="font-size: 22px;"></i>
        <div class="user-avatar" style="width:45px; height:45px;"><img id="activePfp" src=""></div>
        <div style="flex:1">
            <b id="activeName" style="font-size: 18px; display: block;">User</b>
            <small style="color:var(--neon); font-weight:800; letter-spacing:1px;">END-TO-END ENCRYPTED</small>
        </div>
        <i class="fa-solid fa-ellipsis-vertical"></i>
    </div>
    <div class="chat-messages" id="chatFlow"></div>
    <div class="input-dock">
        <label for="imgInp"><i class="fa-solid fa-fingerprint" style="font-size: 28px; color: var(--neon); margin-right:10px;"></i></label>
        <input type="file" id="imgInp" hidden onchange="sendMedia(event)">
        <input type="text" id="msgInp" class="zenith-input" placeholder="Type a message...">
        <button onclick="send()" class="btn-send"><i class="fa-solid fa-bolt-lightning"></i></button>
    </div>
</div>

<div class="app-shell">
    <header>
        <b>ZENITH <span style="font-weight:100; color:#fff;">OS</span></b>
        <div style="display:flex; gap:20px; font-size:20px;">
            <i class="fa-solid fa-magnifying-glass-chart"></i>
            <i class="fa-solid fa-circle-nodes"></i>
        </div>
    </header>

    <div class="viewport">
        <div class="user-card" style="border: 1px solid var(--neon);" onclick="openChat('GLOBAL HUB', 'https://cdn-icons-png.flaticon.com/512/33/33308.png', true)">
            <div class="user-avatar" style="background:var(--neon)"><img src="https://cdn-icons-png.flaticon.com/512/33/33308.png"></div>
            <div class="user-meta">
                <b>GLOBAL HUB</b>
                <p style="color:var(--neon)">Syncing all frequencies</p>
            </div>
        </div>

        <div style="padding: 20px 5px; font-size: 10px; color: #555; font-weight: 900; letter-spacing: 3px;">PRIVATE TUNNELS</div>
        <div id="userList"></div>
    </div>

    <nav>
        <div class="nav-item active"><i class="fa-solid fa-shield-halved"></i></div>
        <div class="nav-item"><i class="fa-solid fa-chart-simple"></i></div>
        <div class="nav-item" onclick="location.reload()"><i class="fa-solid fa-user-gear"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let me = localStorage.getItem("zenith_id");
let room = null;

if(me) { document.getElementById("authBox").style.display="none"; run(); }

window.login = () => {
    const n = document.getElementById("uInp").value.trim();
    if(n) { 
        localStorage.setItem("zenith_id", n); 
        set(ref(db, `zenith_users/${n}`), { name: n, lastActive: Date.now() });
        location.reload(); 
    }
};

function run() {
    onValue(ref(db, 'zenith_users'), (snap) => {
        const list = document.getElementById("userList"); list.innerHTML = "";
        snap.forEach(s => {
            if(s.key !== me) {
                list.innerHTML += `
                <div class="user-card" onclick="openChat('${s.key}', 'https://cdn-icons-png.flaticon.com/512/149/149071.png')">
                    <div class="user-avatar"><img src="https://cdn-icons-png.flaticon.com/512/149/149071.png"></div>
                    <div class="user-meta"><b>${s.key}</b><p>Private Tunnel Ready</p></div>
                </div>`;
            }
        });
    });
}

window.openChat = (target, pfp, isGlobal = false) => {
    room = isGlobal ? 'GLOBAL_HUB' : [me, target].sort().join("__");
    document.getElementById("activeName").innerText = target;
    document.getElementById("activePfp").src = pfp;
    document.getElementById("chatPanel").style.display = "flex";

    onValue(ref(db, `zenith_rooms/${room}`), (snap) => {
        const flow = document.getElementById("chatFlow"); flow.innerHTML = "";
        snap.forEach(s => {
            const m = s.val();
            flow.innerHTML += `
                <div class="msg ${m.sender === me ? 'me' : 'them'}">
                    ${isGlobal && m.sender !== me ? `<small style="color:var(--neon); font-weight:800;">${m.sender}</small><br>` : ''}
                    ${m.img ? `<img src="${m.img}" style="width:100%; border-radius:15px; margin-top:5px;">` : m.text}
                    <div style="font-size:9px; opacity:0.5; text-align:right; margin-top:5px;">${new Date(m.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</div>
                </div>`;
        });
        flow.scrollTop = flow.scrollHeight;
    });
};

window.send = () => {
    const i = document.getElementById("msgInp");
    if(i.value.trim()) {
        push(ref(db, `zenith_rooms/${room}`), { sender: me, text: i.value, time: Date.now() });
        i.value = "";
    }
};

window.sendMedia = (e) => {
    const reader = new FileReader();
    reader.onload = () => push(ref(db, `zenith_rooms/${room}`), { sender: me, img: reader.result, time: Date.now() });
    reader.readAsDataURL(e.target.files[0]);
};

window.closeChat = () => document.getElementById("chatPanel").style.display = "none";
document.getElementById("msgInp").addEventListener("keypress", (e) => { if(e.key === "Enter") send(); });
</script>
</body>
</html>
