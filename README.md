<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | Ghost Protocol V8</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#c026d3; --s:#0ea5e9; --bg:#05050a; --acc:#f43f5e; --glass:rgba(15,15,25,0.7); --border:rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Inter', system-ui, sans-serif; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
body { margin:0; background:var(--bg); color:#f8fafc; overflow:hidden; background-image: radial-gradient(circle at 0% 0%, #1e1b4b 0%, #05050a 100%); }

/* OVERLAYS */
#freezeOverlay { display:none; position:fixed; inset:0; background:rgba(0,0,0,0.98); z-index:50000; flex-direction:column; align-items:center; justify-content:center; color:#f43f5e; text-align:center; backdrop-filter: blur(40px); }
#hackOverlay { display:none; position:fixed; inset:0; background:#000; z-index:30000; color:#22c55e; padding:30px; font-family: 'Courier New', monospace; font-size:13px; }

.app { max-width:480px; margin:auto; height:100vh; display:flex; flex-direction:column; position:relative; border-left: 1px solid var(--border); border-right: 1px solid var(--border); }

/* MODERN LOGIN */
#loginPage { position:fixed; inset:0; background:#000; z-index:10000; display:flex; flex-direction:column; align-items:center; justify-content:center; padding:40px; }
#loginPage img { width:160px; height:160px; border-radius:50%; border:2px solid var(--s); box-shadow: 0 0 60px rgba(192, 38, 211, 0.4); margin-bottom:30px; }
#loginPage h1 { font-size: 32px; font-weight: 900; background: linear-gradient(to right, #fff, var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
#loginPage input { width:100%; max-width:320px; padding:20px; border-radius:20px; background:var(--glass); border:1px solid var(--border); color:#fff; text-align:center; margin-bottom:20px; outline:none; font-size: 16px; }
#loginPage button { width:100%; max-width:320px; padding:20px; border:none; border-radius:20px; background: linear-gradient(135deg, var(--p), var(--s)); color:#fff; font-weight:900; cursor:pointer; box-shadow: 0 10px 20px rgba(0,0,0,0.4); }

header { padding:50px 25px 20px; background:rgba(0,0,0,0.3); backdrop-filter: blur(20px); border-bottom: 1px solid var(--border); display:flex; justify-content:space-between; align-items:center; }
header h2 { margin:0; font-size:22px; font-weight: 900; letter-spacing: -1px; }

.page { display:none; padding:20px; flex:1; overflow-y:auto; padding-bottom:120px; scrollbar-width: none; }
.page.active { display:block; animation: slideUp 0.5s ease; }
@keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

/* CARDS */
.card { background:var(--glass); padding:20px; border-radius:30px; border:1px solid var(--border); margin-bottom:15px; box-shadow: 0 4px 20px rgba(0,0,0,0.2); }

/* CHAT SYSTEM */
#chatHeader { padding:15px; background:var(--glass); border-radius:25px; margin-bottom:15px; display:flex; align-items:center; gap:15px; border:1px solid var(--border); }
#chatBox { height:50vh; overflow-y:auto; display:flex; flex-direction:column; gap:12px; }
.msg { padding:14px 18px; border-radius:24px; font-size:14px; max-width:85%; position:relative; line-height: 1.6; }
.msg.me { background: linear-gradient(135deg, var(--p), #701a75); align-self:flex-end; border-bottom-right-radius:4px; box-shadow: 0 4px 15px rgba(192, 38, 211, 0.2); }
.msg.them { background:#1e1e2e; align-self:flex-start; border-bottom-left-radius:4px; border:1px solid var(--border); }
.msg img { max-width: 100%; border-radius: 15px; margin-top: 8px; border: 1px solid rgba(255,255,255,0.1); }
.msg b { display:block; font-size:10px; margin-bottom:5px; color:var(--s); letter-spacing: 1px; }

.input-area { display:flex; gap:10px; background:var(--glass); padding:10px; border-radius:30px; border:1px solid var(--border); align-items: center; margin-top: 10px; }
.input-area input { flex:1; background:transparent; border:none; color:#fff; padding:12px; outline:none; font-size: 15px; }
.input-area label { cursor: pointer; color: var(--s); font-size: 20px; margin-left: 10px; }
.input-area button { width:50px; height:50px; border-radius:50%; border:none; background:var(--s); color:#fff; cursor:pointer; display:flex; align-items:center; justify-content:center; }

/* NAV */
nav { position:fixed; bottom:25px; left:50%; transform:translateX(-50%); width:90%; max-width: 430px; background:rgba(15,15,25,0.85); padding:18px 0; border-radius:35px; border:1px solid var(--border); display:flex; justify-content:space-around; backdrop-filter: blur(30px); z-index: 2000; }
nav button { background:none; border:none; font-size:24px; color:#475569; cursor:pointer; }
nav button.active { color:var(--s); transform: translateY(-5px); }

/* ENTITY CARDS */
.entity { background:var(--glass); padding:18px; border-radius:25px; border:1px solid var(--border); margin-bottom:12px; display:flex; justify-content:space-between; align-items:center; cursor:pointer; }
.entity:active { transform: scale(0.95); }
.admin-card { border: 1px solid var(--acc) !important; background: rgba(244, 63, 94, 0.05) !important; }
.status-dot { width: 10px; height: 10px; border-radius: 50%; display: inline-block; margin-right: 10px; box-shadow: 0 0 10px currentColor; }
</style>
</head>
<body>

<div id="freezeOverlay"><i class="fa-solid fa-burst" style="font-size:100px; color:#f43f5e"></i><h1 style="font-size:32px">ENCRYPTION TERMINATED</h1><p style="opacity:0.7">Admin786 has locked your node.<br>Reason: Unauthorized Intelligence.</p></div>
<div id="hackOverlay"></div>

<div class="app">
    <div id="loginPage">
        <img src="https://i.pinimg.com/736x/8e/4a/1b/8e4a1b802a8069d273e04098675402a7.jpg"> <h1>Live Connect</h1>
        <p style="color:#64748b; font-size: 12px; letter-spacing: 4px; margin-bottom: 20px;">SYSTEM OVERRIDE V8</p>
        <input type="text" id="usernameInput" placeholder="Enter Secure ID" autocomplete="off">
        <button onclick="initiateHack()">ESTABLISH LINK</button>
    </div>

    <header>
        <h2>LIVE CONNECT <span id="admTag" style="display:none; color:var(--acc); font-size:10px; vertical-align:middle; margin-left:10px;">• GOD MODE</span></h2>
        <button onclick="logout()" style="background:none; border:none; color:var(--acc); font-size:22px;"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="home" class="page active">
        <div class="card" style="background: linear-gradient(135deg, rgba(192,38,211,0.1), rgba(14,165,233,0.1));">
            <h3 style="margin:0;">Identity: <span id="dashUser" style="color:var(--s);">...</span></h3>
            <p style="font-size:12px; opacity:0.6; margin-top:10px;">Network: Encrypted | Status: Ghost Online</p>
        </div>

        <div class="card" style="display:flex; justify-content:space-between; align-items:center;">
            <span>Active Nodes:</span>
            <span id="onlineCount" style="color:var(--s); font-weight:900; font-size: 20px;">0</span>
        </div>

        <div id="adminPanel" style="display:none;">
            <h4 style="color:var(--acc); margin-left:10px;"><i class="fa-solid fa-skull-crossbones"></i> GHOST SPY CONSOLE</h4>
            <div id="adminUserList"></div>
            <button onclick="wipeAll()" style="width:100%; padding:20px; background:var(--acc); border:none; border-radius:25px; color:white; font-weight:900; margin-top:15px; box-shadow: 0 10px 20px rgba(244,63,94,0.3);">EXECUTE NUCLEAR WIPE</button>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatHeader">
            <button onclick="openPage('usersPage')" style="background:none; border:none; color:var(--s); font-size: 18px;"><i class="fa-solid fa-arrow-left"></i></button>
            <span id="chatWith" style="font-weight:900; letter-spacing:1px; color:#fff;">SELECT NODE</span>
        </div>
        <div id="chatBox"></div>
        <div class="input-area">
            <label for="imgInput"><i class="fa-solid fa-image"></i></label>
            <input type="file" id="imgInput" style="display:none" accept="image/*" onchange="sendImage(this)">
            <input id="msgInput" placeholder="Enter encrypted packet..."/>
            <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="usersPage" class="page">
        <h3 style="color:#64748b; font-size:13px; letter-spacing:2px; margin-bottom:20px;">CONNECTED ENTITIES</h3>
        <div id="publicUserList"></div>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openPage('usersPage',this)"><i class="fa-solid fa-ghost"></i></button>
        <button onclick="openPage('chat',this)" id="chatNav"><i class="fa-solid fa-terminal"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");
let targetUser = ""; 

window.initiateHack = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    const overlay = document.getElementById("hackOverlay");
    overlay.style.display = "block";
    const logs = ["BYPASSING NODES...", "LOGGING GEOLOCATION...", "HIJACKING IP PROTOCOL...", "INJECTING GOD_MODE...", "ACCESS GRANTED!"];
    let i = 0;
    const itv = setInterval(() => {
        overlay.innerHTML += `<p style='margin:8px 0'>[LOG]: ${logs[i]}</p>`; i++;
        if(i >= logs.length) { clearInterval(itv); setTimeout(() => { localStorage.setItem("lc_user", val); location.reload(); }, 600); }
    }, 300);
};

if(user) {
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("dashUser").textContent = user;
    
    // IP TRACING
    fetch('https://ipapi.co/json/').then(r=>r.json()).then(data=>{
        update(ref(db, "users/"+user), { name:user, online:true, ip:data.ip, city:data.city, frozen:false });
    });
    onDisconnect(ref(db, "users/"+user)).update({ online:false });

    // LOCK STATUS
    onValue(ref(db, "users/"+user+"/frozen"), snap => {
        document.getElementById("freezeOverlay").style.display = snap.val() ? "flex" : "none";
    });

    // ADMIN PRIVILEGE
    if(user === "Admin786") {
        document.getElementById("adminPanel").style.display = "block";
        document.getElementById("admTag").style.display = "inline";
    }

    // LIST DATA
    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("publicUserList"); 
        const adminList = document.getElementById("adminUserList");
        let count = 0; list.innerHTML = ""; adminList.innerHTML = "";
        
        snap.forEach(u => {
            const d = u.val();
            if(d.online) count++;
            if(u.key === user) return;

            // User View
            list.innerHTML += `<div class="entity" onclick="startChat('${u.key}')">
                <span><div class="status-dot" style="color:${d.online?'#22c55e':'#475569'}"></div> ${d.name}</span>
                <i class="fa-solid fa-chevron-right" style="opacity:0.3"></i>
            </div>`;

            // Admin Ghost Control
            if(user === "Admin786") {
                adminList.innerHTML += `<div class="card admin-card">
                    <div style="display:flex; justify-content:space-between; align-items:center;">
                        <span><b>${d.name}</b><br><small style="color:var(--s)">${d.ip} | ${d.city}</small></span>
                        <div>
                            <button onclick="startChat('${u.key}')" style="background:var(--s); border:none; padding:8px 12px; border-radius:10px; color:#000; font-weight:bold;"><i class="fa-solid fa-eye"></i></button>
                            <button onclick="freezeUser('${u.key}', ${!d.frozen})" style="background:var(--acc); border:none; padding:8px 12px; border-radius:10px; color:white; font-weight:bold;"><i class="fa-solid fa-lock${d.frozen?'':''-open}"></i></button>
                        </div>
                    </div>
                </div>`;
            }
        });
        document.getElementById("onlineCount").textContent = count;
    });
}

// CHAT CORE
window.startChat = (target) => {
    targetUser = target;
    document.getElementById("chatWith").textContent = target.toUpperCase();
    openPage('chat', document.getElementById('chatNav'));
    loadMessages();
};

function loadMessages() {
    const chatID = [user, targetUser].sort().join("_");
    onValue(ref(db, "messages/" + chatID), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const v = m.val();
            const type = v.from === user ? 'me' : 'them';
            let content = v.type === 'image' ? `<img src="${v.text}">` : v.text;
            box.innerHTML += `<div class="msg ${type}"><b>${v.from}</b>${content}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value || !targetUser) return;
    const chatID = [user, targetUser].sort().join("_");
    push(ref(db, "messages/" + chatID), { from: user, text: inp.value, type: 'text' });
    inp.value = "";
};

window.sendImage = (input) => {
    const file = input.files[0]; if(!file || !targetUser) return;
    const reader = new FileReader();
    reader.onload = (e) => {
        const chatID = [user, targetUser].sort().join("_");
        push(ref(db, "messages/" + chatID), { from: user, text: e.target.result, type: 'image' });
    };
    reader.readAsDataURL(file);
};

// ACTIONS
window.freezeUser = (target, state) => { update(ref(db, "users/"+target), { frozen: state }); };
window.wipeAll = () => { if(confirm("NUCLEAR WIPE: DESTROY ALL DATA?")) { remove(ref(db, "messages")); remove(ref(db, "users")); localStorage.clear(); location.reload(); } };

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    if(btn) {
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }
};
window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
