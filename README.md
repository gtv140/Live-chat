<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect Pro 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --bg: #0b0e11; --card: #151921; --primary: #7d67ff; --accent: #ff5c8d;
  --text: #ffffff; --muted: #94a3b8; --success: #22c55e;
  --grad: linear-gradient(135deg, #7d67ff 0%, #ff5c8d 100%);
}
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; outline: none; }
body { 
  margin: 0; font-family: 'Segoe UI', Roboto, sans-serif; background: var(--bg); color: var(--text);
  height: 100dvh; display: flex; flex-direction: column; overflow: hidden;
}

/* SMART HEADER */
header { 
  padding: 45px 20px 15px; background: var(--card); border-bottom: 1px solid #2d3748; 
  display: flex; justify-content: space-between; align-items: center; flex-shrink: 0;
}
header h1 { font-size: 20px; font-weight: 800; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

/* VIEWPORT & PAGES */
.viewport { flex: 1; position: relative; overflow: hidden; }
.page { display: none; height: 100%; flex-direction: column; padding: 16px; overflow-y: auto; }
.page.active { display: flex; }

/* MODERN CHAT CONTAINER */
.chat-window { display: flex; flex-direction: column; height: 100%; background: var(--bg); border-radius: 20px 20px 0 0; }
#chatBox { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px; }

/* BUBBLES WITH COMMENTS */
.msg-card { max-width: 85%; padding: 12px; border-radius: 18px; position: relative; font-size: 14px; animation: slideIn 0.3s ease; }
.msg-card.me { align-self: flex-end; background: var(--primary); border-bottom-right-radius: 2px; }
.msg-card.other { align-self: flex-start; background: var(--card); border: 1px solid #2d3748; border-bottom-left-radius: 2px; }
@keyframes slideIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

.msg-header { font-size: 10px; font-weight: bold; opacity: 0.7; margin-bottom: 4px; display: flex; justify-content: space-between; }
.comment-box { margin-top: 8px; background: rgba(0,0,0,0.2); border-radius: 10px; padding: 6px 10px; font-size: 12px; border-left: 2px solid var(--accent); }

/* FLOATING INPUT ROW */
.input-row { 
  padding: 12px; background: var(--card); display: flex; align-items: center; gap: 10px; 
  border-top: 1px solid #2d3748; margin-bottom: env(safe-area-inset-bottom);
}
.input-row input { flex: 1; background: #0b0e11; border: 1px solid #2d3748; padding: 12px 15px; border-radius: 25px; color: white; font-size: 15px; }
.input-row button { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--grad); color: white; cursor: pointer; }

/* CARDS & LISTS */
.node-card { background: var(--card); padding: 15px; border-radius: 16px; margin-bottom: 10px; display: flex; align-items: center; gap: 12px; border: 1px solid transparent; transition: 0.2s; }
.node-card:active { transform: scale(0.98); border-color: var(--primary); }
.pfp { width: 40px; height: 40px; border-radius: 12px; background: var(--grad); display: flex; align-items: center; justify-content: center; font-weight: bold; }

/* NAVIGATION */
nav { 
  height: 75px; display: flex; justify-content: space-around; align-items: center; 
  background: var(--card); border-top: 1px solid #2d3748; padding-bottom: 15px; flex-shrink: 0;
}
nav button { background: none; border: none; color: var(--muted); font-size: 20px; transition: 0.3s; position: relative; }
nav button.active { color: var(--primary); }
nav button.active::after { content:''; position:absolute; bottom:-6px; left:50%; transform:translateX(-50%); width:4px; height:4px; background:var(--primary); border-radius:50%; }

/* LOGIN */
#loginPage { position: fixed; inset: 0; z-index: 10000; background: var(--bg); display: flex; align-items: center; justify-content: center; padding: 25px; text-align: center; }
.login-box { width: 100%; max-width: 320px; }
.login-box input { width: 100%; padding: 16px; border-radius: 15px; background: var(--card); border: 1px solid #2d3748; color: white; margin: 20px 0; font-size: 16px; text-align: center; }
.login-box button { width: 100%; padding: 16px; border-radius: 15px; border: none; background: var(--grad); color: white; font-weight: bold; font-size: 16px; box-shadow: 0 10px 20px rgba(125, 103, 255, 0.3); }

/* HERO SECTION */
.hero { background: var(--grad); border-radius: 25px; padding: 25px; margin-bottom: 20px; box-shadow: 0 10px 25px rgba(0,0,0,0.3); }
.hero h2 { margin: 0; font-size: 22px; }
.stat-row { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
.stat-card { background: var(--card); padding: 15px; border-radius: 15px; text-align: center; border: 1px solid #2d3748; }
.stat-card b { display: block; font-size: 20px; color: var(--primary); }
</style>
</head>
<body>

<div id="loginPage" class="page active">
  <div class="login-box">
    <div style="font-size: 50px; margin-bottom: 10px;">🚀</div>
    <h1>Live Connect</h1>
    <p style="color:var(--muted); font-size: 12px;">The Future of Real-Time Chat</p>
    <input type="text" id="usernameInput" placeholder="Choose a nickname...">
    <button onclick="login()">GET STARTED</button>
  </div>
</div>

<header>
  <h1 id="headerTitle">Live Connect</h1>
  <div style="display:flex; gap:15px;">
    <button style="background:none; border:none; color:var(--text); font-size:18px;" onclick="location.reload()"><i class="fa-solid fa-rotate"></i></button>
    <button style="background:none; border:none; color:var(--accent); font-size:18px;" onclick="logout()"><i class="fa-solid fa-power-off"></i></button>
  </div>
</header>

<div class="viewport">
  <div id="home" class="page">
    <div class="hero">
      <h2>Hi, <span id="dashUser">User</span>! 👋</h2>
      <p style="opacity:0.8; font-size:13px;">You are connected to the secure node.</p>
    </div>
    <div class="stat-row">
      <div class="stat-card"><b><span id="onlineCount">0</span></b><small>Online</small></div>
      <div class="stat-card"><b><span id="groupCount">0</span></b><small>Groups</small></div>
    </div>
  </div>

  <div id="chat" class="page" style="padding:0;">
    <div class="chat-window">
      <div id="chatBox"></div>
      <div class="input-row">
        <input id="msgInput" placeholder="Message...">
        <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
      </div>
    </div>
  </div>

  <div id="users" class="page">
    <h3 style="font-size:14px; color:var(--muted);">ACTIVE USERS</h3>
    <div id="userList"></div>
    <h3 style="font-size:14px; color:var(--muted); margin-top:20px;">CLUSTERS</h3>
    <div id="groupList"></div>
    <div class="input-row" style="margin-top:15px; border-radius:15px; border:1px solid #2d3748;">
      <input id="groupInput" placeholder="New group name...">
      <button onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
    </div>
  </div>

  <div id="about" class="page">
    <div class="node-card" style="flex-direction:column; align-items:flex-start;">
      <h3 style="color:var(--primary);">About Platform</h3>
      <p style="font-size:13px; color:var(--muted); line-height:1.6;">
        Live Connect is a high-speed messaging protocol designed by <b>WebHub Technologies</b>. 
        It features end-to-end event tracking and real-time database synchronization.
      </p>
    </div>
    <div class="node-card" onclick="window.open('tel:+923001234567')">
      <i class="fa-solid fa-phone" style="color:var(--success);"></i>
      <div><b>Support Line</b><br><small>+92 300 1234567</small></div>
    </div>
    <div class="node-card" onclick="window.open('mailto:webhub262@gmail.com')">
      <i class="fa-solid fa-envelope" style="color:var(--accent);"></i>
      <div><b>Official Email</b><br><small>webhub262@gmail.com</small></div>
    </div>
  </div>
</div>

<nav>
  <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house-user"></i></button>
  <button onclick="openPage('chat',this)"><i class="fa-solid fa-comment-dots"></i></button>
  <button onclick="openPage('users',this)"><i class="fa-solid fa-users-viewfinder"></i></button>
  <button onclick="openPage('about',this)"><i class="fa-solid fa-circle-nodes"></i></button>
</nav>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let currentUser = localStorage.getItem("lc_user");
let curChat = "";
let isGroup = false;

if(currentUser) { 
  document.getElementById("loginPage").style.display = "none";
  initSession(currentUser);
}

window.login = () => {
  const uname = document.getElementById("usernameInput").value.trim();
  if(!uname) return;
  currentUser = uname;
  localStorage.setItem("lc_user", uname);
  initSession(uname);
  document.getElementById("loginPage").style.display = "none";
};

function initSession(uname) {
  document.getElementById("dashUser").textContent = uname;
  const userRef = ref(db, "users/" + uname);
  update(userRef, { online: true });
  onDisconnect(userRef).update({ online: false });
  loadLists();
}

window.logout = () => {
  if(!currentUser) return;
  update(ref(db, "users/" + currentUser), { online: false }).then(() => {
    localStorage.clear();
    location.reload();
  });
};

function loadLists() {
  onValue(ref(db, "users"), snap => {
    let count = 0;
    const list = document.getElementById("userList");
    list.innerHTML = "";
    snap.forEach(u => {
      if(u.val().online) {
        count++;
        if(u.key !== currentUser) {
          list.innerHTML += `<div class="node-card" onclick="startChat('${u.key}', false)">
            <div class="pfp">${u.key[0]}</div>
            <div style="flex:1"><b>${u.key}</b><br><small style="color:var(--success)">Available</small></div>
            <i class="fa-solid fa-chevron-right" style="font-size:12px; opacity:0.3;"></i>
          </div>`;
        }
      }
    });
    document.getElementById("onlineCount").textContent = count;
  });

  onValue(ref(db, "groups"), snap => {
    const list = document.getElementById("groupList");
    list.innerHTML = "";
    document.getElementById("groupCount").textContent = snap.size || 0;
    snap.forEach(g => {
      list.innerHTML += `<div class="node-card" onclick="startChat('${g.key}', true)">
        <div class="pfp" style="background:#333">#</div>
        <div style="flex:1"><b>${g.key}</b><br><small style="color:var(--muted)">Group Channel</small></div>
      </div>`;
    });
  });
}

window.startChat = (target, groupMode) => {
  curChat = target;
  isGroup = groupMode;
  document.getElementById("headerTitle").innerText = target;
  openPage('chat', document.querySelectorAll('nav button')[1]);
  loadMessages();
};

function loadMessages() {
  const path = (isGroup ? "groupChats/" : "chats/") + [currentUser, curChat].sort().join("_");
  onValue(ref(db, path), snap => {
    const box = document.getElementById("chatBox");
    box.innerHTML = "";
    snap.forEach(m => {
      const d = m.val();
      const isMe = d.from === currentUser;
      const comments = d.comments ? Object.values(d.comments).map(c => `<div class="comment-box"><b>${c.from}:</b> ${c.text}</div>`).join('') : '';
      
      box.innerHTML += `
        <div class="msg-card ${isMe?'me':'other'}">
          <div class="msg-header"><span>${d.from}</span><span onclick="deleteMsg('${path}','${m.key}')">🗑️</span></div>
          ${d.text}
          ${comments}
          <div style="display:flex; gap:5px; margin-top:8px;">
            <input id="ci_${m.key}" placeholder="Reply..." style="flex:1; font-size:11px; background:rgba(0,0,0,0.1); border:none; color:white; padding:4px 8px; border-radius:5px;">
            <button onclick="addComment('${path}','${m.key}')" style="background:none; border:none; color:white; font-size:14px;"><i class="fa-solid fa-reply"></i></button>
          </div>
        </div>`;
    });
    box.scrollTop = box.scrollHeight;
  });
}

window.sendMsg = () => {
  const inp = document.getElementById("msgInput");
  if(!inp.value || !curChat) return;
  const path = (isGroup ? "groupChats/" : "chats/") + [currentUser, curChat].sort().join("_");
  push(ref(db, path), { from: currentUser, text: inp.value, time: Date.now() });
  inp.value = "";
};

window.addComment = (path, key) => {
  const inp = document.getElementById("ci_" + key);
  if(!inp.value) return;
  push(ref(db, path + "/" + key + "/comments"), { from: currentUser, text: inp.value });
  inp.value = "";
};

window.deleteMsg = (path, key) => { if(confirm("Delete message?")) remove(ref(db, path + "/" + key)); };

window.createGroup = () => {
  const g = document.getElementById("groupInput").value.trim();
  if(!g) return;
  set(ref(db, "groups/" + g), { createdBy: currentUser });
  document.getElementById("groupInput").value = "";
};

window.openPage = (id, btn) => {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
  if(id !== 'chat') document.getElementById("headerTitle").innerText = "Live Connect";
};
</script>
</body>
</html>
