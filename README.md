<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Infinity Pro Messenger 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --primary: #00d2ff; --secondary: #3a7bd5; --bg: #0b0e14;
  --card: #1b202d; --text: #ffffff; --accent: #ff007a; --glass: rgba(255,255,255,0.05);
}
body, html { margin:0; padding:0; height:100%; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); overflow: hidden; }

.app { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Header */
header { padding: 15px 20px; background: var(--card); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--glass); z-index: 1000; }
header h1 { margin: 0; font-size: 1.2rem; color: var(--primary); letter-spacing: 1px; }

/* Main Content Area */
.content { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 160px; scroll-behavior: smooth; }
.page { display: none; }
.page.active { display: block; animation: fadeIn 0.3s ease; }

@keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

/* Dashboard / Home */
.hero-card { background: linear-gradient(135deg, var(--primary), var(--secondary)); padding: 25px; border-radius: 20px; text-align: center; margin-bottom: 20px; box-shadow: 0 10px 20px rgba(0,0,0,0.3); }
.stat-box { background: var(--card); padding: 15px; border-radius: 15px; display: flex; justify-content: space-between; margin-bottom: 10px; border: 1px solid var(--glass); }

/* Chat UI */
.chat-container { display: flex; flex-direction: column; gap: 10px; }
.msg { padding: 10px 14px; border-radius: 15px; max-width: 75%; font-size: 14px; position: relative; }
.msg.me { align-self: flex-end; background: var(--secondary); border-bottom-right-radius: 2px; }
.msg.them { align-self: flex-start; background: var(--card); border: 1px solid var(--glass); border-bottom-left-radius: 2px; }
.msg b { display: block; font-size: 10px; color: var(--primary); margin-bottom: 2px; }

/* Bottom Multimedia Input Bar */
.input-dock { position: absolute; bottom: 80px; left: 10px; right: 10px; background: #242b3d; padding: 10px 15px; border-radius: 30px; display: flex; align-items: center; gap: 12px; border: 1px solid var(--glass); z-index: 2000; box-shadow: 0 5px 15px rgba(0,0,0,0.5); }
.input-dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 15px; }
.input-dock i { font-size: 20px; color: var(--primary); cursor: pointer; transition: 0.2s; }
.input-dock i:hover { transform: scale(1.1); }

/* Bottom Nav Bar */
nav { position: absolute; bottom: 0; left: 0; right: 0; height: 70px; background: var(--card); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--glass); z-index: 2000; }
nav button { background: none; border: none; color: #8696a0; display: flex; flex-direction: column; align-items: center; gap: 4px; cursor: pointer; font-size: 18px; transition: 0.3s; }
nav button.active { color: var(--primary); transform: translateY(-3px); }
nav button span { font-size: 10px; font-weight: 600; }

/* User Items */
.user-item { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 15px; cursor: pointer; border: 1px solid transparent; }
.user-item:hover { border-color: var(--primary); background: rgba(255,255,255,0.1); }
.status-dot { width: 10px; height: 10px; background: #00ffcc; border-radius: 50%; box-shadow: 0 0 10px #00ffcc; }

/* Settings */
.set-item { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; justify-content: space-between; }
</style>
</head>
<body>

<div class="app">
    <header>
        <h1>INFINITY PRO</h1>
        <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--accent); cursor:pointer;"></i>
    </header>

    <div class="content" id="scrollArea">
        
        <div id="loginPage" class="page active">
            <div style="text-align:center; padding-top:60px;">
                <i class="fa-solid fa-rocket" style="font-size:60px; color:var(--primary);"></i>
                <h2>Cloud Sync Login</h2>
                <input type="text" id="uName" placeholder="Create Username..." style="width:80%; padding:15px; border-radius:15px; border:1px solid var(--primary); background:transparent; color:white; margin-top:20px; outline:none;">
                <button onclick="login()" style="width:80%; padding:15px; border-radius:15px; background:var(--primary); color:white; border:none; font-weight:bold; margin-top:20px; cursor:pointer;">INITIALIZE</button>
            </div>
        </div>

        <div id="home" class="page">
            <div class="hero-card">
                <h2 id="welcomeText">Welcome</h2>
                <p>All systems operational</p>
            </div>
            <div class="stat-box"><span>Network Users</span><b id="onlineCount">0</b></div>
            <div class="stat-box"><span>Active Groups</span><b id="groupCount">0</b></div>
            <button onclick="openPage('users', document.getElementById('uBtn'))" style="width:100%; padding:15px; border-radius:15px; background:var(--glass); border:1px solid var(--primary); color:white; cursor:pointer;">Find People</button>
        </div>

        <div id="chat" class="page">
            <div id="chatHeader" style="text-align:center; padding:5px; color:var(--primary); font-weight:bold; font-size:14px; margin-bottom:10px;">Global Room</div>
            <div class="chat-container" id="chatBox"></div>
        </div>

        <div id="users" class="page">
            <h3>Online Explorers</h3>
            <div id="userList"></div>
        </div>

        <div id="settings" class="page">
            <h3>App Control</h3>
            <div class="set-item"><span>Dark Mode</span><i class="fa-solid fa-moon"></i></div>
            <div class="set-item" onclick="logout()"><span>Switch Account</span><i class="fa-solid fa-right-from-bracket"></i></div>
            <p style="text-align:center; font-size:12px; color:#555; margin-top:20px;">Version 2.5.0 Professional</p>
        </div>

    </div>

    <div class="input-dock" id="inputPanel" style="display:none;">
        <i class="fa-solid fa-camera" onclick="alert('Camera opening...')"></i>
        <input type="text" id="msgInp" placeholder="Type message...">
        <i class="fa-solid fa-microphone" id="micBtn"></i>
        <i class="fa-solid fa-paper-plane" onclick="send()"></i>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="openPage('home', this)" class="active"><i class="fa-solid fa-house"></i><span>Home</span></button>
        <button onclick="openPage('chat', this)" id="cBtn"><i class="fa-solid fa-comment-dots"></i><span>Chat</span></button>
        <button onclick="openPage('users', this)" id="uBtn"><i class="fa-solid fa-user-astronaut"></i><span>Users</span></button>
        <button onclick="openPage('settings', this)"><i class="fa-solid fa-sliders"></i><span>Settings</span></button>
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

let user = localStorage.getItem("inc_user");
let room = "Global_Base";
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
    document.getElementById("welcomeText").innerText = "Hello, " + user;
    
    set(ref(db, "users/" + user), { online: true });
    onDisconnect(ref(db, "users/" + user)).set({ online: false });
    loadData();
}

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    
    // Logic to show/hide input bar
    document.getElementById('inputPanel').style.display = (id === 'chat') ? 'flex' : 'none';
    if(id === 'chat') syncMessages();
};

function loadData() {
    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("userList");
        let count = 0; list.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online) {
                count++;
                if(u.key !== user) {
                    const d = document.createElement("div");
                    d.className = "user-item";
                    d.innerHTML = `<div class="status-dot"></div><b>${u.key}</b>`;
                    d.onclick = () => {
                        room = [user, u.key].sort().join("_");
                        isPrivate = true;
                        document.getElementById("chatHeader").innerText = "Private Chat: " + u.key;
                        openPage('chat', document.getElementById('cBtn'));
                    };
                    list.appendChild(d);
                }
            }
        });
        document.getElementById("onlineCount").innerText = count;
    });
}

function syncMessages() {
    const path = isPrivate ? "private/" + room : "global/";
    onValue(ref(db, path), snap => {
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
    if(!inp.value.trim()) return;
    const path = isPrivate ? "private/" + room : "global/";
    push(ref(db, path), { from: user, text: inp.value });
    inp.value = "";
};

window.logout = () => { localStorage.removeItem("inc_user"); location.reload(); };

</script>
</body>
</html>
