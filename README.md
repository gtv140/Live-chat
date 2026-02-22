<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect Pro 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root {
  --bg: #050505; --card: rgba(255,255,255,0.06); --primary: #6b5bff;
  --accent: #ff5c8d; --success: #22c55e; --text: #ffffff; --border: rgba(255,255,255,0.1);
}
body.light {
  --bg: #f0f2f5; --card: #ffffff; --text: #111; --border: rgba(0,0,0,0.05);
}
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
body { margin: 0; background: var(--bg); color: var(--text); transition: 0.4s; overflow: hidden; }

/* Dynamic Background Glow */
.bg-pulse { position: fixed; top: -10%; left: -10%; width: 300px; height: 300px; background: var(--primary); filter: blur(150px); opacity: 0.2; z-index: -1; animation: move 8s infinite alternate; }
@keyframes move { from { transform: translate(0,0); } to { transform: translate(100%, 50%); } }

.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Header */
header { padding: 50px 20px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(20px); border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 22px; font-weight: 800; background: linear-gradient(90deg, var(--primary), var(--accent)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
header button { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 8px 12px; border-radius: 12px; cursor: pointer; transition: 0.3s; }

/* Navigation */
nav { position: absolute; bottom: 20px; left: 50%; transform: translateX(-50%); width: 90%; height: 70px; background: rgba(0,0,0,0.8); backdrop-filter: blur(20px); border-radius: 25px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
nav button { background: none; border: none; font-size: 20px; color: #555; cursor: pointer; transition: 0.3s; }
nav button.active { color: var(--primary); transform: translateY(-5px); text-shadow: 0 0 10px var(--primary); }

/* Pages */
.page { display: none; padding: 16px; flex: 1; overflow-y: auto; padding-bottom: 100px; animation: slideUp 0.4s ease; }
.page.active { display: block; }
@keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

/* Modern Components */
.hero { background: linear-gradient(135deg, var(--accent), var(--primary)); padding: 25px; border-radius: 24px; color: #fff; text-align: center; box-shadow: 0 15px 30px rgba(107,91,255,0.3); margin-bottom: 20px; }
.stat-card { background: var(--card); padding: 18px; border-radius: 20px; border: 1px solid var(--border); display: flex; justify-content: space-between; margin-bottom: 12px; backdrop-filter: blur(10px); }
.stat-card h4 { margin: 0; color: var(--accent); font-weight: 600; }

/* Chat UI */
.chat-window { background: var(--card); border-radius: 24px; padding: 15px; height: 48vh; overflow-y: auto; border: 1px solid var(--border); margin-bottom: 15px; }
.msg { padding: 12px 16px; border-radius: 20px; margin-bottom: 10px; font-size: 14px; position: relative; background: rgba(255,255,255,0.05); border: 1px solid var(--border); max-width: 85%; line-height: 1.5; }
.msg.me { background: var(--primary); align-self: flex-end; margin-left: auto; border: none; box-shadow: 0 5px 15px rgba(107,91,255,0.3); }
.msg img { width: 100%; border-radius: 12px; margin-top: 8px; }
.msg-info { font-size: 10px; opacity: 0.7; margin-top: 5px; display: block; text-align: right; }

/* Input Bar */
.input-row { display: flex; gap: 10px; align-items: center; }
.input-row input { flex: 1; padding: 16px; border-radius: 18px; border: 1px solid var(--border); background: var(--card); color: #fff; outline: none; }
.input-row button { width: 55px; height: 55px; border-radius: 18px; border: none; background: var(--primary); color: #fff; font-size: 20px; cursor: pointer; box-shadow: 0 5px 15px var(--primary); }

/* List Items */
.list-item { display: flex; align-items: center; gap: 12px; padding: 15px; background: var(--card); border-radius: 18px; margin-bottom: 10px; border: 1px solid var(--border); cursor: pointer; transition: 0.3s; }
.list-item:hover { background: rgba(255,255,255,0.1); transform: translateX(5px); }
.online-dot { width: 10px; height: 10px; background: var(--success); border-radius: 50%; box-shadow: 0 0 10px var(--success); }

/* Login */
#loginPage { position: fixed; inset: 0; background: var(--bg); z-index: 2000; display: flex; align-items: center; justify-content: center; padding: 25px; }
.login-box { width: 100%; background: var(--card); padding: 35px; border-radius: 30px; border: 1px solid var(--border); text-align: center; }
.login-box input { width: 100%; padding: 18px; margin: 20px 0; border-radius: 15px; border: 1px solid var(--border); background: #000; color: #fff; text-align: center; outline: none; }
.login-box button { width: 100%; padding: 18px; border-radius: 15px; border: none; background: var(--primary); color: #fff; font-weight: 800; cursor: pointer; }
</style>
</head>
<body>

<div class="bg-pulse"></div>

<div class="app">
  <header>
    <h1>LIVE CONNECT</h1>
    <div style="display:flex; gap:10px;">
      <button onclick="toggleTheme()"><i class="fa-solid fa-circle-half-stroke"></i></button>
      <button onclick="logout()"><i class="fa-solid fa-power-off"></i></button>
    </div>
  </header>

  <div id="loginPage">
    <div class="login-box">
      <i class="fa-solid fa-shield-cat" style="font-size: 50px; color: var(--primary);"></i>
      <h2 style="margin-top:15px;">Initialize Node</h2>
      <input type="text" id="usernameInput" placeholder="Enter Quantum ID">
      <button onclick="login()">CONNECT</button>
    </div>
  </div>

  <div id="home" class="page">
    <div class="hero">
      <h2 style="margin:0;">Greetings, <span id="dashUser"></span></h2>
      <p style="opacity:0.8; font-size:14px; margin-top:5px;">System status: Operational 🚀</p>
    </div>
    <div class="stat-card"><h4>Network Nodes</h4><span id="onlineCount">0</span></div>
    <div class="stat-card"><h4>Encrypted Groups</h4><span id="groupCount">0</span></div>
  </div>

  <div id="chat" class="page">
    <div id="activeHead" style="margin-bottom:10px; font-weight:800; color:var(--primary); text-transform:uppercase; font-size:12px; letter-spacing:1px;">Channel: Offline</div>
    <div class="chat-window" id="chatBox"></div>
    <div class="input-row">
      <label for="imgInp"><i class="fa-solid fa-paperclip" style="font-size:22px; color:var(--muted); cursor:pointer;"></i></label>
      <input type="file" id="imgInp" hidden accept="image/*" onchange="sendImage(event)">
      <input id="msgInput" placeholder="Broadcast message..."/>
      <button onclick="sendMsg()"><i class="fa-solid fa-bolt"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <h4 style="color:var(--muted); margin-bottom:12px;">ACTIVE NODES</h4>
    <div id="userList"></div>
    <h4 style="color:var(--muted); margin:20px 0 12px;">GROUPS</h4>
    <div id="groupList"></div>
    <div class="input-row" style="margin-top:10px;">
      <input id="groupInput" placeholder="Group Identity..."/>
      <button onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
    </div>
  </div>

  <div id="about" class="page">
    <div class="stat-card" style="display:block;">
      <h3 style="color:var(--primary);">System Info</h3>
      <p style="font-size:14px; color:var(--muted);"><b>Live Connect Pro</b> is a high-speed real-time hub developed by WebHub Technologies.</p>
      <ul style="font-size:13px; padding-left:18px; color:var(--muted);">
        <li>Quantum Encryption Enabled</li>
        <li>Instant Data Sync</li>
        <li>Image Rendering Engine</li>
      </ul>
    </div>
    <div class="list-item" onclick="window.open('mailto:webhub262@gmail.com')">
        <i class="fa-solid fa-envelope" style="color:var(--accent)"></i> <b>Official Support</b>
    </div>
  </div>

  <nav>
    <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house-user"></i></button>
    <button onclick="openPage('chat',this)"><i class="fa-solid fa-comment-nodes"></i></button>
    <button onclick="openPage('users',this)"><i class="fa-solid fa-users-rays"></i></button>
    <button onclick="openPage('about',this)"><i class="fa-solid fa-circle-info"></i></button>
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

let currentUser = localStorage.getItem("node_id");
let curChat = "";
let isGroup = false;

if(currentUser) {
  document.getElementById("loginPage").style.display = "none";
  startSystem();
}

window.login = () => {
  const uname = document.getElementById("usernameInput").value.trim();
  if(!uname) return;
  currentUser = uname;
  localStorage.setItem("node_id", uname);
  startSystem();
  document.getElementById("loginPage").style.display = "none";
  document.getElementById("home").classList.add("active");
};

function startSystem() {
  document.getElementById("dashUser").textContent = currentUser;
  const userRef = ref(db, "users/"+currentUser);
  set(userRef, {name: currentUser, online: true});
  onDisconnect(userRef).update({online: false});
}

window.logout = () => {
  if(currentUser) set(ref(db, "users/"+currentUser), {online: false});
  localStorage.removeItem("node_id");
  location.reload();
};

window.openPage = (id, btn) => {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
};

window.toggleTheme = () => document.body.classList.toggle('light');

onValue(ref(db, "users"), snap => {
  let online = 0;
  document.getElementById("userList").innerHTML = "";
  snap.forEach(u => {
    if(u.val().online) {
      online++;
      if(u.key !== currentUser) {
        const d = document.createElement("div");
        d.className = "list-item";
        d.innerHTML = `<div class="online-dot"></div><b>${u.val().name}</b>`;
        d.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("activeHead").innerText="Node: "+u.key; loadChat(); openPage('chat', document.querySelector('nav button:nth-child(2)')); };
        document.getElementById("userList").appendChild(d);
      }
    }
  });
  document.getElementById("onlineCount").textContent = online;
});

onValue(ref(db, "groups"), snap => {
  document.getElementById("groupList").innerHTML = "";
  document.getElementById("groupCount").textContent = snap.size || 0;
  snap.forEach(g => {
    const d = document.createElement("div");
    d.className = "list-item";
    d.innerHTML = `<i class="fa-solid fa-hashtag" style="color:var(--primary)"></i><b>${g.key}</b>`;
    d.onclick = () => { curChat=g.key; isGroup=true; document.getElementById("activeHead").innerText="Group: "+g.key; loadChat(); openPage('chat', document.querySelector('nav button:nth-child(2)')); };
    document.getElementById("groupList").appendChild(d);
  });
});

window.createGroup = () => {
  const g = document.getElementById("groupInput").value.trim();
  if(g) { set(ref(db, "groups/"+g), {createdBy: currentUser}); document.getElementById("groupInput").value=''; }
};

function loadChat() {
  const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
  onValue(ref(db, path), snap => {
    const chatBox = document.getElementById("chatBox");
    chatBox.innerHTML = "";
    snap.forEach(m => {
      const isMe = m.val().from === currentUser;
      const div = document.createElement("div");
      div.className = "msg " + (isMe ? "me" : "");
      div.innerHTML = `
        <div style="font-size:11px; font-weight:800; margin-bottom:4px; opacity:0.8;">${m.val().from}</div>
        ${m.val().img ? `<img src="${m.val().img}">` : m.val().text}
        <span class="msg-info">${new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})} <i class="fa-solid fa-trash" style="margin-left:8px;" onclick="deleteMsg('${path}','${m.key}')"></i></span>
      `;
      chatBox.appendChild(div);
    });
    chatBox.scrollTop = chatBox.scrollHeight;
  });
}

window.sendMsg = () => {
  const input = document.getElementById('msgInput');
  if(!input.value || !curChat) return;
  const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
  push(ref(db, path), {from: currentUser, text: input.value, time: Date.now()});
  input.value = '';
};

window.sendImage = (e) => {
  const reader = new FileReader();
  reader.onload = () => {
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    push(ref(db, path), {from: currentUser, img: reader.result, time: Date.now()});
  };
  reader.readAsDataURL(e.target.files[0]);
};

window.deleteMsg = (path, key) => { if(confirm("Erase message?")) remove(ref(db, path+"/"+key)); };
</script>
</body>
</html>
