<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Infinity Connect Pro 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --primary: #00d2ff; --secondary: #3a7bd5; --bg: #0f0c29;
  --card: rgba(255, 255, 255, 0.05); --text: #ffffff; --accent: #ff007a;
}

body, html { margin:0; padding:0; height:100%; font-family: 'Inter', sans-serif; background: linear-gradient(to bottom, #0f0c29, #302b63, #24243e); color: var(--text); overflow: hidden; }

/* Main Shell */
.app-container { max-width: 450px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; backdrop-filter: blur(10px); }

/* Header - Modern & Transparent */
header { padding: 20px; display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.2); border-bottom: 1px solid rgba(255,255,255,0.1); }
header h1 { font-size: 22px; font-weight: 800; background: linear-gradient(45deg, var(--primary), var(--accent)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

/* Content Area */
.main-view { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 120px; }
.page { display: none; }
.page.active { display: block; animation: slideUp 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); }

@keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }

/* Status Bar (WhatsApp Style) */
.status-row { display: flex; gap: 15px; overflow-x: auto; padding: 10px 0; margin-bottom: 20px; scrollbar-width: none; }
.status-circle { min-width: 60px; height: 60px; border-radius: 50%; border: 2px solid var(--primary); padding: 3px; cursor: pointer; }
.status-circle img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }

/* Chat Bubbles */
.chat-window { display: flex; flex-direction: column; gap: 12px; }
.message { padding: 12px 16px; border-radius: 20px; max-width: 80%; font-size: 14px; position: relative; backdrop-filter: blur(5px); }
.message.sent { align-self: flex-end; background: linear-gradient(135deg, var(--primary), var(--secondary)); border-bottom-right-radius: 4px; box-shadow: 0 4px 15px rgba(0, 210, 255, 0.3); }
.message.received { align-self: flex-start; background: var(--card); border: 1px solid rgba(255,255,255,0.1); border-bottom-left-radius: 4px; }
.message small { display: block; font-size: 10px; margin-top: 5px; opacity: 0.7; text-align: right; }

/* Futuristic Input Bar */
.bottom-panel { position: absolute; bottom: 85px; left: 15px; right: 15px; background: rgba(255,255,255,0.1); border-radius: 30px; padding: 10px 15px; display: flex; align-items: center; gap: 12px; border: 1px solid rgba(255,255,255,0.2); }
.bottom-panel input { flex: 1; background: transparent; border: none; color: white; font-size: 15px; outline: none; }
.action-icon { font-size: 20px; color: var(--primary); cursor: pointer; transition: 0.3s; }
.action-icon:hover { transform: scale(1.2); color: var(--accent); }

/* Navigation Dock */
nav { position: absolute; bottom: 15px; left: 15px; right: 15px; height: 65px; background: rgba(0,0,0,0.4); backdrop-filter: blur(15px); border-radius: 20px; display: flex; justify-content: space-around; align-items: center; border: 1px solid rgba(255,255,255,0.1); }
nav button { background: none; border: none; color: #888; transition: 0.3s; cursor: pointer; display: flex; flex-direction: column; align-items: center; }
nav button.active { color: var(--primary); transform: translateY(-5px); }
nav button i { font-size: 22px; }

/* Login Styling */
.login-box { text-align: center; padding: 40px 20px; background: var(--card); border-radius: 30px; margin-top: 50px; border: 1px solid rgba(255,255,255,0.1); }
.login-btn { width: 100%; padding: 15px; border-radius: 15px; border: none; background: linear-gradient(45deg, var(--primary), var(--secondary)); color: white; font-weight: bold; margin-top: 20px; cursor: pointer; box-shadow: 0 10px 20px rgba(0, 210, 255, 0.4); }

</style>
</head>
<body>

<div class="app-container">
    <header>
        <h1>INFINITY</h1>
        <div style="display:flex; gap:15px;">
            <i class="fa-solid fa-bell action-icon"></i>
            <i class="fa-solid fa-user-gear action-icon" onclick="openPage('settings', this)"></i>
        </div>
    </header>

    <div class="main-view" id="scrollArea">
        
        <div id="loginPage" class="page active">
            <div class="login-box">
                <i class="fa-solid fa-shield-halved" style="font-size: 60px; color: var(--primary);"></i>
                <h2 style="margin-top:20px;">Identity Sync</h2>
                <input type="text" id="username" placeholder="Enter secure alias..." style="width:100%; box-sizing:border-box; padding:15px; border-radius:12px; background:rgba(0,0,0,0.3); border:1px solid var(--primary); color:white; margin-top:15px; outline:none;">
                <button class="login-btn" onclick="login()">INITIALIZE CONNECT</button>
            </div>
        </div>

        <div id="home" class="page">
            <div class="status-row">
                <div class="status-circle"><img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Felix"></div>
                <div class="status-circle" style="border-color:var(--accent)"><img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Aneka"></div>
                <div class="status-circle"><img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Jack"></div>
                <div class="status-circle"><img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Sarah"></div>
            </div>
            <h3 style="margin-bottom:15px;">Live Broadcasts</h3>
            <div id="groupList"></div>
        </div>

        <div id="chat" class="page">
            <div id="chatHeader" style="text-align:center; padding-bottom:10px; border-bottom:1px solid var(--card); margin-bottom:15px;">
                <b id="activeChatName">Global Room</b>
            </div>
            <div class="chat-window" id="chatBox"></div>
        </div>

        <div id="users" class="page">
            <h3>Active Node Users</h3>
            <div id="userList"></div>
        </div>

    </div>

    <div class="bottom-panel" id="inputPanel" style="display:none;">
        <i class="fa-solid fa-paperclip action-icon"></i>
        <input type="text" id="msgInp" placeholder="Type a message...">
        <i class="fa-solid fa-microphone action-icon"></i>
        <i class="fa-solid fa-paper-plane action-icon" style="color:var(--primary)" onclick="send()"></i>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="openPage('home', this)" class="active"><i class="fa-solid fa-house-chimney"></i></button>
        <button onclick="openPage('chat', this)" id="cTab"><i class="fa-solid fa-comment-dots"></i></button>
        <button onclick="openPage('users', this)"><i class="fa-solid fa-users-viewfinder"></i></button>
        <button onclick="logout()"><i class="fa-solid fa-power-off"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("inc_user");
let activeRoom = "Global_Lobby";

window.login = () => {
    const val = document.getElementById("username").value.trim();
    if(!val) return;
    currentUser = val;
    localStorage.setItem("inc_user", val);
    start();
};

function start() {
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("home").classList.add("active");
    document.getElementById("navbar").style.display = "flex";
    
    set(ref(db, "users/" + currentUser), { online: true, status: "Active" });
    onDisconnect(ref(db, "users/" + currentUser)).set({ online: false });
    loadData();
}

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    document.getElementById('inputPanel').style.display = (id === 'chat') ? 'flex' : 'none';
};

function loadData() {
    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("userList");
        list.innerHTML = "";
        snap.forEach(u => {
            if(u.key !== currentUser) {
                const item = document.createElement("div");
                item.style = "padding:15px; background:var(--card); border-radius:15px; margin-bottom:10px; display:flex; align-items:center; gap:15px; border:1px solid rgba(255,255,255,0.05); cursor:pointer;";
                item.innerHTML = `<img src="https://api.dicebear.com/7.x/avataaars/svg?seed=${u.key}" width="40"> <div><b>${u.key}</b><br><small style="color:${u.val().online ? '#00ffcc':'#888'}">${u.val().online ? '● Online':'Offline'}</small></div>`;
                item.onclick = () => startPrivate(u.key);
                list.appendChild(item);
            }
        });
    });
}

function startPrivate(target) {
    activeRoom = [currentUser, target].sort().join("_");
    document.getElementById("activeChatName").innerText = target;
    openPage('chat', document.getElementById('cTab'));
    syncMessages();
}

function syncMessages() {
    onValue(ref(db, "chats/" + activeRoom), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div");
            div.className = "message " + (d.from === currentUser ? "sent" : "received");
            div.innerHTML = `<b>${d.from}</b>${d.text}<small>${new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}</small>`;
            box.appendChild(div);
        });
        document.getElementById('scrollArea').scrollTop = document.getElementById('scrollArea').scrollHeight;
    });
}

window.send = () => {
    const inp = document.getElementById("msgInp");
    if(!inp.value) return;
    push(ref(db, "chats/" + activeRoom), { from: currentUser, text: inp.value });
    inp.value = "";
};

window.logout = () => { localStorage.removeItem("inc_user"); location.reload(); };

if(currentUser) start();
</script>
</body>
</html>
