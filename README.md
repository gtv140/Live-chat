<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Infinity Pro Max 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --p: #00f2fe; --s: #4facfe; --bg: #080a12;
  --card: rgba(255, 255, 255, 0.06); --glass: rgba(255, 255, 255, 0.1);
  --text: #ffffff; --muted: #a0a0b0; --accent: #ff007a;
}
body, html { margin:0; padding:0; height:100%; font-family: 'Poppins', sans-serif; background: var(--bg); color: var(--text); overflow: hidden; }

/* Glowing Background */
.glow { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: radial-gradient(circle at 50% 50%, #1a1f3c 0%, #080a12 100%); z-index: -1; }

.app { max-width: 450px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Header */
header { padding: 20px; display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.3); backdrop-filter: blur(10px); border-bottom: 1px solid var(--glass); z-index: 1000; }
header h1 { font-size: 20px; font-weight: 800; background: linear-gradient(to right, var(--p), var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin:0; }

/* Scrollable Content */
.main { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 160px; scroll-behavior: smooth; }
.page { display: none; }
.page.active { display: block; animation: slideUp 0.4s ease-out; }

@keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

/* Stories Bar */
.stories { display: flex; gap: 15px; overflow-x: auto; padding-bottom: 15px; scrollbar-width: none; }
.story { min-width: 60px; height: 60px; border-radius: 50%; border: 2px solid var(--p); padding: 3px; cursor: pointer; }
.story img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }

/* Cards */
.card { background: var(--card); border: 1px solid var(--glass); padding: 20px; border-radius: 20px; margin-bottom: 15px; backdrop-filter: blur(5px); }
.hero { background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%); color: #000; font-weight: bold; }

/* Chat Bubbles */
.chat-box { display: flex; flex-direction: column; gap: 12px; }
.m { padding: 12px 16px; border-radius: 20px; max-width: 80%; font-size: 14px; box-shadow: 0 4px 15px rgba(0,0,0,0.2); }
.m.sent { align-self: flex-end; background: linear-gradient(135deg, var(--s), var(--p)); color: #000; border-bottom-right-radius: 4px; }
.m.rec { align-self: flex-start; background: var(--card); border: 1px solid var(--glass); border-bottom-left-radius: 4px; }
.m b { display: block; font-size: 10px; margin-bottom: 4px; opacity: 0.7; }

/* Modern Bottom Input Dock */
.input-dock { position: absolute; bottom: 85px; left: 15px; right: 15px; background: rgba(255,255,255,0.1); backdrop-filter: blur(20px); border-radius: 30px; padding: 10px 18px; display: flex; align-items: center; gap: 12px; border: 1px solid var(--glass); z-index: 2000; }
.input-dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 15px; }
.input-dock i { font-size: 20px; color: var(--p); cursor: pointer; transition: 0.2s; }
.input-dock i:hover { color: var(--accent); transform: scale(1.1); }

/* Navigation */
nav { position: absolute; bottom: 0; left: 0; right: 0; height: 75px; background: rgba(0,0,0,0.6); backdrop-filter: blur(15px); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--glass); z-index: 2000; }
nav button { background: none; border: none; color: var(--muted); display: flex; flex-direction: column; align-items: center; gap: 5px; cursor: pointer; transition: 0.3s; }
nav button.active { color: var(--p); transform: translateY(-5px); }
nav button i { font-size: 22px; }

/* User List */
.u-item { display: flex; align-items: center; gap: 15px; padding: 12px; background: var(--card); border-radius: 15px; margin-bottom: 10px; cursor: pointer; transition: 0.2s; border: 1px solid transparent; }
.u-item:hover { border-color: var(--p); transform: translateX(5px); }
.u-avatar { width: 45px; height: 45px; border-radius: 50%; background: linear-gradient(45deg, var(--p), var(--s)); display: flex; align-items: center; justify-content: center; font-weight: bold; color: #000; }
</style>
</head>
<body>

<div class="glow"></div>
<div class="app">
    <header>
        <h1>INFINITY PRO</h1>
        <div style="display:flex; gap:15px;">
            <i class="fa-solid fa-bell" style="color:var(--p)"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--accent)"></i>
        </div>
    </header>

    <div class="main" id="scrollArea">
        
        <div id="loginPage" class="page active">
            <div style="text-align:center; padding-top:80px;">
                <div style="width:100px; height:100px; background:var(--card); border-radius:50%; display:inline-flex; align-items:center; justify-content:center; margin-bottom:20px; border:2px solid var(--p);">
                    <i class="fa-solid fa-fingerprint" style="font-size:50px; color:var(--p)"></i>
                </div>
                <h2>Quantum Login</h2>
                <input type="text" id="uName" placeholder="Enter secure alias..." style="width:85%; padding:18px; border-radius:15px; border:1px solid var(--p); background:rgba(0,0,0,0.5); color:white; outline:none; margin-top:10px;">
                <button onclick="login()" style="width:85%; padding:18px; border-radius:15px; background:var(--p); color:black; border:none; font-weight:bold; margin-top:20px; cursor:pointer; box-shadow: 0 0 20px var(--p);">SYNC SESSION</button>
            </div>
        </div>

        <div id="home" class="page">
            <div class="stories">
                <div class="story"><img src="https://api.dicebear.com/7.x/avataaars/svg?seed=1"></div>
                <div class="story"><img src="https://api.dicebear.com/7.x/avataaars/svg?seed=2"></div>
                <div class="story"><img src="https://api.dicebear.com/7.x/avataaars/svg?seed=3"></div>
                <div class="story"><img src="https://api.dicebear.com/7.x/avataaars/svg?seed=4"></div>
            </div>
            <div class="card hero">
                <h2 id="greet" style="margin:0">Hi!</h2>
                <p style="margin:5px 0 0 0; opacity:0.8;">Your network is secured.</p>
            </div>
            <div style="display:grid; grid-template-columns: 1fr 1fr; gap:15px;">
                <div class="card" style="text-align:center;"><b>Online</b><br><span id="onlineNum" style="font-size:24px; color:var(--p)">0</span></div>
                <div class="card" style="text-align:center;"><b>Nodes</b><br><span style="font-size:24px; color:var(--accent)">Live</span></div>
            </div>
        </div>

        <div id="chat" class="page">
            <div style="display:flex; justify-content:space-between; align-items:center; padding-bottom:15px;">
                <h3 id="chatTitle" style="margin:0">Global Room</h3>
                <small style="color:var(--p)">Encrypted</small>
            </div>
            <div class="chat-box" id="chatBox"></div>
        </div>

        <div id="users" class="page">
            <h3>Network Directory</h3>
            <div id="uList"></div>
        </div>

    </div>

    <div class="input-dock" id="inputDock" style="display:none;">
        <i class="fa-solid fa-circle-plus"></i>
        <input type="text" id="msgInp" placeholder="Send a message...">
        <i class="fa-solid fa-microphone"></i>
        <i class="fa-solid fa-paper-plane" onclick="send()"></i>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="tab('home', this)" class="active"><i class="fa-solid fa-house-user"></i></button>
        <button onclick="tab('chat', this)" id="cBtn"><i class="fa-solid fa-comments"></i></button>
        <button onclick="tab('users', this)" id="uBtn"><i class="fa-solid fa-users"></i></button>
        <button onclick="alert('Settings Locked')"><i class="fa-solid fa-gear"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("pro_x_user");
let room = "Global_Lobby";
let isPriv = false;

if(user) start();

window.login = () => {
    const v = document.getElementById("uName").value.trim();
    if(!v) return;
    user = v;
    localStorage.setItem("pro_x_user", v);
    start();
};

function start() {
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("home").classList.add("active");
    document.getElementById("navbar").style.display = "flex";
    document.getElementById("greet").innerText = "Hi, " + user + "!";
    
    set(ref(db, "users/" + user), { online: true });
    onDisconnect(ref(db, "users/" + user)).set({ online: false });
    load();
}

window.tab = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    document.getElementById("inputDock").style.display = (id==='chat'?'flex':'none');
    if(id==='chat') sync();
};

function load() {
    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("uList");
        let count = 0; list.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online) {
                count++;
                if(u.key !== user) {
                    const d = document.createElement("div");
                    d.className = "u-item";
                    d.innerHTML = `<div class="u-avatar">${u.key[0].toUpperCase()}</div><div><b>${u.key}</b><br><small style="color:var(--p)">Online Now</small></div>`;
                    d.onclick = () => {
                        room = [user, u.key].sort().join("_");
                        isPriv = true;
                        document.getElementById("chatTitle").innerText = u.key;
                        tab('chat', document.getElementById('cBtn'));
                    };
                    list.appendChild(d);
                }
            }
        });
        document.getElementById("onlineNum").innerText = count;
    });
}

function sync() {
    const path = isPriv ? "private/" + room : "global/main";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div");
            div.className = "m " + (d.from === user ? "sent" : "rec");
            div.innerHTML = `<b>${d.from}</b>${d.text}`;
            box.appendChild(div);
        });
        const s = document.getElementById('scrollArea');
        s.scrollTop = s.scrollHeight;
    });
}

window.send = () => {
    const i = document.getElementById("msgInp");
    if(!i.value.trim()) return;
    const path = isPriv ? "private/" + room : "global/main";
    push(ref(db, path), { from: user, text: i.value });
    i.value = "";
};

window.logout = () => { localStorage.removeItem("pro_x_user"); location.reload(); };

</script>
</body>
</html>
