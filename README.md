<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect | Ghost Admin Console</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root { --p: #6b5bff; --a: #ff5c8d; --bg: #020202; --card: rgba(255,255,255,0.06); --text: #ffffff; --muted: #888; --border: rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
body { margin: 0; background: var(--bg); color: var(--text); overflow: hidden; }

/* OVERRIDE SCREEN */
#maintenanceOverlay { display:none; position:fixed; inset:0; background:#000; z-index:10000; flex-direction:column; align-items:center; justify-content:center; text-align:center; padding:40px; }
.glitch { font-size: 35px; font-weight: 800; color: red; text-shadow: 0 0 15px red; animation: shake 0.2s infinite; }
@keyframes shake { 0% { transform: translate(2px, -2px); } 50% { transform: translate(-2px, 2px); } 100% { transform: translate(0); } }

.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
#newsBar { display: none; background: linear-gradient(90deg, #ff4444, #ff8c00); color: white; padding: 12px; font-size: 13px; font-weight: 800; text-align: center; z-index: 2000; position: absolute; top: 0; width: 100%; }

header { padding: 55px 25px 15px; display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 22px; font-weight: 800; background: linear-gradient(90deg, #fff, var(--p)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 92%; height: 75px; background: rgba(15,15,15,0.95); backdrop-filter: blur(20px); border-radius: 30px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
.nav-btn { color: var(--muted); font-size: 24px; cursor: pointer; }
.nav-btn.active { color: var(--p); transform: translateY(-8px); text-shadow: 0 0 20px var(--p); }

.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 130px; }
.page.active { display: block; animation: slideUp 0.4s ease; }
@keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }

.chat-container { background: rgba(255,255,255,0.02); border-radius: 30px; border: 1px solid var(--border); height: 50vh; overflow-y: auto; padding: 18px; display: flex; flex-direction: column; gap: 12px; }
.bubble { padding: 12px 18px; border-radius: 22px; font-size: 14px; background: #181818; max-width: 85%; border: 1px solid var(--border); }
.bubble.me { background: var(--p); align-self: flex-end; border: none; }

.dash-card { background: var(--card); border-radius: 26px; padding: 24px; margin-bottom: 15px; border: 1px solid var(--border); }
.magic-btn { width: 100%; padding: 16px; border-radius: 18px; border: none; font-weight: 800; cursor: pointer; margin-top: 12px; }

#auth { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; }
#auth img { width: 180px; height: 180px; border-radius: 50%; margin-bottom: 25px; border: 2px solid var(--p); box-shadow: 0 0 40px var(--p); object-fit: cover; }
#auth input { width: 100%; max-width: 320px; padding: 20px; border-radius: 22px; background: var(--card); border: 1px solid var(--border); color: #fff; text-align: center; margin-bottom: 15px; }

.spy-badge { color: gold; font-size: 9px; border: 1px solid gold; padding: 2px 6px; border-radius: 5px; margin-right: 8px; }
</style>
</head>
<body>

<div id="newsBar"></div>

<div id="maintenanceOverlay">
    <div class="glitch">SYSTEM OVERRIDE</div>
    <p style="color:white; opacity:0.8;">Access locked by Ghost Admin.</p>
</div>

<div class="app">
  <header>
    <h1>Live Connect <span id="ghostBadge" style="display:none; color:gold; font-size:10px; border:1px solid gold; padding:2px 5px; border-radius:5px; margin-left:8px;">GHOST MODE</span></h1>
    <div onclick="logout()" style="color:var(--muted); cursor:pointer;"><i class="fa-solid fa-power-off"></i></div>
  </header>

  <div id="auth">
    <img src="http://googleusercontent.com/generated_image_content/0.png" alt="Ghost Admin">
    <h2 style="margin:0;">Secure Login</h2>
    <p style="color: var(--muted); font-size: 12px; margin: 10px 0 25px;">Initialize Private Protocol</p>
    <input type="text" id="userInput" placeholder="Identity Name">
    <button onclick="login()" style="width:100%; max-width:320px; padding:20px; border-radius:22px; background:var(--p); border:none; color:#fff; font-weight:800; cursor:pointer;">INITIATE ACCESS</button>
  </div>

  <div id="home" class="page active">
    <div class="dash-card" style="background: linear-gradient(135deg, #6b5bff, #ff5c8d); border:none;">
        <h2 style="margin:0;">Master <span id="uNameDisp">...</span></h2>
        <p id="roleNote" style="opacity:0.9; font-size:13px;">Secure Node Connected</p>
    </div>

    <div id="godConsole" style="display:none;">
        <h4 style="color:gold; font-size:10px; letter-spacing:2px;">MASTER CONTROL UNIT</h4>
        <div class="dash-card" style="border: 1px solid gold;">
            <input type="text" id="newsInput" style="width:100%; padding:15px; background:rgba(0,0,0,0.5); border:1px solid var(--border); color:white; border-radius:12px;" placeholder="Broadcast Message...">
            <button class="magic-btn" style="background:gold; color:black;" onclick="broadcastNews()">SEND BROADCAST</button>
            <button id="overrideBtn" class="magic-btn" style="background:purple; color:white;" onclick="toggleOverride()">ACTIVATE OVERRIDE</button>
            <button class="magic-btn" style="background:#ff4444; color:white;" onclick="wipeAll()">WIPE ALL DATA</button>
        </div>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatWith" style="font-size:12px; font-weight:800; color:var(--p); margin-bottom:15px;">CHANNEL</div>
    <div class="chat-container" id="chatBox"></div>
    <div style="display:flex; gap:12px; padding-top:15px;">
      <input id="msgInput" style="flex:1; background:var(--card); border:1px solid var(--border); color:white; padding:16px; border-radius:20px;" placeholder="Type..."/>
      <button onclick="sendMsg()" style="width:55px; height:55px; border-radius:50%; border:none; background:var(--p); color:white;"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <div id="userList"></div>
    <div id="spySection" style="display:none;">
        <h4 style="color:gold; font-size:10px; margin-top:30px;">GLOBAL SPY CHANNELS</h4>
        <div id="spyList"></div>
    </div>
  </div>

  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-ghost"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></div>
    <div class="nav-btn" onclick="openPage('users',this)"><i class="fa-solid fa-user-shield"></i></div>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, get } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");
let role = localStorage.getItem("lc_role") || "user";
let curChat = "", spyMode = false;

if(user) boot();

window.login = () => {
    const n = document.getElementById("userInput").value.trim();
    if(n) {
        user = n; localStorage.setItem("lc_user", n);
        role = (n === "Admin786") ? "admin" : "user";
        localStorage.setItem("lc_role", role);
        location.reload();
    }
};

function boot() {
    document.getElementById("auth").style.display = "none";
    document.getElementById("uNameDisp").textContent = user;
    if(role === "admin") {
        document.getElementById("godConsole").style.display = "block";
        document.getElementById("ghostBadge").style.display = "inline";
        document.getElementById("spySection").style.display = "block";
    }
    
    onValue(ref(db, "systemState"), snap => {
        const locked = snap.val()?.locked;
        if(locked && role !== "admin") document.getElementById("maintenanceOverlay").style.display = "flex";
        else document.getElementById("maintenanceOverlay").style.display = "none";
        if(role === "admin") document.getElementById("overrideBtn").innerText = locked ? "RESTORE SYSTEM" : "ACTIVATE OVERRIDE";
    });

    onValue(ref(db, "globalNews"), snap => {
        const bar = document.getElementById("newsBar");
        if(snap.exists()) { bar.innerText = "🚨 " + snap.val().text; bar.style.display = "block"; }
        else bar.style.display = "none";
    });
}

onValue(ref(db, "users"), snap => {
    const list = document.getElementById("userList"); list.innerHTML = "";
    snap.forEach(u => {
        if(u.key !== user && u.key !== "Admin786") {
            const div = document.createElement("div"); div.className = "dash-card";
            div.innerHTML = `<b>${u.val().name}</b>`;
            div.onclick = () => { curChat=u.key; spyMode=false; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
            list.appendChild(div);
        }
    });
});

if(role === "admin") {
    onValue(ref(db, "chats"), snap => {
        const list = document.getElementById("spyList"); list.innerHTML = "";
        snap.forEach(c => {
            const div = document.createElement("div"); div.className = "dash-card";
            div.innerHTML = `<span class="spy-badge">SPY</span> <b>${c.key}</b>`;
            div.onclick = () => { curChat=c.key; spyMode=true; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
            list.appendChild(div);
        });
    });
}

function loadChat() {
    const path = spyMode ? "chats/" + curChat : "chats/" + [user, curChat].sort().join("_");
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const v = m.val();
            const div = document.createElement("div"); div.className = "bubble " + (v.from === user ? "me" : "them");
            div.innerHTML = `<small style="display:block;opacity:0.5;font-size:9px;">${v.from}</small>${v.text}`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.sendMsg = () => {
    const i = document.getElementById('msgInput'); if(!i.value || spyMode) return;
    push(ref(db, "chats/" + [user, curChat].sort().join("_")), { from: user, text: i.value });
    i.value = '';
};

window.toggleOverride = async () => {
    const s = await get(ref(db, "systemState"));
    set(ref(db, "systemState"), { locked: !s.val()?.locked });
};

window.broadcastNews = () => set(ref(db, "globalNews"), {text: document.getElementById("newsInput").value});
window.wipeAll = () => { if(confirm("Wipe everything?")) { remove(ref(db, "chats")); remove(ref(db, "users")); } };
window.logout = () => { localStorage.clear(); location.reload(); };
window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('.nav-btn').forEach(n => n.classList.remove('active'));
    btn.classList.add('active');
};
</script>
</body>
</html>
