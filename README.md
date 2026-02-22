<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect | Ultimate</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root { --p: #6b5bff; --a: #ff5c8d; --bg: #030303; --card: rgba(255,255,255,0.06); --text: #ffffff; --muted: #888; --border: rgba(255,255,255,0.1); --glass: rgba(0,0,0,0.85); }
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; outline: none; transition: all 0.2s ease; }
body { margin: 0; background: var(--bg); color: var(--text); overflow: hidden; }
.mesh { position: fixed; inset: 0; z-index: -1; background: radial-gradient(circle at 15% 15%, rgba(107,91,255,0.15) 0%, transparent 45%), radial-gradient(circle at 85% 85%, rgba(255,92,141,0.15) 0%, transparent 45%); filter: blur(30px); }
.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
header { padding: 55px 25px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(25px); background: var(--glass); border-bottom: 1px solid var(--border); z-index: 1001; }
header h1 { margin: 0; font-size: 22px; font-weight: 800; background: linear-gradient(90deg, #fff, var(--p)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 92%; height: 72px; background: rgba(15,15,15,0.85); backdrop-filter: blur(20px); border-radius: 28px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; box-shadow: 0 15px 40px rgba(0,0,0,0.7); }
.nav-btn { color: var(--muted); font-size: 22px; cursor: pointer; }
.nav-btn.active { color: var(--p); transform: translateY(-5px); text-shadow: 0 0 15px var(--p); }
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; animation: fadeIn 0.4s ease; }
.page.active { display: block; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
.chat-container { background: rgba(255,255,255,0.02); border-radius: 30px; border: 1px solid var(--border); height: 52vh; overflow-y: auto; padding: 18px; display: flex; flex-direction: column; gap: 15px; scrollbar-width: none; }
.bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; background: #1a1a1a; border: 1px solid var(--border); max-width: 85%; position: relative; word-wrap: break-word; }
.bubble.me { background: linear-gradient(135deg, var(--p), #4a3aff); border: none; align-self: flex-end; border-bottom-right-radius: 4px; }
.bubble.them { border-bottom-left-radius: 4px; }
.dash-card { background: linear-gradient(145deg, #181818, #0a0a0a); border: 1px solid var(--border); border-radius: 25px; padding: 20px; margin-bottom: 15px; }
.dock-bar { display: flex; gap: 10px; align-items: center; background: var(--card); padding: 10px 15px; border-radius: 25px; border: 1px solid var(--border); margin-top: 10px; }
.dock-input { flex: 1; background: transparent; border: none; color: #fff; font-size: 14px; }
.send-btn { width: 40px; height: 40px; border-radius: 50%; border: none; background: var(--p); color: #fff; cursor: pointer; display: flex; align-items: center; justify-content: center; }
#auth { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; text-align: center; }
#auth input { width: 100%; max-width: 280px; padding: 18px; border-radius: 20px; background: var(--card); border: 1px solid var(--border); color: #fff; text-align: center; margin-bottom: 15px; font-size: 16px; }
.admin-power-btn { background: #ff4444; color: #fff; border: none; padding: 10px; border-radius: 12px; font-size: 11px; font-weight: 800; cursor: pointer; margin-top: 10px; width: 100%; box-shadow: 0 5px 15px rgba(255,68,68,0.3); }
.admin-badge { color: gold; font-size: 10px; border: 1px solid gold; padding: 2px 6px; border-radius: 6px; margin-left: 8px; font-weight: 800; vertical-align: middle; }
</style>
</head>
<body>
<div class="mesh"></div>
<div class="app">
  <header>
    <h1>Live Connect <span id="masterBadge" style="display:none;" class="admin-badge">MASTER</span></h1>
    <div onclick="logout()" style="color:var(--muted); cursor:pointer;"><i class="fa-solid fa-power-off"></i></div>
  </header>

  <div id="auth">
    <div style="font-size: 60px; color: var(--p); margin-bottom: 20px;"><i class="fa-solid fa-bolt"></i></div>
    <h2 style="margin:0;">Secure Access</h2>
    <p style="color: var(--muted); font-size: 14px; margin: 10px 0 25px;">Connecting to private node...</p>
    <input type="text" id="userInput" placeholder="Identity Name">
    <button onclick="login()" style="width:100%; max-width:280px; padding:18px; border-radius:20px; background:var(--p); border:none; color:#fff; font-weight:800; cursor:pointer;">START ENCRYPTION</button>
  </div>

  <div id="home" class="page active">
    <div class="dash-card" style="background: linear-gradient(135deg, #6b5bff, #ff5c8d); border:none;">
        <h2 style="margin:0; font-size:28px;">Master <span id="uNameDisplay">...</span></h2>
        <p id="roleText" style="opacity:0.9; margin-top:5px; font-size:14px;">Node Status: Secure & Active</p>
    </div>
    
    <div id="adminMagicBox" style="display:none;">
        <h4 style="color:gold; font-size:10px; letter-spacing:2px; margin-bottom:10px; font-weight:800;">GOD MODE CONSOLE</h4>
        <div class="dash-card" style="border: 1px solid gold;">
            <p style="font-size:12px; margin:0 0 10px; opacity:0.7;">System Maintenance Tools</p>
            <button class="admin-power-btn" onclick="masterWipe()">WIPE ALL CHATS</button>
            <button class="admin-power-btn" style="background:#333;" onclick="masterUnban()">RESET BAN LIST</button>
        </div>
    </div>

    <div style="display:grid; grid-template-columns:1fr 1fr; gap:15px;">
        <div class="dash-card" style="text-align:center;"><h3 id="onlineCount" style="margin:0; font-size:24px;">0</h3><p style="margin:0; font-size:10px; color:var(--muted); font-weight:800;">LIVE NODES</p></div>
        <div class="dash-card" style="text-align:center;"><h3 style="margin:0; font-size:24px; color:var(--a);">Locked</h3><p style="margin:0; font-size:10px; color:var(--muted); font-weight:800;">FIREWALL</p></div>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatWith" style="font-size:11px; font-weight:800; color:var(--p); margin-bottom:12px; letter-spacing:1px; text-transform:uppercase;">PRIVATE TUNNEL</div>
    <div class="chat-container" id="chatBox"></div>
    <div class="dock-bar">
      <label for="imgInp" style="color:var(--p); cursor:pointer; font-size:18px;"><i class="fa-solid fa-plus-circle"></i></label>
      <input type="file" id="imgInp" hidden onchange="sendImg(event)">
      <input id="msgInput" class="dock-input" placeholder="Whisper something..."/>
      <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <div id="userList"></div>
    <h4 style="font-size:10px; color:var(--muted); margin:30px 0 12px; font-weight:800; letter-spacing:1px;">PUBLIC CHANNELS</h4>
    <div id="groupList"></div>
  </div>
  
  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-house-chimney"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-comment-dots"></i></div>
    <div class="nav-btn" onclick="openPage('users',this)"><i class="fa-solid fa-user-astronaut"></i></div>
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

const MASTER_KEY = "Admin786"; 

if(user) checkBan(user);

window.login = () => {
    const n = document.getElementById("userInput").value.trim();
    if(n) checkBan(n, true);
};

async function checkBan(name, isNew = false) {
    const s = await get(ref(db, "bannedList/" + name));
    if (s.exists()) {
        alert("🚨 ACCESS DENIED: Connection refused by Server.");
        localStorage.clear(); location.reload();
    } else {
        if(isNew) {
            user = name; localStorage.setItem("lc_user", name);
            role = (name === MASTER_KEY) ? "admin" : "user";
            localStorage.setItem("lc_role", role);
            location.reload();
        }
        document.getElementById("auth").style.display = "none";
        setupApp();
    }
}

function setupApp() {
    document.getElementById("uNameDisplay").textContent = user;
    if(role === "admin") {
        document.getElementById("masterBadge").style.display = "inline";
        document.getElementById("adminMagicBox").style.display = "block";
        document.getElementById("roleText").textContent = "Superuser Protocol Active.";
    }
    set(ref(db, "users/"+user), {name: user, online: true});
    onDisconnect(ref(db, "users/"+user)).update({online: false});
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
        if(d && d.name && d.online) {
            count++;
            if(u.key !== user) {
                const div = document.createElement("div"); div.className = "dash-card";
                div.style.display = "flex"; div.style.justifyContent = "space-between"; div.style.alignItems = "center";
                div.innerHTML = `<b>${d.name}</b>`;
                if(role === "admin") {
                    const bBtn = document.createElement("button"); bBtn.className = "admin-power-btn";
                    bBtn.style.width = "auto"; bBtn.style.margin = "0"; bBtn.innerText = "BAN";
                    bBtn.onclick = (e) => { e.stopPropagation(); ban(d.name); };
                    div.appendChild(bBtn);
                }
                div.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("chatWith").innerText=d.name; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
                list.appendChild(div);
            }
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

window.ban = (name) => { if(confirm("Banish " + name + " from the network?")) set(ref(db, "bannedList/" + name), true); };
window.masterWipe = () => { if(confirm("ERASE ALL SECURE DATA?")) { remove(ref(db, "chats")); remove(ref(db, "groupChats")); alert("Data Wiped."); }};
window.masterUnban = () => { if(confirm("Reset ban list?")) { remove(ref(db, "bannedList")); alert("All restrictions lifted."); }};

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
                let magicDel = (role === "admin") ? `<i class="fa-solid fa-wand-magic-sparkles" style="color:gold; margin-left:10px; cursor:pointer;" onclick="deleteMagic('${path}', '${m.key}')"></i>` : "";
                div.innerHTML = `${isGroup?`<small style="display:block; opacity:0.7; font-size:10px;">${v.from}</small>`:''}${v.img ? `<img src="${v.img}" style="width:100%; border-radius:15px; margin-top:5px;">` : v.text} ${magicDel}`;
                box.appendChild(div);
            }
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.deleteMagic = (path, id) => { update(ref(db, path + "/" + id), { deleted: true }); };

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
