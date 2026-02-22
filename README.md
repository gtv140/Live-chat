<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect: Zenith Pro</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root {
  --p: #6b5bff; --a: #ff5c8d; --bg: #030303; --card: rgba(255,255,255,0.04);
  --text: #ffffff; --muted: #777; --border: rgba(255,255,255,0.08); --glass: rgba(0,0,0,0.8);
}
body.light {
  --bg: #f5f7fb; --card: #ffffff; --text: #0a0a0a; --border: rgba(0,0,0,0.05); --glass: rgba(255,255,255,0.8);
}
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; outline: none; }
body { margin: 0; background: var(--bg); color: var(--text); transition: 0.5s; overflow: hidden; }

/* Cinematic Background */
.mesh { position: fixed; inset: 0; z-index: -1; background: radial-gradient(circle at 10% 10%, rgba(107,91,255,0.15) 0%, transparent 40%), radial-gradient(circle at 90% 90%, rgba(255,92,141,0.15) 0%, transparent 40%); }

.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Polished Header */
header { padding: 55px 24px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(25px); background: var(--glass); border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 20px; font-weight: 800; background: linear-gradient(135deg, var(--p), #fff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
.btn-icon { width: 42px; height: 42px; border-radius: 14px; border: 1px solid var(--border); background: var(--card); color: var(--text); cursor: pointer; display: flex; align-items: center; justify-content: center; }

/* Navigation */
nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 90%; height: 75px; background: var(--glass); backdrop-filter: blur(30px); border-radius: 30px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; box-shadow: 0 20px 40px rgba(0,0,0,0.4); }
.nav-btn { color: var(--muted); font-size: 22px; cursor: pointer; transition: 0.4s; position: relative; }
.nav-btn.active { color: var(--p); text-shadow: 0 0 15px var(--p); }

/* Pages */
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; animation: pageIn 0.5s ease; }
.page.active { display: block; }
@keyframes pageIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

/* Cards */
.hero-box { background: linear-gradient(135deg, var(--p), var(--a)); padding: 30px; border-radius: 32px; color: #fff; margin-bottom: 25px; box-shadow: 0 20px 40px rgba(107,91,255,0.25); }
.stat-tile { background: var(--card); padding: 20px; border-radius: 24px; border: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; }

/* Chat UI */
.chat-container { background: var(--card); border-radius: 28px; border: 1px solid var(--border); height: 50vh; overflow-y: auto; padding: 20px; margin-bottom: 20px; display: flex; flex-direction: column; gap: 10px; }
.bubble { padding: 14px 18px; border-radius: 22px; font-size: 15px; background: #151515; border: 1px solid var(--border); max-width: 80%; }
.bubble.me { background: var(--p); border: none; align-self: flex-end; border-bottom-right-radius: 4px; box-shadow: 0 8px 20px rgba(107,91,255,0.3); }
.bubble img { width: 100%; border-radius: 16px; margin-top: 8px; display: block; cursor: pointer; }

/* Improved Input Dock */
.dock-bar { display: flex; gap: 12px; align-items: center; background: var(--card); padding: 8px 15px; border-radius: 25px; border: 1px solid var(--border); }
.dock-input { flex: 1; background: transparent; border: none; color: #fff; padding: 12px; font-size: 15px; }
.action-btn { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--p); color: #fff; cursor: pointer; display: flex; align-items: center; justify-content: center; box-shadow: 0 5px 15px var(--p); }
.file-label { cursor: pointer; color: var(--p); font-size: 20px; display: flex; align-items: center; }

/* User Tiles */
.user-tile { padding: 18px; background: var(--card); border-radius: 22px; margin-bottom: 12px; display: flex; align-items: center; gap: 15px; border: 1px solid var(--border); cursor: pointer; }
.status-indicator { width: 12px; height: 12px; border-radius: 50%; background: var(--success); box-shadow: 0 0 10px var(--success); }

/* Auth */
#authPanel { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; }
.auth-input { width: 100%; max-width: 320px; padding: 20px; background: var(--card); border: 1px solid var(--border); border-radius: 20px; color: #fff; text-align: center; margin-bottom: 20px; }
</style>
</head>
<body>

<div class="mesh"></div>

<div class="app">
  <header>
    <h1>ZENITH PRO</h1>
    <div style="display:flex; gap:12px;">
      <button class="btn-icon" onclick="toggleTheme()"><i class="fa-solid fa-wand-magic-sparkles"></i></button>
      <button class="btn-icon" onclick="logout()"><i class="fa-solid fa-door-open"></i></button>
    </div>
  </header>

  <div id="authPanel">
    <i class="fa-solid fa-bolt" style="font-size: 50px; color: var(--p); margin-bottom: 20px;"></i>
    <input type="text" id="usernameInput" class="auth-input" placeholder="Identity Name">
    <button onclick="login()" style="width:100%; max-width:320px; padding:20px; border-radius:20px; border:none; background:var(--p); color:#fff; font-weight:800; cursor:pointer;">ACCESS NEXUS</button>
  </div>

  <div id="home" class="page active">
    <div class="hero-box">
      <h2 style="margin:0;">Greetings, <span id="dashUser">...</span></h2>
      <p style="opacity:0.9; margin-top:8px;">System synchronized. Ready.</p>
    </div>
    <div class="stat-tile"><h4>Active Nodes</h4><b id="onlineCount">0</b></div>
    <div class="stat-tile"><h4>Channels</h4><b id="groupCount">0</b></div>
  </div>

  <div id="chat" class="page">
    <div style="margin-bottom:12px; font-weight:700; font-size:12px; color:var(--p);"><i class="fa-solid fa-circle-nodes"></i> <span id="targetName">SELECT CHAT</span></div>
    <div class="chat-container" id="chatBox"></div>
    <div class="dock-bar">
      <label for="imgInp" class="file-label">
        <i class="fa-solid fa-camera-retro"></i>
      </label>
      <input type="file" id="imgInp" hidden accept="image/*" onchange="sendImg(event)">
      
      <input id="msgInput" class="dock-input" placeholder="Type message..."/>
      <button class="action-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <h4 style="color:var(--muted); font-size:11px; margin-bottom:15px; letter-spacing:2px;">ONLINE</h4>
    <div id="userList"></div>
    <h4 style="color:var(--muted); font-size:11px; margin:25px 0 15px; letter-spacing:2px;">CHANNELS</h4>
    <div id="groupList"></div>
    <div class="dock-bar" style="margin-top:15px;">
        <input id="groupInput" class="dock-input" placeholder="New Channel Name..."/>
        <button class="action-btn" onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
    </div>
  </div>

  <div id="about" class="page">
    <div class="stat-tile" style="flex-direction:column; align-items:flex-start; gap:10px;">
        <h3 style="color:var(--p); margin:0;">Quantum Hub</h3>
        <p style="font-size:14px; color:var(--muted); margin:0;">Encrypted communication engine by WebHub Technologies.</p>
    </div>
    <div class="user-tile" onclick="window.open('mailto:webhub262@gmail.com')">
        <i class="fa-solid fa-headset" style="color:var(--a)"></i> <b>Official Support</b>
    </div>
  </div>

  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-house"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></div>
    <div class="nav-btn" onclick="openPage('users',this)"><i class="fa-solid fa-compass"></i></div>
    <div class="nav-btn" onclick="openPage('about',this)"><i class="fa-solid fa-circle-info"></i></div>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("zenith_id");
let curChat = "";
let isGroup = false;

if(currentUser) { document.getElementById("authPanel").style.display = "none"; init(); }

window.login = () => {
  const n = document.getElementById("usernameInput").value.trim();
  if(!n) return;
  currentUser = n;
  localStorage.setItem("zenith_id", n);
  init();
  document.getElementById("authPanel").style.display = "none";
};

function init() {
  document.getElementById("dashUser").textContent = currentUser;
  set(ref(db, "users/"+currentUser), {name: currentUser, online: true});
  onDisconnect(ref(db, "users/"+currentUser)).update({online: false});
}

window.logout = () => {
  set(ref(db, "users/"+currentUser), {online: false});
  localStorage.removeItem("zenith_id");
  location.reload();
};

window.openPage = (id, btn) => {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
};

window.toggleTheme = () => document.body.classList.toggle('light');

onValue(ref(db, "users"), snap => {
  let count = 0; const list = document.getElementById("userList"); list.innerHTML = "";
  snap.forEach(u => {
    if(u.val().online) {
      count++;
      if(u.key !== currentUser) {
        const d = document.createElement("div"); d.className = "user-tile";
        d.innerHTML = `<div class="status-indicator"></div><b>${u.val().name}</b>`;
        d.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("targetName").innerText = u.key; loadChat(); openPage('chat', document.querySelector('.nav-btn:nth-child(2)')); };
        list.appendChild(d);
      }
    }
  });
  document.getElementById("onlineCount").textContent = count;
});

onValue(ref(db, "groups"), snap => {
  const list = document.getElementById("groupList"); list.innerHTML = "";
  document.getElementById("groupCount").textContent = snap.size || 0;
  snap.forEach(g => {
    const d = document.createElement("div"); d.className = "user-tile";
    d.innerHTML = `<i class="fa-solid fa-hashtag" style="color:var(--p)"></i><b>${g.key}</b>`;
    d.onclick = () => { curChat=g.key; isGroup=true; document.getElementById("targetName").innerText = g.key; loadChat(); openPage('chat', document.querySelector('.nav-btn:nth-child(2)')); };
    list.appendChild(d);
  });
});

window.createGroup = () => {
  const g = document.getElementById("groupInput").value.trim();
  if(g) { set(ref(db, "groups/"+g), {createdBy: currentUser}); document.getElementById("groupInput").value=''; }
};

function loadChat() {
  const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
  onValue(ref(db, path), snap => {
    const box = document.getElementById("chatBox"); box.innerHTML = "";
    snap.forEach(m => {
      const isMe = m.val().from === currentUser;
      const div = document.createElement("div"); div.className = "bubble " + (isMe ? "me" : "them");
      div.innerHTML = `
        <b style="font-size:10px; opacity:0.7;">${m.val().from}</b><br>
        ${m.val().img ? `<img src="${m.val().img}">` : m.val().text}
        <div style="font-size:9px; margin-top:5px; opacity:0.6; text-align:right;">
          ${new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}
          <i class="fa-solid fa-trash" style="margin-left:8px; cursor:pointer;" onclick="deleteMsg('${path}','${m.key}')"></i>
        </div>
      `;
      box.appendChild(div);
    });
    box.scrollTop = box.scrollHeight;
  });
}

window.sendMsg = () => {
  const i = document.getElementById('msgInput');
  if(!i.value || !curChat) return;
  const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
  push(ref(db, path), {from: currentUser, text: i.value, time: Date.now()});
  i.value = '';
};

// --- FIX: IMAGE SENDING LOGIC ---
window.sendImg = (e) => {
  const file = e.target.files[0];
  if (!file || !curChat) return;
  
  const reader = new FileReader();
  reader.onload = () => {
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    push(ref(db, path), {
      from: currentUser, 
      img: reader.result, 
      time: Date.now()
    });
    // Clear the input so same file can be sent again
    e.target.value = "";
  };
  reader.readAsDataURL(file);
};

window.deleteMsg = (p, k) => { if(confirm("Delete message?")) remove(ref(db, p+"/"+k)); };
</script>
</body>
</html>
