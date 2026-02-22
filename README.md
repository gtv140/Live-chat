<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect | God Mode</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root { --p: #6b5bff; --a: #ff5c8d; --bg: #030303; --card: rgba(255,255,255,0.08); --text: #ffffff; --muted: #888; --border: rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; outline: none; transition: 0.3s ease; }
body { margin: 0; background: var(--bg); color: var(--text); overflow: hidden; }
.mesh { position: fixed; inset: 0; z-index: -1; background: radial-gradient(circle at 10% 10%, rgba(107,91,255,0.2) 0%, transparent 40%), radial-gradient(circle at 90% 90%, rgba(255,92,141,0.2) 0%, transparent 40%); filter: blur(40px); }
.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* NEWS BAR */
#newsBar { display: none; background: linear-gradient(90deg, #ff4444, #ff8c00); color: white; padding: 10px; font-size: 12px; font-weight: 800; text-align: center; z-index: 2000; position: absolute; top: 0; width: 100%; box-shadow: 0 5px 15px rgba(255,68,68,0.5); border-bottom: 1px solid rgba(255,255,255,0.2); animation: slideDown 0.5s ease; }
@keyframes slideDown { from { transform: translateY(-100%); } to { transform: translateY(0); } }

header { padding: 55px 25px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(20px); border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 20px; font-weight: 800; letter-spacing: -1px; }

nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 90%; height: 70px; background: rgba(20,20,20,0.9); backdrop-filter: blur(15px); border-radius: 25px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
.nav-btn { color: var(--muted); font-size: 20px; cursor: pointer; }
.nav-btn.active { color: var(--p); transform: translateY(-5px); text-shadow: 0 0 10px var(--p); }

.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; }
.page.active { display: block; animation: slideUp 0.4s ease; }
@keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

.chat-container { background: rgba(255,255,255,0.02); border-radius: 25px; border: 1px solid var(--border); height: 50vh; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px; scrollbar-width: none; }
.bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; background: #1a1a1a; max-width: 80%; position: relative; border: 1px solid rgba(255,255,255,0.05); }
.bubble.me { background: var(--p); align-self: flex-end; border-bottom-right-radius: 4px; border: none; }
.bubble.them { border-bottom-left-radius: 4px; }

.dash-card { background: var(--card); border-radius: 22px; padding: 20px; margin-bottom: 15px; border: 1px solid var(--border); }
.admin-badge { color: gold; font-size: 10px; border: 1px solid gold; padding: 2px 6px; border-radius: 6px; margin-left: 8px; font-weight: 800; }
.magic-input { width: 100%; padding: 12px; border-radius: 12px; background: rgba(0,0,0,0.5); border: 1px solid var(--border); color: white; margin-bottom: 10px; font-size: 13px; }
.magic-btn { width: 100%; padding: 12px; border-radius: 12px; border: none; font-weight: 800; cursor: pointer; font-size: 12px; text-transform: uppercase; }

#auth { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; }
#auth input { width: 100%; max-width: 280px; padding: 18px; border-radius: 20px; background: var(--card); border: 1px solid var(--border); color: #fff; text-align: center; margin-bottom: 15px; font-size: 16px; }

.kick-btn { background: #ff4444; color: white; border: none; padding: 6px 10px; border-radius: 8px; font-size: 10px; font-weight: 800; cursor: pointer; }
</style>
</head>
<body>
<div id="newsBar"></div>
<div class="mesh"></div>
<div class="app">
  <header>
    <h1>Live Connect <span id="ghostBadge" style="display:none;" class="admin-badge">GHOST MODE ✨</span></h1>
    <div onclick="logout()" style="color:var(--muted); cursor:pointer;"><i class="fa-solid fa-power-off"></i></div>
  </header>

  <div id="auth">
    <div style="font-size: 70px; color: var(--p); margin-bottom: 20px; filter: drop-shadow(0 0 20px var(--p));"><i class="fa-solid fa-user-secret"></i></div>
    <h2 style="margin:0; letter-spacing: -1px;">System Access</h2>
    <p style="color: var(--muted); font-size: 13px; margin: 10px 0 30px;">Initialize Secure Connection</p>
    <input type="text" id="userInput" placeholder="Enter Identity Name">
    <button onclick="login()" style="width:100%; max-width:280px; padding:18px; border-radius:20px; background:var(--p); border:none; color:#fff; font-weight:800; cursor:pointer; box-shadow: 0 10px 20px rgba(107,91,255,0.3);">LOGIN TO NODE</button>
  </div>

  <div id="home" class="page active">
    <div class="dash-card" style="background: linear-gradient(135deg, #6b5bff, #ff5c8d); border:none; box-shadow: 0 15px 30px rgba(107,91,255,0.2);">
        <h2 style="margin:0; font-size: 26px;">Master <span id="uNameDisp">...</span></h2>
        <p id="roleNote" style="opacity:0.8; font-size:14px; margin-top: 5px;">Secure Node Protocol Active.</p>
    </div>

    <div id="godConsole" style="display:none;">
        <h4 style="color:gold; font-size:10px; letter-spacing:2px; margin-bottom:10px; font-weight:800;">GOD MODE CONSOLE</h4>
        <div class="dash-card" style="border: 1px solid gold;">
            <input type="text" id="newsInput" class="magic-input" placeholder="Broadcast message to everyone...">
            <button class="magic-btn" style="background:gold; color:black;" onclick="broadcastNews()">EXECUTE BROADCAST</button>
            <button class="magic-btn" style="background:#333; color:white; margin-top:8px;" onclick="stopNews()">TERMINATE NEWS</button>
            <hr style="opacity:0.1; margin: 15px 0;">
            <button class="magic-btn" style="background:red; color:white;" onclick="wipeAll()">WIPE DATABASE</button>
        </div>
    </div>

    <div style="display:grid; grid-template-columns:1fr 1fr; gap:12px;">
        <div class="dash-card" style="text-align:center;"><h3 id="onlineCount" style="margin:0; color:var(--p);">0</h3><p style="margin:0; font-size:10px; opacity:0.6;">LIVE USERS</p></div>
        <div class="dash-card" style="text-align:center;"><h3 style="margin:0; color:var(--a);">99.9%</h3><p style="margin:0; font-size:10px; opacity:0.6;">UPTIME</p></div>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatWith" style="font-size:11px; font-weight:800; color:var(--p); margin-bottom:12px; letter-spacing:1px;">SECURE CHANNEL</div>
    <div class="chat-container" id="chatBox"></div>
    <div class="dock-bar">
      <label for="imgInp" style="color:var(--p); cursor:pointer; font-size:22px;"><i class="fa-solid fa-circle-plus"></i></label>
      <input type="file" id="imgInp" hidden onchange="sendImg(event)">
      <input id="msgInput" class="dock-input" placeholder="Whisper to the void..."/>
      <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <div id="userList"></div>
    <h4 style="font-size:10px; color:var(--muted); margin:25px 0 12px; letter-spacing:1px; font-weight:800;">GLOBAL NETWORKS</h4>
    <div id="groupList"></div>
  </div>
  
  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-house-user"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-message"></i></div>
    <div class="nav-btn" onclick="openPage('users',this)"><i class="fa-solid fa-shield-halved"></i></div>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect, update, get } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");
let role = localStorage.getItem("lc_role") || "user";
let curChat = "", isGroup = false;

// Aapki Secret Key
const GOD_KEY = "Admin786";

if(user) checkAccess(user);

window.login = () => {
    const n = document.getElementById("userInput").value.trim();
    if(n) checkAccess(n, true);
};

async function checkAccess(name, isNew = false) {
    const s = await get(ref(db, "bannedList/" + name));
    if (s.exists()) { 
        alert("🚨 ACCESS DENIED: Your node has been permanently blacklisted."); 
        localStorage.clear(); location.reload(); 
    } else {
        if(isNew) {
            user = name; localStorage.setItem("lc_user", name);
            role = (name === GOD_KEY) ? "admin" : "user";
            localStorage.setItem("lc_role", role);
            location.reload();
        }
        document.getElementById("auth").style.display = "none";
        boot();
    }
}

function boot() {
    document.getElementById("uNameDisp").textContent = user;
    if(role === "admin") {
        document.getElementById("ghostBadge").style.display = "inline";
        document.getElementById("godConsole").style.display = "block";
        document.getElementById("roleNote").textContent = "Ghost Mode Active: You are invisible to the public.";
    } else {
        // Only public users are visible
        set(ref(db, "users/"+user), {name: user, online: true});
        onDisconnect(ref(db, "users/"+user)).update({online: false});
    }

    // Global News Hook
    onValue(ref(db, "globalNews"), snap => {
        const bar = document.getElementById("newsBar");
        if(snap.exists() && snap.val().text) {
            bar.innerText = "🚨 SYSTEM ALERT: " + snap.val().text;
            bar.style.display = "block";
        } else { bar.style.display = "none"; }
    });
}

window.logout = () => { localStorage.clear(); location.reload(); };
window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
};

onValue(ref(db, "users"), snap => {
    const list = document.getElementById("userList"); list.innerHTML = "";
    let count = 0;
    snap.forEach(u => {
        const d = u.val();
        if(d && d.online) {
            count++;
            if(u.key !== user) {
                const div = document.createElement("div"); div.className = "dash-card";
                div.style.display="flex"; div.style.justifyContent="space-between"; div.style.alignItems="center";
                div.innerHTML = `<b>${d.name}</b>`;
                if(role === "admin") {
                    const kBtn = document.createElement("button"); kBtn.className = "kick-btn"; kBtn.innerText = "KICK";
                    kBtn.onclick = (e) => { e.stopPropagation(); if(confirm("Ban "+d.name+"?")) set(ref(db, "bannedList/"+d.name), true); };
                    div.appendChild(kBtn);
                }
                div.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("chatWith").innerText=d.name; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
                list.appendChild(div);
            }
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

// ADMIN MAGIC
window.broadcastNews = () => { const t = document.getElementById("newsInput").value; if(t) set(ref(db, "globalNews"), {text: t}); };
window.stopNews = () => { remove(ref(db, "globalNews")); };
window.wipeAll = () => { if(confirm("DESTROY ALL CHATS?")) { remove(ref(db, "chats")); remove(ref(db, "groupChats")); alert("Wipe Complete."); }};

onValue(ref(db, "groups"), snap => {
    const list = document.getElementById("groupList"); list.innerHTML = "";
    snap.forEach(g => {
        const div = document.createElement("div"); div.className = "dash-card";
        div.innerHTML = `<i class="fa-solid fa-hashtag" style="color:var(--p);"></i> <b>${g.key}</b>`;
        div.onclick = () => { curChat=g.key; isGroup=true; document.getElementById("chatWith").innerText="Group: "+g.key; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
        list.appendChild(div);
    });
});

function loadChat() {
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [user, curChat].sort().join("_"));
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const v = m.val();
            if(!v.deleted) {
                const isMe = v.from === user;
                const div = document.createElement("div"); div.className = "bubble " + (isMe ? "me" : "them");
                let wand = (role === "admin") ? `<i class="fa-solid fa-wand-sparkles" style="color:gold; font-size:10px; margin-left:10px; cursor:pointer;" onclick="deleteMsg('${path}', '${m.key}')"></i>` : "";
                div.innerHTML = `${isGroup?`<small style="display:block; font-size:9px; opacity:0.6; font-weight:800; color:var(--a);">${v.from}</small>`:''}${v.img ? `<img src="${v.img}" style="width:100%; border-radius:15px; margin-top:5px;">` : v.text} ${wand}`;
                box.appendChild(div);
            }
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.deleteMsg = (p, id) => { update(ref(db, p + "/" + id), { deleted: true }); };

window.sendMsg = () => {
    const i = document.getElementById('msgInput'); if(!i.value || !curChat) return;
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [user, curChat].sort().join("_"));
    push(ref(db, path), { from: user, text: i.value, time: Date.now() });
    i.value = '';
};

window.sendImg = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [user, curChat].sort().join("_"));
        push(ref(db, path), { from: user, img: reader.result, time: Date.now() });
    };
    reader.readAsDataURL(e.target.files[0]);
};
</script>
</body>
</html>
