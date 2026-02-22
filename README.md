<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Infinity Pro Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --bg: #0b0e14; --card: #1b202d; --primary: #00a884; --accent: #34b7f1;
  --text: #e9edef; --muted: #8696a0; --glass: rgba(255,255,255,0.1);
}
body { margin:0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); overflow: hidden; }
.app { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Dynamic Header */
header { padding: 15px; background: var(--card); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--glass); }
header h1 { font-size: 1.2rem; color: var(--primary); margin: 0; }

.content { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 120px; }
.page { display: none; }
.active { display: block; animation: fadeIn 0.3s; }

/* WhatsApp Style Chat Bubbles */
.chat-container { display: flex; flex-direction: column; gap: 8px; }
.msg { padding: 8px 12px; border-radius: 10px; max-width: 75%; font-size: 14px; position: relative; background: var(--card); box-shadow: 0 1px 2px rgba(0,0,0,0.3); }
.msg.me { align-self: flex-end; background: #005c4b; color: white; border-bottom-right-radius: 2px; }
.msg.them { align-self: flex-start; border-bottom-left-radius: 2px; }
.msg b { font-size: 11px; color: var(--accent); display: block; margin-bottom: 2px; }
.typing-status { font-style: italic; font-size: 12px; color: var(--primary); margin-bottom: 5px; height: 15px; }

/* Multimedia Input Bar */
.input-area { position: absolute; bottom: 75px; left: 0; right: 0; background: var(--card); padding: 10px; display: flex; align-items: center; gap: 10px; }
.input-area input { flex: 1; background: #2a3942; border: none; padding: 12px; border-radius: 20px; color: white; outline: none; }
.icon-btn { color: var(--muted); font-size: 20px; cursor: pointer; transition: 0.2s; }
.icon-btn:hover { color: var(--primary); }

/* Navigation */
nav { position: absolute; bottom: 0; left: 0; right: 0; height: 70px; background: var(--card); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--glass); }
nav button { background: none; border: none; color: var(--muted); display: flex; flex-direction: column; align-items: center; font-size: 18px; cursor: pointer; }
nav button.active { color: var(--primary); }
nav button span { font-size: 10px; margin-top: 4px; }

@keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
</style>
</head>
<body>

<div class="app">
  <header>
    <h1 id="headerTitle">Infinity Pro</h1>
    <div style="display:flex; gap:15px;">
      <i class="fa-solid fa-camera icon-btn"></i>
      <i class="fa-solid fa-ellipsis-vertical icon-btn"></i>
    </div>
  </header>

  <div class="content" id="scrollArea">
    <div id="loginPage" class="page active">
        <div style="text-align:center; margin-top:50px;">
            <img src="https://cdn-icons-png.flaticon.com/512/733/733585.png" width="80">
            <h2>Welcome to Connect</h2>
            <input type="text" id="uName" placeholder="Enter your name" style="width:80%; padding:15px; border-radius:10px; margin-top:20px; border:none; background:var(--card); color:white;">
            <button onclick="startApp()" style="width:80%; padding:15px; border-radius:10px; margin-top:20px; border:none; background:var(--primary); color:white; font-weight:bold;">START CHATTING</button>
        </div>
    </div>

    <div id="home" class="page">
        <h3>Recent Chats</h3>
        <div id="recentList"></div>
    </div>

    <div id="chat" class="page">
        <div class="typing-status" id="typingStatus"></div>
        <div class="chat-container" id="chatBox"></div>
    </div>

    <div id="users" class="page">
        <h3>Network Directory</h3>
        <div id="userList"></div>
    </div>
  </div>

  <div class="input-area" id="inputPanel" style="display:none;">
    <i class="fa-solid fa-plus icon-btn"></i>
    <input type="text" id="msgInp" placeholder="Message..." oninput="handleTyping()">
    <i class="fa-solid fa-microphone icon-btn" id="micBtn"></i>
    <i class="fa-solid fa-paper-plane icon-btn" style="color:var(--primary)" onclick="sendMessage()"></i>
  </div>

  <nav id="bottomNav" style="display:none;">
    <button onclick="tab('home', this)" class="active"><i class="fa-solid fa-circle-notch"></i><span>Status</span></button>
    <button onclick="tab('users', this)"><i class="fa-solid fa-user-group"></i><span>Users</span></button>
    <button onclick="tab('chat', this)" id="cTab"><i class="fa-solid fa-message"></i><span>Chats</span></button>
    <button onclick="tab('settings', this)"><i class="fa-solid fa-gear"></i><span>Settings</span></button>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("pro_user");
let currentRoom = "Global";

window.startApp = () => {
    const val = document.getElementById("uName").value.trim();
    if(!val) return;
    user = val;
    localStorage.setItem("pro_user", user);
    initSession();
};

function initSession() {
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("home").classList.add("active");
    document.getElementById("bottomNav").style.display = "flex";
    
    set(ref(db, "users/" + user), { online: true, lastSeen: Date.now() });
    onDisconnect(ref(db, "users/" + user)).set({ online: false, lastSeen: Date.now() });
    loadUsers();
}

window.tab = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    document.getElementById('inputPanel').style.display = (id === 'chat') ? 'flex' : 'none';
};

function loadUsers() {
    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("userList");
        list.innerHTML = "";
        snap.forEach(u => {
            if(u.key !== user) {
                const d = document.createElement("div");
                d.style = "padding:15px; background:var(--card); margin-bottom:10px; border-radius:10px; display:flex; align-items:center; gap:10px;";
                d.innerHTML = `<div style="width:10px; height:10px; border-radius:50%; background:${u.val().online ? '#00a884':'#8696a0'}"></div> <b>${u.key}</b>`;
                d.onclick = () => startChat(u.key);
                list.appendChild(d);
            }
        });
    });
}

function startChat(target) {
    currentRoom = [user, target].sort().join("_");
    document.getElementById("headerTitle").innerText = target;
    tab('chat', document.getElementById('cTab'));
    syncMessages();
}

function syncMessages() {
    onValue(ref(db, "chats/" + currentRoom), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const data = m.val();
            const div = document.createElement("div");
            div.className = "msg " + (data.from === user ? "me" : "them");
            div.innerHTML = `<b>${data.from}</b>${data.text}`;
            box.appendChild(div);
        });
        document.getElementById('scrollArea').scrollTop = document.getElementById('scrollArea').scrollHeight;
    });
}

window.sendMessage = () => {
    const inp = document.getElementById("msgInp");
    if(!inp.value) return;
    push(ref(db, "chats/" + currentRoom), { from: user, text: inp.value, time: Date.now() });
    inp.value = "";
};

if(user) initSession();
</script>
</body>
</html>
