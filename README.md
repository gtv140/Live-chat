<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect | Magic Admin</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root { --p: #6b5bff; --a: #ff5c8d; --bg: #030303; --card: rgba(255,255,255,0.06); --text: #ffffff; --muted: #888; --border: rgba(255,255,255,0.1); --glass: rgba(0,0,0,0.85); }
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; outline: none; }
body { margin: 0; background: var(--bg); color: var(--text); overflow: hidden; }
.mesh { position: fixed; inset: 0; z-index: -1; background: radial-gradient(circle at 15% 15%, rgba(107,91,255,0.15) 0%, transparent 45%), radial-gradient(circle at 85% 85%, rgba(255,92,141,0.15) 0%, transparent 45%); filter: blur(30px); }
.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
header { padding: 55px 25px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(25px); background: var(--glass); border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 22px; font-weight: 800; background: linear-gradient(90deg, #fff, var(--p)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 92%; height: 72px; background: rgba(15,15,15,0.8); backdrop-filter: blur(20px); border-radius: 28px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; box-shadow: 0 15px 40px rgba(0,0,0,0.7); }
.nav-btn { color: var(--muted); font-size: 22px; cursor: pointer; transition: 0.4s; }
.nav-btn.active { color: var(--p); transform: translateY(-3px); }
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; animation: fadeIn 0.4s ease; }
.page.active { display: block; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
.chat-container { background: rgba(255,255,255,0.02); border-radius: 30px; border: 1px solid var(--border); height: 52vh; overflow-y: auto; padding: 18px; display: flex; flex-direction: column; gap: 15px; scrollbar-width: none; }
.bubble { padding: 14px 18px; border-radius: 22px; font-size: 14px; background: #1a1a1a; border: 1px solid var(--border); max-width: 82%; position: relative; }
.bubble.me { background: linear-gradient(135deg, var(--p), #4a3aff); border: none; align-self: flex-end; border-bottom-right-radius: 4px; }
.sender-tag { font-size: 11px; font-weight: 800; color: var(--a); margin-bottom: 4px; display: block; }
.dash-card { background: linear-gradient(145deg, #181818, #0a0a0a); border: 1px solid var(--border); border-radius: 28px; padding: 22px; margin-bottom: 15px; }
.dock-bar { display: flex; gap: 10px; align-items: center; background: var(--card); padding: 12px; border-radius: 25px; border: 1px solid var(--border); margin-top: 10px; }
.dock-input { flex: 1; background: transparent; border: none; color: #fff; }
.send-btn { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--p); color: #fff; cursor: pointer; }
#auth { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; text-align: center; }
.magic-btn { background: #ff4444; color: white; border: none; padding: 5px 10px; border-radius: 10px; font-size: 10px; cursor: pointer; margin-top: 5px; }
</style>
</head>
<body>
<div class="mesh"></div>
<div class="app">
  <header>
    <h1>Live Connect <span id="magicBadge" style="display:none; color: gold; font-size: 12px; margin-left: 10px;">✨ MAGICIAN</span></h1>
    <div onclick="logout()" style="color:var(--muted); cursor:pointer;"><i class="fa-solid fa-power-off"></i></div>
  </header>
  
  <div id="auth">
    <div style="font-size: 50px; color: var(--p); margin-bottom: 15px;"><i class="fa-solid fa-wand-magic-sparkles"></i></div>
    <h2>Enter the Portal</h2>
    <input type="text" id="userInput" style="width:100%; max-width:280px; padding:18px; border-radius:20px; background:var(--card); border:1px solid var(--border); color:#fff; text-align:center; margin-bottom:15px;" placeholder="Your Name">
    <button onclick="login()" style="width:100%; max-width:280px; padding:18px; border-radius:20px; background:var(--p); border:none; color:#fff; font-weight:800;">CONNECT</button>
  </div>

  <div id="home" class="page active">
    <div class="dash-card" style="background: linear-gradient(135deg, #7c4dff, #ff4081);">
        <h2 style="margin:0; font-size:28px;">Master <span id="uName">...</span></h2>
        <p id="magicInfo" style="opacity:0.9; margin-top:5px; font-size:14px;">Welcome to your Magic Dashboard.</p>
    </div>
    <div id="adminPanel" style="display:none;" class="dash-card">
        <h4 style="margin:0; color: gold;">MAGICAL TOOLS</h4>
        <p style="font-size: 11px; color: var(--muted);">Click a user in Terminal to BAN them.</p>
    </div>
    <div style="display:grid; grid-template-columns:1fr 1fr; gap:15px;">
        <div class="dash-card" style="text-align:center;"><h3 id="onlineCount" style="margin:0;">0</h3><p style="margin:0; font-size:10px;">ACTIVE NODES</p></div>
        <div class="dash-card" style="text-align:center;"><h3 style="margin:0; color:var(--a);">Locked</h3><p style="margin:0; font-size:10px;">E2E SECURE</p></div>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatWith" style="font-size:11px; font-weight:800; color:var(--p); margin-bottom:12px;">ACTIVE CHANNEL</div>
    <div class="chat-container" id="chatBox"></div>
    <div class="dock-bar">
      <label for="imgInp" style="color:var(--p); cursor:pointer;"><i class="fa-solid fa-image"></i></label>
      <input type="file" id="imgInp" hidden onchange="sendImg(event)">
      <input id="msgInput" class="dock-input" placeholder="Whisper something..."/>
      <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page"><div id="userList"></div><h4 style="font-size:10px; color:var(--muted); margin:25px 0 12px;">GLOBAL CHANNELS</h4><div id="groupList"></div></div>
  
  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-house"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></div>
    <div class="nav-btn" onclick="openPage('users',this)"><i class="fa-solid fa-terminal"></i></div>
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

const MAGIC_KEY = "Admin786"; // Sirf aapko pata hai!

if(user) { checkBanStatus(user); }

window.login = () => {
    const n = document.getElementById("userInput").value.trim();
    if(n) {
        checkBanStatus(n, true);
    }
};

async function checkBanStatus(name, isNew = false) {
    const snapshot = await get(ref(db, "bannedUsers/" + name));
    if (snapshot.exists()) {
        alert("🚨 ACCESS DENIED: Your portal access has been revoked by the High Council.");
        localStorage.clear();
        location.reload();
    } else {
        if(isNew) {
            user = name;
            localStorage.setItem("lc_user", name);
            if(name === MAGIC_KEY) localStorage.setItem("lc_role", "magician");
            else localStorage.setItem("lc_role", "user");
            location.reload();
        }
        document.getElementById("auth").style.display = "none";
        start();
    }
}

function start() {
    document.getElementById("uName").textContent = user;
    if(role === "magician") {
        document.getElementById("magicBadge").style.display = "inline";
        document.getElementById("adminPanel").style.display = "block";
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
        if(d && d.name && d.name !== "undefined" && d.online) {
            count++;
            if(u.key !== user) {
                const div = document.createElement("div"); div.className = "dash-card";
                div.innerHTML = `<b>${d.name}</b>`;
                if(role === "magician") {
                    div.innerHTML += `<br><button class="magic-btn" onclick="banUser('${d.name}')">BAN USER</button>`;
                }
                div.onclick = (e) => { if(e.target.tagName !== 'BUTTON') { curChat=u.key; isGroup=false; document.getElementById("chatWith").innerText=d.name; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); }};
                list.appendChild(div);
            }
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

window.banUser = (name) => {
    if(confirm("Magically ban " + name + " forever?")) {
        set(ref(db, "bannedUsers/" + name), true);
        alert(name + " has been vanished from the portal!");
    }
};

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
                let nTag = isGroup ? `<span class="sender-tag">${v.from}</span>` : "";
                let magicDel = (role === "magician") ? `<i class="fa-solid fa-wand-sparkles" style="color:gold; font-size:10px; margin-left:10px; cursor:pointer;" onclick="deleteMsgMagic('${path}', '${m.key}')"></i>` : "";
                div.innerHTML = `${nTag}${v.img ? `<img src="${v.img}" style="width:100%; border-radius:15px;">` : v.text} ${magicDel}`;
                box.appendChild(div);
            }
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.deleteMsgMagic = (path, msgId) => {
    update(ref(db, path + "/" + msgId), { deleted: true });
};

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
