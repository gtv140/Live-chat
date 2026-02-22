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
  --text: #ffffff; --muted: #777; --border: rgba(255,255,255,0.08); --glass: rgba(0,0,0,0.6);
}
body.light {
  --bg: #f5f7fb; --card: #ffffff; --text: #0a0a0a; --border: rgba(0,0,0,0.05); --glass: rgba(255,255,255,0.8);
}
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; outline: none; }
body { margin: 0; background: var(--bg); color: var(--text); transition: 0.5s cubic-bezier(0.4, 0, 0.2, 1); overflow: hidden; }

/* Cinematic Background */
.mesh { position: fixed; inset: 0; z-index: -1; background: radial-gradient(circle at 10% 10%, rgba(107,91,255,0.15) 0%, transparent 40%), radial-gradient(circle at 90% 90%, rgba(255,92,141,0.15) 0%, transparent 40%); }

.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Polished Header */
header { padding: 55px 24px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(25px); background: var(--glass); border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 20px; font-weight: 800; letter-spacing: -0.5px; background: linear-gradient(135deg, var(--p), #fff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
.btn-icon { width: 42px; height: 42px; border-radius: 14px; border: 1px solid var(--border); background: var(--card); color: var(--text); cursor: pointer; transition: 0.3s; display: flex; align-items: center; justify-content: center; font-size: 18px; }
.btn-icon:active { transform: scale(0.9); background: var(--p); color: #fff; }

/* HUD Navigation */
nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 90%; height: 75px; background: var(--glass); backdrop-filter: blur(30px); border-radius: 30px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; box-shadow: 0 20px 40px rgba(0,0,0,0.4); }
.nav-btn { color: var(--muted); font-size: 22px; cursor: pointer; transition: 0.4s; position: relative; }
.nav-btn.active { color: var(--p); text-shadow: 0 0 20px var(--p); }
.nav-btn.active::after { content: ''; position: absolute; bottom: -10px; left: 50%; transform: translateX(-50%); width: 5px; height: 5px; background: var(--p); border-radius: 50%; box-shadow: 0 0 10px var(--p); }

/* Pages & Cards */
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; animation: pageIn 0.5s cubic-bezier(0.1, 0.7, 0.1, 1); }
.page.active { display: block; }
@keyframes pageIn { from { opacity: 0; transform: scale(0.98) translateY(10px); } to { opacity: 1; transform: scale(1) translateY(0); } }

.hero-box { background: linear-gradient(135deg, var(--p), var(--a)); padding: 30px; border-radius: 32px; color: #fff; margin-bottom: 25px; box-shadow: 0 20px 40px rgba(107,91,255,0.25); position: relative; overflow: hidden; }
.hero-box::before { content: ''; position: absolute; top: -50%; left: -50%; width: 200%; height: 200%; background: radial-gradient(circle, rgba(255,255,255,0.1) 0%, transparent 70%); }

.stat-tile { background: var(--card); padding: 20px; border-radius: 24px; border: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; transition: 0.3s; }
.stat-tile h4 { margin: 0; font-size: 14px; color: var(--muted); text-transform: uppercase; letter-spacing: 1px; }
.stat-tile b { font-size: 20px; color: var(--a); }

/* Chat Polished */
.chat-container { background: var(--card); border-radius: 28px; border: 1px solid var(--border); height: 50vh; overflow-y: auto; padding: 20px; margin-bottom: 20px; scroll-behavior: smooth; }
.chat-container::-webkit-scrollbar { width: 0; }

.bubble { padding: 14px 18px; border-radius: 22px; margin-bottom: 12px; font-size: 15px; position: relative; background: #151515; border: 1px solid var(--border); max-width: 80%; animation: bubbleIn 0.3s ease; }
.bubble.me { background: var(--p); border: none; align-self: flex-end; margin-left: auto; border-bottom-right-radius: 4px; box-shadow: 0 8px 20px rgba(107,91,255,0.3); }
.bubble.them { border-bottom-left-radius: 4px; }
.bubble img { width: 100%; border-radius: 16px; margin-bottom: 8px; }
.bubble .meta { font-size: 9px; opacity: 0.6; display: block; margin-top: 6px; text-align: right; text-transform: uppercase; }

/* Input Engine */
.dock-bar { display: flex; gap: 12px; align-items: center; background: var(--card); padding: 10px 15px; border-radius: 25px; border: 1px solid var(--border); }
.dock-input { flex: 1; background: transparent; border: none; color: #fff; padding: 10px; font-size: 15px; }
.action-btn { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--p); color: #fff; cursor: pointer; display: flex; align-items: center; justify-content: center; box-shadow: 0 5px 15px var(--p); }
.mic-btn { color: var(--muted); background: none; border: none; font-size: 18px; cursor: pointer; }
.mic-btn.active { color: var(--a); animation: pulse 1s infinite; }
@keyframes pulse { 0% { opacity: 1; } 50% { opacity: 0.4; } 100% { opacity: 1; } }

/* User Tiles */
.user-tile { padding: 18px; background: var(--card); border-radius: 22px; margin-bottom: 12px; display: flex; align-items: center; gap: 15px; border: 1px solid var(--border); cursor: pointer; transition: 0.3s; }
.user-tile:active { transform: scale(0.97); background: rgba(255,255,255,0.08); }
.status-indicator { width: 12px; height: 12px; border-radius: 50%; background: var(--success); box-shadow: 0 0 10px var(--success); border: 2px solid var(--bg); }

/* Authentication */
#authPanel { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; }
.auth-input { width: 100%; max-width: 320px; padding: 20px; background: var(--card); border: 1px solid var(--border); border-radius: 20px; color: #fff; text-align: center; font-size: 18px; margin-bottom: 20px; }
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
    <div style="font-size: 60px; margin-bottom: 20px; color: var(--p); text-shadow: 0 0 30px var(--p);"><i class="fa-solid fa-atom"></i></div>
    <h2 style="margin-bottom: 30px; letter-spacing: 2px;">AUTHENTICATE</h2>
    <input type="text" id="usernameInput" class="auth-input" placeholder="Enter Identity">
    <button onclick="login()" style="width:100%; max-width:320px; padding:20px; border-radius:20px; border:none; background:var(--p); color:#fff; font-weight:800; cursor:pointer;">ACCESS NEXUS</button>
  </div>

  <div id="home" class="page active">
    <div class="hero-box">
      <h2 style="margin:0; font-size:28px;">Hello, <span id="dashUser">...</span></h2>
      <p style="opacity:0.9; margin-top:8px;">Frequency synchronized. Ready for data.</p>
    </div>
    <div class="stat-tile"><h4>Active Terminals</h4><b id="onlineCount">0</b></div>
    <div class="stat-tile"><h4>Global Syncs</h4><b id="groupCount">0</b></div>
  </div>

  <div id="chat" class="page">
    <div id="chatInfo" style="margin-bottom:15px; font-weight:700; font-size:12px; color:var(--p); display:flex; align-items:center; gap:8px;">
        <i class="fa-solid fa-circle-nodes"></i> <span id="targetName">READY TO CONNECT</span>
    </div>
    <div class="chat-container" id="chatBox"></div>
    <div class="dock-bar">
      <label for="imgInp"><i class="fa-solid fa-plus-circle mic-btn" style="color:var(--p)"></i></label>
      <input type="file" id="imgInp" hidden onchange="sendImg(event)">
      <button class="mic-btn" id="mic"><i class="fa-solid fa-microphone"></i></button>
      <input id="msgInput" class="dock-input" placeholder="Type a message..."/>
      <button class="action-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <h4 style="color:var(--muted); font-size:11px; margin-bottom:15px; letter-spacing:2px;">ONLINE NODES</h4>
    <div id="userList"></div>
    <h4 style="color:var(--muted); font-size:11px; margin:25px 0 15px; letter-spacing:2px;">CHANNELS</h4>
    <div id="groupList"></div>
    <div class="dock-bar" style="margin-top:15px;">
        <input id="groupInput" class="dock-input" placeholder="New Channel Identity..."/>
        <button class="action-btn" onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
    </div>
  </div>

  <div id="about" class="page">
    <div class="stat-tile" style="flex-direction:column; align-items:flex-start; gap:10px;">
        <h3 style="color:var(--p); margin:0;">Quantum Hub</h3>
        <p style="font-size:14px; color:var(--muted); margin:0;">Encrypted real-time communication engine by WebHub Technologies.</p>
    </div>
    <div class="user-tile" onclick="window.open('mailto:webhub262@gmail.com')">
        <i class="fa-solid fa-headset" style="color:var(--a)"></i> <b>Direct Support</b>
    </div>
  </div>

  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-house-crack"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-comment-dots"></i></div>
    <div class="nav-btn" onclick="openPage('users',this)"><i class="fa-solid fa-globe"></i></div>
    <div class="nav-btn" onclick="openPage('about',this)"><i class="fa-solid fa-info-circle"></i></div>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("zenith_id");
let curChat = "";
let isGroup = false;

if(currentUser) {
  document.getElementById("authPanel").style.display = "none";
  init();
}

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
  let count = 0;
  const list = document.getElementById("userList"); list.innerHTML = "";
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
        <span class="meta">${new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})} <i class="fa-solid fa-trash" style="margin-left:5px" onclick="deleteMsg('${path}','${m.key}')"></i></span>
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

window.sendImg = (e) => {
  const reader = new FileReader();
  reader.onload = () => {
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    push(ref(db, path), {from: currentUser, img: reader.result, time: Date.now()});
  };
  reader.readAsDataURL(e.target.files[0]);
};

window.deleteMsg = (p, k) => { if(confirm("Erase data?")) remove(ref(db, p+"/"+k)); };

// Mic Simulation
const mic = document.getElementById('mic');
mic.onclick = () => {
    mic.classList.toggle('active');
    if(mic.classList.contains('active')) {
        document.getElementById('msgInput').placeholder = "Listening...";
    } else {
        document.getElementById('msgInput').placeholder = "Type a message...";
    }
};
</script>
</body>
</html>
