<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Infinity Zenith | Live Connect</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root {
  --p: #6b5bff; --a: #ff5c8d; --bg: #050505; --card: rgba(255,255,255,0.06);
  --text: #ffffff; --muted: #a1a1a1; --border: rgba(255,255,255,0.1);
}
body.light {
  --bg: #f8f9fa; --card: rgba(255,255,255,0.8); --text: #111; --muted: #666; --border: rgba(0,0,0,0.1);
}
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); transition: 0.4s; overflow: hidden; }

/* Background Glow */
.glow-bg { position: fixed; top: -10%; right: -10%; width: 300px; height: 300px; background: var(--p); filter: blur(150px); opacity: 0.3; z-index: -1; pointer-events: none; }

.app { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Header Modernized */
header { padding: 50px 20px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(20px); border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 24px; font-weight: 800; background: linear-gradient(45deg, var(--p), var(--a)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
.head-btns { display: flex; gap: 15px; }
.head-btns button { background: var(--card); border: 1px solid var(--border); color: var(--text); width: 40px; height: 40px; border-radius: 12px; cursor: pointer; }

/* Navigation Re-imagined */
nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 90%; height: 75px; background: rgba(0,0,0,0.8); backdrop-filter: blur(25px); border-radius: 25px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
nav button { background: none; border: none; font-size: 22px; color: var(--muted); cursor: pointer; transition: 0.3s; }
nav button.active { color: var(--p); transform: translateY(-5px); text-shadow: 0 0 15px var(--p); }

/* Pages Styling */
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; animation: fadeIn 0.4s ease; }
.page.active { display: block; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

/* Cards & Stats */
.hero-card { background: linear-gradient(135deg, var(--p), var(--a)); padding: 30px; border-radius: 25px; color: #fff; margin-bottom: 20px; box-shadow: 0 15px 35px rgba(107,91,255,0.3); }
.stat-box { background: var(--card); padding: 18px; border-radius: 20px; border: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; }
.stat-box b { font-size: 18px; color: var(--a); }

/* Chat Engine */
.chat-window { background: var(--card); border-radius: 25px; border: 1px solid var(--border); height: 45vh; overflow-y: auto; padding: 15px; margin-bottom: 15px; display: flex; flex-direction: column; gap: 10px; }
.msg-bubble { padding: 12px 16px; border-radius: 20px; background: #1a1a1a; font-size: 14px; position: relative; max-width: 85%; border: 1px solid var(--border); }
.msg-bubble.me { background: var(--p); align-self: flex-end; border-bottom-right-radius: 4px; border: none; }
.msg-bubble .msg-actions { position: absolute; top: 5px; right: 10px; font-size: 12px; cursor: pointer; opacity: 0.6; }

/* Modern Input */
.input-dock { display: flex; gap: 10px; }
.input-dock input { flex: 1; padding: 15px 20px; border-radius: 20px; border: 1px solid var(--border); background: var(--card); color: var(--text); outline: none; }
.input-dock button { width: 55px; height: 55px; border-radius: 18px; border: none; background: var(--p); color: #fff; font-size: 18px; box-shadow: 0 5px 15px var(--p); }

/* User/Group List */
.list-item { background: var(--card); padding: 15px; border-radius: 20px; border: 1px solid var(--border); display: flex; align-items: center; gap: 12px; margin-bottom: 10px; transition: 0.3s; }
.list-item:active { transform: scale(0.96); }
.status-dot { width: 10px; height: 10px; background: #22c55e; border-radius: 50%; box-shadow: 0 0 10px #22c55e; }

/* Login */
.login-screen { position: fixed; inset: 0; background: var(--bg); z-index: 2000; display: flex; align-items: center; justify-content: center; padding: 30px; }
.login-card { width: 100%; background: var(--card); padding: 35px; border-radius: 30px; border: 1px solid var(--border); text-align: center; }
.login-card input { width: 100%; padding: 18px; border-radius: 15px; border: 1px solid var(--border); background: #000; color: #fff; margin-bottom: 15px; text-align: center; }
.login-card button { width: 100%; padding: 18px; border-radius: 15px; border: none; background: var(--p); color: #fff; font-weight: 800; }
</style>
</head>
<body>

<div class="glow-bg"></div>

<div class="app">
  <header>
    <h1>ZENITH</h1>
    <div class="head-btns">
      <button onclick="toggleTheme()"><i class="fa-solid fa-circle-half-stroke"></i></button>
      <button onclick="logout()"><i class="fa-solid fa-power-off"></i></button>
    </div>
  </header>

  <div id="loginPage" class="login-screen">
    <div class="login-card">
      <i class="fa-solid fa-bolt-lightning" style="font-size: 40px; color: var(--p); margin-bottom: 20px;"></i>
      <h2 style="margin-top:0;">Access Nexus</h2>
      <input type="text" id="usernameInput" placeholder="Quantum ID">
      <button onclick="login()">INITIALIZE</button>
    </div>
  </div>

  <div id="home" class="page">
    <div class="hero-card">
      <h2 style="margin:0;">Welcome, <span id="dashUser">User</span></h2>
      <p style="opacity:0.8;">Secure connection established.</p>
    </div>
    <div class="stat-box"><span>Network Users</span><b id="onlineCount">0</b></div>
    <div class="stat-box"><span>Active Channels</span><b id="groupCount">0</b></div>
  </div>

  <div id="chat" class="page">
    <div id="chatTarget" style="margin-bottom:10px; font-weight:800; color:var(--p);">Select a contact</div>
    <div class="chat-window" id="chatBox"></div>
    <div class="input-dock">
      <input id="msgInput" placeholder="Type a message..."/>
      <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <h4 style="margin-bottom:15px; color:var(--muted); letter-spacing:1px;">ONLINE DIRECTORY</h4>
    <div id="userList"></div>
    <h4 style="margin:20px 0 15px; color:var(--muted); letter-spacing:1px;">GROUP CHANNELS</h4>
    <div id="groupList"></div>
    <div class="input-dock" style="margin-top:10px;">
      <input id="groupInput" placeholder="New Channel Name"/>
      <button onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
    </div>
  </div>

  <div id="about" class="page">
    <h3 style="color:var(--p);">Quantum Tech Support</h3>
    <p style="font-size:14px; color:var(--muted);">Developed by <b>WebHub Technologies</b></p>
    <div class="list-item" onclick="window.open('mailto:webhub262@gmail.com')">
      <i class="fa-solid fa-envelope"></i> <b>Support Email</b>
    </div>
    <div class="list-item" onclick="window.open('https://youtube.com/@CrazyKhanTV')">
      <i class="fa-brands fa-youtube" style="color:#ff0000"></i> <b>CrazyKhan TV</b>
    </div>
    <div class="list-item">
      <i class="fa-solid fa-shield-check"></i> <b>Encrypted Sessions</b>
    </div>
  </div>

  <nav>
    <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house-chimney-window"></i></button>
    <button onclick="openPage('chat',this)"><i class="fa-solid fa-message-captions"></i></button>
    <button onclick="openPage('users',this)"><i class="fa-solid fa-compass"></i></button>
    <button onclick="openPage('about',this)"><i class="fa-solid fa-fingerprint"></i></button>
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
  storageBucket: "live-chat-b810c.appspot.com",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("zenith_user") || null;
let curChat = "";
let isGroup = false;

if(currentUser) {
  document.getElementById("loginPage").style.display = "none";
  initSession();
}

window.login = () => {
  const uname = document.getElementById("usernameInput").value.trim();
  if(!uname) return;
  currentUser = uname;
  localStorage.setItem("zenith_user", uname);
  initSession();
  document.getElementById("loginPage").style.display = "none";
  document.getElementById("home").classList.add("active");
};

function initSession(){
  document.getElementById("dashUser").textContent = currentUser;
  const userRef = ref(db, "users/"+currentUser);
  set(userRef, {name: currentUser, online: true});
  onDisconnect(userRef).update({online: false});
}

window.logout = () => {
  localStorage.removeItem("zenith_user");
  if(currentUser) set(ref(db, "users/"+currentUser), {online: false});
  location.reload();
};

window.openPage = (id, btn) => {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
};

window.toggleTheme = () => document.body.classList.toggle('light');

// Sync Core Data
onValue(ref(db, "users"), snap => {
  let online = 0;
  userList.innerHTML = "";
  snap.forEach(u => {
    if(u.val().online) {
      online++;
      if(u.key !== currentUser) {
        const d = document.createElement("div");
        d.className = "list-item";
        d.innerHTML = `<div class="status-dot"></div><b>${u.val().name}</b>`;
        d.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("chatTarget").innerText = "Direct: "+u.key; loadChat(); openPage('chat', document.querySelector('nav button:nth-child(2)')); };
        userList.appendChild(d);
      }
    }
  });
  onlineCount.textContent = online;
});

onValue(ref(db, "groups"), snap => {
  groupList.innerHTML = "";
  groupCount.textContent = snap.size || 0;
  snap.forEach(g => {
    const d = document.createElement("div");
    d.className = "list-item";
    d.innerHTML = `<i class="fa-solid fa-hashtag" style="color:var(--p)"></i><b>${g.key}</b>`;
    d.onclick = () => { curChat=g.key; isGroup=true; document.getElementById("chatTarget").innerText = "Channel: "+g.key; loadChat(); openPage('chat', document.querySelector('nav button:nth-child(2)')); };
    groupList.appendChild(d);
  });
});

window.createGroup = () => {
  const g = document.getElementById("groupInput").value.trim();
  if(!g) return;
  set(ref(db, "groups/"+g), {createdBy: currentUser});
  document.getElementById("groupInput").value = '';
};

function loadChat() {
  if(!curChat) return;
  const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
  onValue(ref(db, path), snap => {
    chatBox.innerHTML = "";
    snap.forEach(m => {
      const isMe = m.val().from === currentUser;
      const div = document.createElement("div");
      div.className = "msg-bubble " + (isMe ? "me" : "");
      div.innerHTML = `
        <div class="msg-actions" onclick="deleteMsg('${path}','${m.key}')">🗑️</div>
        <b style="font-size:11px; opacity:0.8;">${m.val().from}</b><br>
        ${m.val().text}
        <div style="font-size:10px; margin-top:5px; opacity:0.6; text-align:right;">${new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</div>
      `;
      chatBox.appendChild(div);
    });
    chatBox.scrollTop = chatBox.scrollHeight;
  });
}

window.sendMsg = () => {
  if(!curChat) return;
  const input = document.getElementById('msgInput');
  if(!input.value) return;
  const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
  push(ref(db, path), {from: currentUser, text: input.value, time: Date.now()});
  input.value = '';
};

window.deleteMsg = (path, key) => { if(confirm("Erase from history?")) remove(ref(db, path+"/"+key)); };
window.addComment = (path, key, btn) => { /* Keep for compatibility */ };
</script>
</body>
</html>
