<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect | God Mode spying</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root { --p: #6b5bff; --a: #ff5c8d; --bg: #030303; --card: rgba(255,255,255,0.08); --text: #ffffff; --muted: #888; --border: rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.3s ease; }
body { margin: 0; background: var(--bg); color: var(--text); overflow: hidden; }
.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
#newsBar { display: none; background: linear-gradient(90deg, #ff4444, #ff8c00); color: white; padding: 12px; font-size: 13px; font-weight: 800; text-align: center; z-index: 2000; position: absolute; top: 0; width: 100%; box-shadow: 0 5px 20px rgba(255,68,68,0.6); }
header { padding: 55px 25px 15px; display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--border); }
nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 90%; height: 72px; background: rgba(15,15,15,0.9); backdrop-filter: blur(20px); border-radius: 28px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
.nav-btn { color: var(--muted); font-size: 22px; cursor: pointer; }
.nav-btn.active { color: var(--p); transform: translateY(-5px); text-shadow: 0 0 15px var(--p); }
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; }
.page.active { display: block; animation: slideUp 0.4s ease; }
@keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
.chat-container { background: rgba(255,255,255,0.02); border-radius: 28px; border: 1px solid var(--border); height: 50vh; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px; }
.bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; background: #151515; max-width: 82%; position: relative; border: 1px solid var(--border); }
.bubble.me { background: var(--p); align-self: flex-end; border-bottom-right-radius: 4px; border: none; }
.dash-card { background: var(--card); border-radius: 24px; padding: 22px; margin-bottom: 15px; border: 1px solid var(--border); }
.magic-btn { width: 100%; padding: 15px; border-radius: 15px; border: none; font-weight: 800; cursor: pointer; font-size: 13px; margin-top:10px; }
#auth { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; }
#auth img { width: 160px; height: 160px; border-radius: 50%; margin-bottom: 25px; border: 2px solid var(--p); box-shadow: 0 0 30px var(--p); }
#maintenanceOverlay { display:none; position:fixed; inset:0; background:#000; z-index:10000; flex-direction:column; align-items:center; justify-content:center; text-align:center; padding:40px; color:white; }
</style>
</head>
<body>
<div id="newsBar"></div>
<div id="maintenanceOverlay">
    <h1 style="color:red; font-size:40px; text-shadow:0 0 20px red;">⚠️ SYSTEM OVERRIDE</h1>
    <p>The core has been locked by Admin. Access Denied.</p>
</div>
<div class="app">
  <header>
    <h1>Live Connect <span id="ghostBadge" style="display:none; color:gold; font-size:10px;">GHOST ✨</span></h1>
    <div onclick="logout()" style="color:var(--muted); cursor:pointer;"><i class="fa-solid fa-power-off"></i></div>
  </header>

  <div id="auth">
    <img src="http://googleusercontent.com/image_collection/image_retrieval/1554879135812125878_1" alt="Hacker">
    <h2 style="margin:0;">Secure Login</h2>
    <input type="text" id="userInput" placeholder="Identity Name" style="width:100%; max-width:300px; padding:18px; border-radius:20px; background:var(--card); border:1px solid var(--border); color:#fff; text-align:center; margin:20px 0;">
    <button onclick="login()" style="width:100%; max-width:300px; padding:18px; border-radius:20px; background:var(--p); border:none; color:#fff; font-weight:800; cursor:pointer;">CONNECT</button>
  </div>

  <div id="home" class="page active">
    <div class="dash-card" style="background: linear-gradient(135deg, #6b5bff, #ff5c8d); border:none;">
        <h2 style="margin:0;">Master <span id="uNameDisp">...</span></h2>
    </div>
    <div id="godConsole" style="display:none;">
        <h4 style="color:gold; font-size:10px;">MASTER CONTROL UNIT</h4>
        <div class="dash-card" style="border: 1px solid gold;">
            <input type="text" id="newsInput" style="width:100%; padding:12px; background:rgba(0,0,0,0.5); border:1px solid var(--border); color:white; border-radius:10px;" placeholder="Broadcast...">
            <button class="magic-btn" style="background:gold;" onclick="broadcastNews()">SEND BROADCAST</button>
            <button id="overrideBtn" class="magic-btn" style="background:purple; color:white;" onclick="toggleOverride()">ACTIVATE OVERRIDE</button>
            <button class="magic-btn" style="background:red; color:white;" onclick="wipeAll()">WIPE DATABASE</button>
        </div>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatWith" style="font-size:11px; font-weight:800; color:var(--p); margin-bottom:12px;">CHANNEL</div>
    <div class="chat-container" id="chatBox"></div>
    <div style="display:flex; gap:10px; padding:10px;">
      <input id="msgInput" style="flex:1; background:var(--card); border:1px solid var(--border); color:white; padding:12px; border-radius:15px;" placeholder="Message..."/>
      <button onclick="sendMsg()" style="background:var(--p); border:none; padding:10px 20px; border-radius:15px; color:white;"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <div id="userList"></div>
    <h4 style="color:var(--muted); font-size:10px; margin-top:20px;">SPY CHANNELS (PRIVATE CHATS)</h4>
    <div id="spyList"></div>
  </div>

  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-ghost"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></div>
    <div class="nav-btn" onclick="openPage('users',this)"><i class="fa-solid fa-user-shield"></i></div>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, get, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");
let role = localStorage.getItem("lc_role") || "user";
let curChat = "", isGroup = false;

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
    } else {
        set(ref(db, "users/"+user), {name: user, online: true});
    }

    onValue(ref(db, "systemState"), snap => {
        if(snap.val()?.locked && role !== "admin") document.getElementById("maintenanceOverlay").style.display = "flex";
        else document.getElementById("maintenanceOverlay").style.display = "none";
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
        if(u.val().online && u.key !== user) {
            const div = document.createElement("div"); div.className = "dash-card";
            div.innerHTML = `<b>${u.val().name}</b>`;
            div.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("chatWith").innerText=u.val().name; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
            list.appendChild(div);
        }
    });
});

// SPY MODE: Private chats dekhne ke liye
if(role === "admin") {
    onValue(ref(db, "chats"), snap => {
        const list = document.getElementById("spyList"); list.innerHTML = "";
        snap.forEach(c => {
            const div = document.createElement("div"); div.className = "dash-card";
            div.innerHTML = `<i class="fa-solid fa-eye" style="color:gold;"></i> Spy Chat: ${c.key}`;
            div.onclick = () => { curChat=c.key; isGroup=false; document.getElementById("chatWith").innerText="Spying: "+c.key; loadChat("spy"); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
            list.appendChild(div);
        });
    });
}

function loadChat(mode="") {
    const path = mode === "spy" ? "chats/" + curChat : (isGroup ? "groupChats/" : "chats/") + [user, curChat].sort().join("_");
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const v = m.val();
            const div = document.createElement("div"); div.className = "bubble " + (v.from === user ? "me" : "them");
            div.innerHTML = `${v.from}: ${v.text}`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.toggleOverride = async () => {
    const s = await get(ref(db, "systemState"));
    set(ref(db, "systemState"), { locked: !s.val()?.locked });
};

window.broadcastNews = () => { set(ref(db, "globalNews"), {text: document.getElementById("newsInput").value}); };
window.wipeAll = () => { remove(ref(db, "chats")); remove(ref(db, "users")); location.reload(); };
window.openPage = (id, btn) => { 
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('.nav-btn').forEach(n => n.classList.remove('active'));
    btn.classList.add('active');
};
window.sendMsg = () => {
    const i = document.getElementById('msgInput');
    const path = "chats/" + [user, curChat].sort().join("_");
    push(ref(db, path), { from: user, text: i.value });
    i.value = '';
};
</script>
</body>
</html>
