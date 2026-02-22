<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Infinity Connect Pro 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --primary: #00d2ff; --secondary: #3a7bd5; --bg: #0b0e14;
  --card: rgba(255, 255, 255, 0.08); --text: #ffffff; --accent: #ff007a;
}
body, html { margin:0; padding:0; height:100%; font-family: 'Inter', sans-serif; background: #0b0e14; color: var(--text); overflow: hidden; }

/* Background Decoration */
.bg-glow { position: fixed; top: -10%; left: -10%; width: 50%; height: 50%; background: radial-gradient(circle, rgba(0,210,255,0.15), transparent); z-index: -1; }

.app { max-width: 480px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; background: #0b0e14; }

/* Header */
header { padding: 18px; display: flex; justify-content: space-between; align-items: center; background: rgba(27, 32, 45, 0.9); backdrop-filter: blur(10px); border-bottom: 1px solid rgba(255,255,255,0.1); z-index: 100; }
header h1 { font-size: 20px; font-weight: 800; background: linear-gradient(45deg, var(--primary), var(--secondary)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

/* Pages & Scroll Area */
.main-view { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 150px; scroll-behavior: smooth; }
.page { display: none; }
.page.active { display: block; animation: fadeIn 0.4s ease; }

@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

/* Dashboard Cards */
.hero { background: linear-gradient(135deg, var(--primary), var(--secondary)); padding: 25px; border-radius: 20px; text-align: center; margin-bottom: 20px; box-shadow: 0 10px 20px rgba(0,0,0,0.3); }
.stat-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 20px; }
.stat-card { background: var(--card); padding: 15px; border-radius: 15px; text-align: center; border: 1px solid rgba(255,255,255,0.1); }

/* Chat Bubbles */
.chat-container { display: flex; flex-direction: column; gap: 10px; }
.msg { padding: 10px 14px; border-radius: 18px; max-width: 80%; font-size: 14px; line-height: 1.4; position: relative; }
.msg.me { align-self: flex-end; background: var(--secondary); border-bottom-right-radius: 2px; }
.msg.them { align-self: flex-start; background: var(--card); border-bottom-left-radius: 2px; border: 1px solid rgba(255,255,255,0.1); }
.msg b { display: block; font-size: 10px; margin-bottom: 3px; color: var(--primary); }

/* Bottom Multimedia Bar */
.input-dock { position: absolute; bottom: 85px; left: 10px; right: 10px; background: #1b202d; border-radius: 30px; padding: 8px 15px; display: flex; align-items: center; gap: 12px; border: 1px solid rgba(255,255,255,0.1); z-index: 110; }
.input-dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 15px; }

/* Navigation Icons */
nav { position: absolute; bottom: 0; left: 0; right: 0; height: 75px; background: #1b202d; display: flex; justify-content: space-around; align-items: center; border-top: 1px solid rgba(255,255,255,0.1); z-index: 120; }
nav button { background: none; border: none; color: #8696a0; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 5px; transition: 0.3s; }
nav button.active { color: var(--primary); transform: translateY(-5px); }
nav button i { font-size: 20px; }

/* User List */
.user-item { background: var(--card); padding: 12px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 15px; cursor: pointer; border: 1px solid transparent; transition: 0.3s; z-index: 50; }
.user-item:hover { border-color: var(--primary); background: rgba(255,255,255,0.1); }
.avatar { width: 45px; height: 45px; border-radius: 50%; background: #2a3942; display: flex; align-items: center; justify-content: center; font-weight: bold; border: 2px solid var(--primary); }
</style>
</head>
<body>

<div class="bg-glow"></div>
<div class="app">
    <header>
        <h1>INFINITY CONNECT</h1>
        <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--accent); cursor:pointer;"></i>
    </header>

    <div class="main-view" id="scrollArea">
        
        <div id="loginPage" class="page active">
            <div style="text-align:center; padding-top:60px;">
                <i class="fa-solid fa-user-shield" style="font-size:60px; color:var(--primary);"></i>
                <h2 style="margin-top:20px;">Secure Access</h2>
                <input type="text" id="uName" placeholder="Enter Username..." style="width:80%; padding:16px; border-radius:15px; border:1px solid var(--primary); background:rgba(0,0,0,0.4); color:white; margin-top:20px; outline:none;">
                <button onclick="login()" style="width:80%; padding:16px; border-radius:15px; background:var(--primary); color:white; border:none; font-weight:bold; margin-top:20px; cursor:pointer;">SYNC IDENTITY</button>
            </div>
        </div>

        <div id="home" class="page">
            <div class="hero">
                <h2 id="dashUser">Welcome</h2>
                <p>System Online | Real-time Encrypted</p>
            </div>
            <div class="stat-grid">
                <div class="stat-card"><i class="fa-solid fa-wifi" style="color:var(--primary)"></i><br><b id="onlineCount">0</b><br><small>Online</small></div>
                <div class="stat-card"><i class="fa-solid fa-layer-group" style="color:var(--accent)"></i><br><b id="groupCount">0</b><br><small>Groups</small></div>
            </div>
            <h3>Quick Actions</h3>
            <div class="user-item" onclick="openPage('users', document.getElementById('uBtn'))">
                <div class="avatar"><i class="fa-solid fa-search"></i></div>
                <div><b>Find People</b><br><small>Browse active network</small></div>
            </div>
        </div>

        <div id="chat" class="page">
            <div style="text-align:center; padding:10px; color:var(--primary); font-weight:bold;" id="chatTitle">Global Channel</div>
            <div class="chat-container" id="chatBox"></div>
        </div>

        <div id="users" class="page">
            <h3 style="margin-bottom:15px;">Active Nodes</h3>
            <div id="userList"></div>
        </div>

    </div>

    <div class="input-dock" id="inputPanel" style="display:none;">
        <i class="fa-solid fa-circle-plus" style="color:var(--primary); cursor:pointer;"></i>
        <input type="text" id="msgInp" placeholder="Type a message...">
        <i class="fa-solid fa-microphone" style="color:var(--primary); cursor:pointer;"></i>
        <i class="fa-solid fa-paper-plane" style="color:var(--primary); cursor:pointer;" onclick="send()"></i>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="openPage('home', this)" class="active"><i class="fa-solid fa-house"></i><span>Home</span></button>
        <button onclick="openPage('chat', this)" id="cBtn"><i class="fa-solid fa-comments"></i><span>Chat</span></button>
        <button onclick="openPage('users', this)" id="uBtn"><i class="fa-solid fa-user-group"></i><span>Users</span></button>
        <button onclick="alert('Settings Coming Soon!')"><i class="fa-solid fa-sliders"></i><span>Settings</span></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
    apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
    databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("inc_user");
let room = "Global_Lobby";
let isPrivate = false;

if(user) startApp();

window.login = () => {
    const val = document.getElementById("uName").value.trim();
    if(!val) return;
    user = val;
    localStorage.setItem("inc_user", val);
    startApp();
};

function startApp() {
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("home").classList.add("active");
    document.getElementById("navbar").style.display = "flex";
    document.getElementById("dashUser").innerText = "Hi, " + user + "!";
    
    set(ref(db, "users/" + user), { online: true });
    onDisconnect(ref(db, "users/" + user)).set({ online: false });
    loadCoreData();
}

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    document.getElementById('inputPanel').style.display = (id === 'chat') ? 'flex' : 'none';
    if(id === 'chat') syncChat();
};

function loadCoreData() {
    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("userList");
        let count = 0; list.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online) {
                count++;
                if(u.key !== user) {
                    const d = document.createElement("div");
                    d.className = "user-item";
                    d.innerHTML = `<div class="avatar">${u.key[0].toUpperCase()}</div><div><b>${u.key}</b><br><small style="color:var(--primary)">Available</small></div>`;
                    // Fix: User click logic
                    d.addEventListener('click', () => {
                        room = [user, u.key].sort().join("_");
                        isPrivate = true;
                        document.getElementById("chatTitle").innerText = "Chat with " + u.key;
                        openPage('chat', document.getElementById('cBtn'));
                    });
                    list.appendChild(d);
                }
            }
        });
        document.getElementById("onlineCount").innerText = count;
    });
}

function syncChat() {
    const path = isPrivate ? "chats/" : "groups/";
    onValue(ref(db, path + room), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div");
            div.className = "msg " + (d.from === user ? "me" : "them");
            div.innerHTML = `<b>${d.from}</b>${d.text}`;
            box.appendChild(div);
        });
        document.getElementById('scrollArea').scrollTop = document.getElementById('scrollArea').scrollHeight;
    });
}

window.send = () => {
    const inp = document.getElementById("msgInp");
    if(!inp.value) return;
    const path = isPrivate ? "chats/" : "groups/";
    push(ref(db, path + room), { from: user, text: inp.value });
    inp.value = "";
};

window.logout = () => { localStorage.removeItem("inc_user"); location.reload(); };
</script>
</body>
</html>
