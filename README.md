<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Infinity Ultra | Next-Gen</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        :root { 
            --glow: #00f2ff; --p: #7000ff; --bg: #050505; --card: rgba(255,255,255,0.05); 
            --border: rgba(255,255,255,0.1); --text: #fff; 
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Space Grotesk', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* --- QUANTUM LOGIN --- */
        #authBox { position: fixed; inset: 0; background: radial-gradient(circle at center, #1a0033 0%, #000 100%); z-index: 10000; display: flex; align-items: center; justify-content: center; padding: 40px; }
        .auth-card { width: 100%; max-width: 400px; text-align: center; animation: float 3s infinite ease-in-out; }
        .auth-input { width: 100%; padding: 22px; background: var(--card); border: 1px solid var(--border); border-radius: 20px; color: #fff; margin-bottom: 20px; backdrop-filter: blur(20px); font-size: 18px; outline: none; transition: 0.4s; }
        .auth-input:focus { border-color: var(--glow); box-shadow: 0 0 20px rgba(0,242,255,0.2); }
        .btn-neon { width: 100%; padding: 22px; background: linear-gradient(45deg, var(--p), var(--glow)); border: none; border-radius: 20px; color: #fff; font-weight: 800; cursor: pointer; text-transform: uppercase; letter-spacing: 2px; }

        /* --- MASTER LAYOUT --- */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; background: url('https://w0.peakpx.com/wallpaper/596/778/HD-wallpaper-black-abstract-dark-material-design-minimal-modern.jpg'); background-size: cover; }
        
        header { padding: 60px 25px 20px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(30px); border-bottom: 1px solid var(--border); }
        header h1 { font-size: 28px; font-weight: 800; background: linear-gradient(to right, #fff, var(--glow)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

        .viewport { flex: 1; overflow-y: auto; padding: 10px 0 120px; scroll-behavior: smooth; }
        .viewport::-webkit-scrollbar { display: none; }

        /* --- CHAT LIST --- */
        .chat-tile { margin: 10px 15px; padding: 18px; background: var(--card); border: 1px solid var(--border); border-radius: 24px; display: flex; align-items: center; gap: 15px; backdrop-filter: blur(15px); transition: 0.3s; }
        .chat-tile:active { transform: scale(0.95); background: rgba(255,255,255,0.1); }
        .tile-pfp { width: 60px; height: 60px; border-radius: 20px; object-fit: cover; border: 2px solid var(--p); }
        .tile-info { flex: 1; }
        .tile-info b { font-size: 18px; color: #fff; }
        .tile-info p { margin: 5px 0 0; font-size: 13px; color: #aaa; }

        /* --- FULL SCREEN CHAT ENGINE --- */
        #chatEngine { position: fixed; inset: 0; background: #000; z-index: 5000; display: none; flex-direction: column; animation: slideX 0.4s cubic-bezier(0.19, 1, 0.22, 1); }
        .chat-top { padding: 60px 20px 20px; display: flex; align-items: center; gap: 15px; background: rgba(255,255,255,0.03); backdrop-filter: blur(40px); border-bottom: 1px solid var(--border); }
        .chat-main { flex: 1; overflow-y: auto; padding: 25px; display: flex; flex-direction: column; gap: 12px; background: radial-gradient(circle at top right, #110022, #000); }

        .bubble { padding: 14px 20px; border-radius: 25px; max-width: 85%; font-size: 16px; position: relative; animation: pop 0.3s ease; line-height: 1.5; }
        .bubble.me { background: linear-gradient(135deg, var(--p), #400080); align-self: flex-end; border-bottom-right-radius: 5px; box-shadow: 0 10px 20px rgba(112,0,255,0.3); }
        .bubble.them { background: #1a1a1a; border: 1px solid var(--border); align-self: flex-start; border-bottom-left-radius: 5px; }
        .bubble img { width: 100%; border-radius: 15px; margin-top: 8px; }

        /* --- NEON INPUT --- */
        .dock { padding: 20px 20px 45px; display: flex; align-items: center; gap: 12px; background: #000; }
        .dock-input { flex: 1; padding: 18px 25px; background: #111; border: 1px solid var(--border); border-radius: 30px; color: #fff; outline: none; font-size: 16px; }
        .dock-input:focus { border-color: var(--p); }
        .icon-btn { width: 55px; height: 55px; border-radius: 50%; display: flex; align-items: center; justify-content: center; background: var(--card); border: 1px solid var(--border); color: #fff; font-size: 20px; cursor: pointer; transition: 0.3s; }
        .send-active { background: var(--p); box-shadow: 0 0 20px var(--p); border: none; }

        /* --- TAB NAVIGATION --- */
        nav { position: fixed; bottom: 25px; left: 50%; transform: translateX(-50%); width: 90%; max-width: 400px; height: 75px; background: rgba(255,255,255,0.08); backdrop-filter: blur(30px); border-radius: 35px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
        .n-link { color: #666; font-size: 24px; transition: 0.3s; }
        .n-link.active { color: var(--glow); text-shadow: 0 0 15px var(--glow); transform: translateY(-5px); }

        @keyframes float { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-15px); } }
        @keyframes slideX { from { transform: translateX(100%); } to { transform: translateX(0); } }
        @keyframes pop { from { transform: scale(0.8); opacity: 0; } to { transform: scale(1); opacity: 1; } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1 style="font-size: 45px; margin-bottom: 10px; color: #fff;">INFINITY <span style="color:var(--glow)">X</span></h1>
        <p style="color: #888; margin-bottom: 30px; letter-spacing: 3px;">QUANTUM MESSENGER</p>
        <input type="text" id="uInp" class="auth-input" placeholder="ACCESS KEY (NAME)">
        <button onclick="login()" class="btn-neon">Initialize Session</button>
    </div>
</div>

<div id="chatEngine">
    <div class="chat-top">
        <i class="fa-solid fa-chevron-left" onclick="closeChat()" style="font-size: 24px;"></i>
        <img id="activePfp" src="" class="tile-pfp" style="width:45px; height:45px;">
        <div style="flex:1">
            <b id="activeTitle" style="font-size: 20px;">User</b>
            <div style="display:flex; align-items:center; gap:5px; color:var(--glow); font-size:11px; font-weight:bold;">
                <span style="width:6px; height:6px; background:var(--glow); border-radius:50%; display:inline-block;"></span> ENCRYPTED
            </div>
        </div>
        <i class="fa-solid fa-video" style="color:#aaa; font-size: 20px;"></i>
    </div>
    <div class="chat-main" id="chatFlow"></div>
    <div class="dock">
        <label for="imgInp" class="icon-btn"><i class="fa-solid fa-plus"></i></label>
        <input type="file" id="imgInp" hidden onchange="uploadMedia(event)">
        <input type="text" id="msgInp" class="dock-input" placeholder="Type a message...">
        <button id="sendBtn" onclick="send()" class="icon-btn"><i class="fa-solid fa-microphone" id="mic"></i></button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h1>INFINITY</h1>
        <div style="display:flex; gap:25px; font-size:22px;">
            <i class="fa-solid fa-ghost" style="color:var(--glow)"></i>
            <i class="fa-solid fa-gear"></i>
        </div>
    </header>

    <div class="viewport">
        <div class="chat-tile" onclick="openChat('Global_Core', 'https://cdn-icons-png.flaticon.com/512/33/33308.png', true)">
            <div class="tile-pfp" style="background:var(--p); display:flex; align-items:center; justify-content:center;"><i class="fa-solid fa-atom fa-spin"></i></div>
            <div class="tile-info">
                <b>Global Core</b>
                <p>Public quantum frequency...</p>
            </div>
            <div style="background:var(--glow); color:#000; font-weight:900; padding:2px 8px; border-radius:10px; font-size:10px;">LIVE</div>
        </div>

        <div style="padding: 25px 20px 10px; font-size: 11px; color: #555; font-weight: 800; letter-spacing: 2px;">SECURE CHATS</div>
        <div id="contacts"></div>
    </div>

    <nav>
        <div class="n-link active"><i class="fa-solid fa-comment-nodes"></i></div>
        <div class="n-link"><i class="fa-solid fa-fingerprint"></i></div>
        <div class="n-link"><i class="fa-solid fa-user-astronaut"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let user = localStorage.getItem("inf_ultra_user");
let activeRoom = null;

if(user) { document.getElementById("authBox").style.display="none"; init(); }

window.login = () => {
    const n = document.getElementById("uInp").value.trim();
    if(n) { 
        localStorage.setItem("inf_ultra_user", n); 
        set(ref(db, `ultra_users/${n}`), { name: n, status: 'online' });
        location.reload(); 
    }
};

function init() {
    onValue(ref(db, 'ultra_users'), (snap) => {
        const list = document.getElementById("contacts"); list.innerHTML = "";
        snap.forEach(s => {
            if(s.key !== user) {
                list.innerHTML += `
                <div class="chat-tile" onclick="openChat('${s.key}', 'https://cdn-icons-png.flaticon.com/512/149/149071.png')">
                    <img src="https://cdn-icons-png.flaticon.com/512/149/149071.png" class="tile-pfp">
                    <div class="tile-info"><b>${s.key}</b><p>Secure connection established</p></div>
                </div>`;
            }
        });
    });
}

window.openChat = (target, pfp, isGroup = false) => {
    activeRoom = isGroup ? target : [user, target].sort().join("_");
    document.getElementById("activeTitle").innerText = target;
    document.getElementById("activePfp").src = pfp;
    document.getElementById("chatEngine").style.display = "flex";

    onValue(ref(db, `ultra_rooms/${activeRoom}`), (snap) => {
        const flow = document.getElementById("chatFlow"); flow.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.sender === user;
            flow.innerHTML += `
                <div class="bubble ${isMe?'me':'them'}">
                    ${isGroup && !isMe ? `<small style="color:var(--glow); font-size:10px;">${m.sender}</small><br>` : ''}
                    ${m.img ? `<img src="${m.img}">` : m.text}
                    <div style="font-size:9px; opacity:0.6; text-align:right; margin-top:5px;">${new Date(m.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</div>
                </div>`;
        });
        flow.scrollTop = flow.scrollHeight;
    });
};

window.send = () => {
    const i = document.getElementById("msgInp");
    if(i.value.trim()) {
        push(ref(db, `ultra_rooms/${activeRoom}`), { sender: user, text: i.value, time: Date.now() });
        i.value = "";
    }
};

window.uploadMedia = (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();
    reader.onload = () => push(ref(db, `ultra_rooms/${activeRoom}`), { sender: user, img: reader.result, time: Date.now() });
    reader.readAsDataURL(file);
};

window.closeChat = () => document.getElementById("chatEngine").style.display = "none";

// Dynamic UI Logic
const inp = document.getElementById("msgInp");
const btn = document.getElementById("sendBtn");
const mic = document.getElementById("mic");

inp.oninput = () => {
    if(inp.value.length > 0) {
        btn.classList.add("send-active");
        mic.className = "fa-solid fa-paper-plane";
    } else {
        btn.classList.remove("send-active");
        mic.className = "fa-solid fa-microphone";
    }
};
inp.onkeypress = (e) => { if(e.key === "Enter") send(); };
</script>
</body>
</html>
