<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | Admin God Mode</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#c026d3; --s:#0ea5e9; --bg:#05050a; --acc:#f43f5e; --glass:rgba(15,15,25,0.9); --border:rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Inter', sans-serif; transition: 0.3s; -webkit-tap-highlight-color: transparent; }
body { margin:0; background:var(--bg); color:#eee; overflow:hidden; height: 100dvh; background-image: radial-gradient(circle at top right, #1e1b4b, #05050a); }

/* ALERTS */
#freezeOverlay { display:none; position:fixed; inset:0; background:#000; z-index:99999; flex-direction:column; align-items:center; justify-content:center; color:var(--acc); text-align:center; padding:20px; backdrop-filter: blur(50px); }

.app { max-width:480px; margin:auto; height:100dvh; display:flex; flex-direction:column; position:relative; border-left: 1px solid var(--border); border-right: 1px solid var(--border); }

/* HEADER */
header { padding:45px 15px 15px; background:rgba(0,0,0,0.7); backdrop-filter: blur(20px); border-bottom: 1px solid var(--border); display:flex; justify-content:space-between; align-items:center; }
header h1 { margin:0; font-size:18px; font-weight:900; background: linear-gradient(to right, #fff, var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

/* PAGES */
.page { display:none; padding:15px; flex:1; overflow-y:auto; padding-bottom:100px; -webkit-overflow-scrolling: touch; }
.page.active { display:block; animation: fadeIn 0.3s ease; }
@keyframes fadeIn { from { opacity:0; transform:translateY(10px); } to { opacity:1; transform:translateY(0); } }

/* LOGIN */
#loginPage { position:fixed; inset:0; background:#000; z-index:10000; display:flex; flex-direction:column; align-items:center; justify-content:center; padding:30px; }
#loginPage img { width:110px; height:110px; border-radius:50%; border:2px solid var(--s); box-shadow: 0 0 30px var(--p); margin-bottom:20px; }
#loginPage input { width:100%; max-width:280px; padding:15px; border-radius:15px; background:var(--glass); border:1px solid var(--border); color:#fff; text-align:center; margin-bottom:15px; font-size:16px; outline:none; }
#loginPage button { width:100%; max-width:280px; padding:15px; border:none; border-radius:15px; background: linear-gradient(90deg, var(--p), var(--s)); color:#fff; font-weight:900; }

/* CARDS & CHAT */
.card { background:var(--glass); padding:15px; border-radius:20px; border:1px solid var(--border); margin-bottom:12px; }
#chatBox { height:calc(100dvh - 280px); overflow-y:auto; display:flex; flex-direction:column; gap:10px; padding-bottom:10px; }
.msg { padding:12px 14px; border-radius:18px; font-size:13.5px; max-width:85%; line-height:1.4; position:relative; border: 1px solid var(--border); word-wrap: break-word; }
.msg.me { background: var(--p); align-self:flex-end; border-bottom-right-radius:2px; border:none; box-shadow: 0 4px 10px rgba(0,0,0,0.3); }
.msg img { max-width:100%; border-radius:12px; margin-top:5px; border:1px solid var(--border); }
.msg b { display:block; font-size:9px; color:var(--s); margin-bottom:3px; text-transform:uppercase; letter-spacing:1px; }

/* INPUT */
.input-row { display:flex; gap:8px; background:var(--glass); padding:8px; border-radius:25px; border:1px solid var(--border); align-items: center; margin-bottom: 5px; }
.input-row input { flex:1; background:transparent; border:none; color:#fff; outline:none; padding:8px; font-size:14px; }
.input-row button { width:42px; height:42px; border-radius:50%; border:none; background:var(--s); color:#fff; }

/* NAV */
nav { position:fixed; bottom:15px; left:50%; transform:translateX(-50%); width:92%; background:rgba(10,10,20,0.95); padding:12px 0; border-radius:25px; border:1px solid var(--border); display:flex; justify-content:space-around; backdrop-filter: blur(20px); z-index:2000; }
nav button { background:none; border:none; font-size:20px; color:#475569; }
nav button.active { color:var(--s); transform:translateY(-3px); }

/* ADMIN */
.admin-card { background:rgba(244,63,94,0.05); border:1px solid var(--acc); padding:12px; border-radius:15px; margin-bottom:10px; font-size:12px; display:flex; justify-content:space-between; align-items:center; }
.god-tag { background:var(--acc); color:#fff; padding:2px 6px; border-radius:4px; font-size:9px; font-weight:bold; }
</style>
</head>
<body>

<div id="freezeOverlay"><i class="fa-solid fa-skull-crossbones" style="font-size:60px"></i><h2>ACCESS DENIED</h2><p>Your node has been terminated by Admin786</p></div>

<div class="app">
    <div id="loginPage">
        <img src="https://i.pinimg.com/736x/8e/4a/1b/8e4a1b802a8069d273e04098675402a7.jpg">
        <h1>Live Connect</h1>
        <input type="text" id="usernameInput" placeholder="User Identity">
        <button onclick="login()">ESTABLISH LINK</button>
    </div>

    <header>
        <h1>LIVE CONNECT <span id="godBadge" class="god-tag" style="display:none">GOD</span></h1>
        <button onclick="logout()" style="color:var(--acc);"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="home" class="page active">
        <div class="card" style="background:linear-gradient(135deg, rgba(192,38,211,0.2), rgba(14,165,233,0.2)); text-align:center;">
            <p style="margin:0; font-size:14px;">Welcome Back,</p>
            <h3 style="margin:5px 0; color:var(--s)" id="dashUser">...</h3>
        </div>
        <div class="card" style="display:flex; justify-content:space-between; font-size:14px;">
            <span>Active Nodes:</span><b id="onlineCount" style="color:var(--s)">0</b>
        </div>
        
        <div id="adminPanel" style="display:none">
            <p style="color:var(--acc); font-size:11px; font-weight:900; margin:15px 0 5px 5px;">GHOST CONSOLE</p>
            <div id="adminUserList"></div>
            <button onclick="wipeData()" style="width:100%; padding:15px; background:var(--acc); border:none; border-radius:15px; color:white; font-weight:900; margin-top:10px;">NUCLEAR WIPE</button>
        </div>
    </div>

    <div id="chat" class="page">
        <div class="card" style="display:flex; align-items:center; gap:15px; padding:10px 15px; margin-bottom:10px;">
            <button onclick="openPage('users')" style="background:none; border:none; color:var(--s)"><i class="fa-solid fa-arrow-left"></i></button>
            <span id="chatWith" style="font-weight:900; font-size:14px;">SELECT NODE</span>
        </div>
        <div id="chatBox"></div>
        <div class="input-row">
            <label for="imgInput" style="color:var(--s); padding:0 10px; cursor:pointer; font-size:18px;"><i class="fa-solid fa-camera"></i></label>
            <input type="file" id="imgInput" style="display:none" accept="image/*" onchange="sendImg(this)">
            <input id="msgInput" placeholder="Secure message…">
            <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="users" class="page">
        <p style="color:#475569; font-size:11px; font-weight:900; margin-bottom:15px; letter-spacing:1px;">ONLINE ENTITIES</p>
        <div id="userList"></div>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-ghost"></i></button>
        <button onclick="openPage('chat',this)" id="chatBtn"><i class="fa-solid fa-comment-dots"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.firebasestorage.app",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");
let target = "";

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    localStorage.setItem("lc_user", val);
    location.reload();
};

if(user) {
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("dashUser").textContent = user;

    // UPDATE STATUS
    fetch('https://ipapi.co/json/').then(r=>r.json()).then(d=>{
        update(ref(db, "users/"+user), { name:user, online:true, ip:d.ip||'Unknown', city:d.city||'Ghost', frozen:false });
    });
    onDisconnect(ref(db, "users/"+user)).update({ online:false });

    // ADMIN PRIVILEGE
    if(user === "Admin786") {
        document.getElementById("adminPanel").style.display = "block";
        document.getElementById("godBadge").style.display = "inline";
    }

    // CHECK FREEZE
    onValue(ref(db, "users/"+user+"/frozen"), snap => {
        document.getElementById("freezeOverlay").style.display = snap.val() ? "flex" : "none";
    });

    // LOAD NODES
    onValue(ref(db, "users"), snap => {
        const uList = document.getElementById("userList");
        const aList = document.getElementById("adminUserList");
        let online = 0; uList.innerHTML = ""; aList.innerHTML = "";
        
        snap.forEach(u => {
            const d = u.val();
            if(d.online) online++;
            if(u.key === user) return;

            uList.innerHTML += `<div class="card" onclick="startChat('${u.key}')" style="display:flex; justify-content:space-between; align-items:center;">
                <span><i class="fa-solid fa-circle" style="color:${d.online?'#22c55e':'#333'}; font-size:10px; margin-right:8px;"></i> ${d.name}</span>
                <i class="fa-solid fa-chevron-right" style="opacity:0.3"></i>
            </div>`;

            if(user === "Admin786") {
                aList.innerHTML += `<div class="admin-card">
                    <span><b>${d.name}</b><br><small>${d.ip} | ${d.city}</small></span>
                    <div>
                        <button onclick="startChat('${u.key}')" style="background:var(--s); border:none; padding:6px 10px; border-radius:8px; font-weight:bold;">SPY</button>
                        <button onclick="setFreeze('${u.key}', ${!d.frozen})" style="background:var(--acc); border:none; padding:6px 10px; border-radius:8px; color:white; margin-left:5px;"><i class="fa-solid fa-lock"></i></button>
                    </div>
                </div>`;
            }
        });
        document.getElementById("onlineCount").textContent = online;
    });
}

window.startChat = (id) => {
    target = id;
    document.getElementById("chatWith").textContent = id.toUpperCase();
    openPage('chat', document.getElementById('chatBtn'));
    const chatID = [user, target].sort().join("_");
    onValue(ref(db, "messages/" + chatID), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const v = m.val();
            const side = v.from === user ? 'me' : 'them';
            let content = v.type === 'image' ? `<img src="${v.text}">` : v.text;
            box.innerHTML += `<div class="msg ${side}"><b>${v.from}</b>${content}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value || !target) return;
    const chatID = [user, target].sort().join("_");
    push(ref(db, "messages/" + chatID), { from: user, text: inp.value, type: 'text' });
    inp.value = "";
};

window.sendImg = (input) => {
    const file = input.files[0]; if(!file) return;
    const reader = new FileReader();
    reader.onload = (e) => {
        const chatID = [user, target].sort().join("_");
        push(ref(db, "messages/" + chatID), { from: user, text: e.target.result, type: 'image' });
    };
    reader.readAsDataURL(file);
};

window.setFreeze = (id, state) => { update(ref(db, "users/"+id), { frozen: state }); };
window.wipeData = () => { if(confirm("DESTROY DATABASE?")) { remove(ref(db, "messages")); remove(ref(db, "users")); location.reload(); } };
window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    if(btn) { document.querySelectorAll('nav button').forEach(b => b.classList.remove('active')); btn.classList.add('active'); }
};
window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
