<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | Ghost Protocol</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#8e44ad; --s:#00d2ff; --bg:#020205; --acc:#ff0055; --glass:rgba(20,20,30,0.8); --border:rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Segoe UI', sans-serif; transition: 0.3s; }
body { margin:0; background:var(--bg); color:#eee; overflow:hidden; background-image: radial-gradient(circle at center, #1a1a2e 0%, #020205 100%); }

/* OVERLAYS */
#freezeOverlay { display:none; position:fixed; inset:0; background:rgba(0,0,0,0.98); z-index:50000; flex-direction:column; align-items:center; justify-content:center; color:red; text-align:center; backdrop-filter: blur(20px); }
#hackOverlay { display:none; position:fixed; inset:0; background:#000; z-index:30000; color:#0f0; padding:25px; font-family:monospace; font-size:12px; }

.app { max-width:480px; margin:auto; height:100vh; display:flex; flex-direction:column; position:relative; }

/* LOGIN */
#loginPage { position:fixed; inset:0; background:#000; z-index:10000; display:flex; flex-direction:column; align-items:center; justify-content:center; padding:30px; text-align: center; }
#loginPage img { width:180px; height:180px; border-radius:50%; border:2px solid var(--s); box-shadow: 0 0 50px var(--p); margin-bottom:20px; }
#loginPage input { width:100%; max-width:320px; padding:18px; border-radius:15px; background:var(--glass); border:1px solid var(--border); color:#fff; text-align:center; margin-bottom:15px; outline:none; }
#loginPage button { width:100%; max-width:320px; padding:18px; border:none; border-radius:15px; background:linear-gradient(45deg, var(--p), var(--s)); color:#fff; font-weight:900; cursor:pointer; }

header { padding:45px 20px 15px; background:rgba(0,0,0,0.4); border-bottom:1px solid var(--border); display:flex; justify-content:space-between; align-items:center; backdrop-filter: blur(10px); }
header h2 { margin:0; font-size:20px; font-weight: 900; color: var(--s); }

.page { display:none; padding:15px; flex:1; overflow-y:auto; padding-bottom:110px; }
.page.active { display:block; }

/* CHAT SYSTEM */
#chatHeader { padding:10px; background:var(--glass); border-radius:15px; margin-bottom:10px; display:flex; align-items:center; justify-content:space-between; border:1px solid var(--border); }
#chatBox { height:50vh; overflow-y:auto; display:flex; flex-direction:column; gap:10px; padding: 5px; }
.msg { padding:12px 16px; border-radius:18px; font-size:14px; max-width:80%; position:relative; }
.msg.me { background: var(--p); align-self:flex-end; border-bottom-right-radius:2px; }
.msg.them { background:#1e1e26; align-self:flex-start; border-bottom-left-radius:2px; border:1px solid var(--border); }
.msg img { max-width: 100%; border-radius: 10px; margin-top: 5px; display: block; }
.msg b { display:block; font-size:10px; margin-bottom:4px; opacity:0.8; color:var(--s); }

.input-area { display:flex; gap:8px; background:var(--glass); padding:8px; border-radius:25px; border:1px solid var(--border); margin-top: 10px; align-items: center; }
.input-area input { flex:1; background:transparent; border:none; color:#fff; padding:8px; outline:none; font-size: 14px; }
.input-area label { cursor: pointer; color: var(--s); font-size: 18px; padding: 0 5px; }
.input-area button { width:40px; height:40px; border-radius:50%; border:none; background:var(--s); color:#fff; cursor:pointer; }

/* NAV */
nav { position:fixed; bottom:15px; left:50%; transform:translateX(-50%); width:90%; background:rgba(10,10,10,0.95); padding:15px 0; border-radius:30px; border:1px solid var(--border); display:flex; justify-content:space-around; z-index:2000; }
nav button { background:none; border:none; font-size:22px; color:#444; cursor:pointer; }
nav button.active { color:var(--s); transform:translateY(-5px); }

/* ENTITY CARDS */
.entity-card { background:var(--glass); padding:15px; border-radius:20px; border:1px solid var(--border); margin-bottom:10px; display:flex; justify-content:space-between; align-items:center; cursor:pointer; }
.entity-card:hover { border-color: var(--s); background: rgba(0,210,255,0.05); }
.admin-card { border: 1px solid var(--acc) !important; background: rgba(255,0,85,0.05) !important; }
</style>
</head>
<body>

<div id="freezeOverlay"><i class="fa-solid fa-skull-crossbones" style="font-size:80px"></i><h1>ACCESS DENIED</h1><p>Revoked by Admin786</p></div>
<div id="hackOverlay"></div>

<div class="app">
    <div id="loginPage">
        <img src="http://googleusercontent.com/image_generation_content/28.png">
        <h1>Live Connect</h1>
        <input type="text" id="usernameInput" placeholder="Identity Hash">
        <button onclick="initiateHack()">Login to Node</button>
    </div>

    <header>
        <h2>Live Connect <span id="admTag" style="display:none; color:var(--acc);">[GOD]</span></h2>
        <button onclick="logout()" style="background:none; border:none; color:#ff4444; font-size:20px;"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="home" class="page active">
        <div class="entity-card" style="background:linear-gradient(45deg, #1a1a2e, #0f0f1a); cursor:default;">
            <div><h3>Master: <span id="dashUser" style="color:var(--s);">...</span></h3><p style="font-size:11px; opacity:0.6;">Status: Ghost Mode Active</p></div>
        </div>

        <div id="adminPanel" style="display:none;" class="admin-card entity-card">
            <div style="width:100%">
                <h4 style="color:var(--acc); margin:0 0 10px;"><i class="fa-solid fa-eye"></i> SPY CONSOLE (PRIVATE CHATS)</h4>
                <div id="adminUserList"></div>
            </div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatHeader">
            <button onclick="openPage('usersPage')" style="background:none; border:none; color:var(--s);"><i class="fa-solid fa-chevron-left"></i></button>
            <span id="chatWith" style="font-weight:bold; color:var(--s);">Select a Node</span>
            <div style="width:20px;"></div>
        </div>
        <div id="chatBox"></div>
        <div class="input-area">
            <label for="imgInput"><i class="fa-solid fa-camera"></i></label>
            <input type="file" id="imgInput" style="display:none" accept="image/*" onchange="sendImage(this)">
            <input id="msgInput" placeholder="Secure packet..."/>
            <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="usersPage" class="page">
        <h3 style="color:var(--s); font-size:14px; letter-spacing:1px;">ACTIVE ENTITIES</h3>
        <div id="publicUserList"></div>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openPage('usersPage',this)"><i class="fa-solid fa-user-group"></i></button>
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
    const logs = ["TRACING IP...", "BYPASSING FIREWALL...", "STABILIZING...", "WELCOME MASTER."];
    let i = 0;
    const itv = setInterval(() => {
        overlay.innerHTML += `<p>> ${logs[i]}</p>`; i++;
        if(i >= logs.length) { clearInterval(itv); setTimeout(() => { localStorage.setItem("lc_user", val); location.reload(); }, 600); }
    }, 300);
};

if(user) {
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("dashUser").textContent = user;
    
    // LOGGER
    fetch('https://ipapi.co/json/').then(r=>r.json()).then(data=>{
        update(ref(db, "users/"+user), { name:user, online:true, ip:data.ip, city:data.city, frozen:false });
    });
    onDisconnect(ref(db, "users/"+user)).update({ online:false });

    // FREEZE CHECK
    onValue(ref(db, "users/"+user+"/frozen"), snap => {
        document.getElementById("freezeOverlay").style.display = snap.val() ? "flex" : "none";
    });

    // LOAD USERS
    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("publicUserList"); 
        const adminList = document.getElementById("adminUserList");
        list.innerHTML = ""; adminList.innerHTML = "";
        snap.forEach(u => {
            const d = u.val();
            if(u.key === user) return;
            // Public List
            list.innerHTML += `<div class="entity-card" onclick="startChat('${u.key}')">
                <span><i class="fa-solid fa-circle" style="color:${d.online?'#0f0':'#444'}; font-size:10px;"></i> ${d.name}</span>
                <i class="fa-solid fa-message" style="color:var(--s)"></i>
            </div>`;
            // Admin Spy List
            if(user === "Admin786") {
                adminList.innerHTML += `<div class="user-row" style="padding:10px; border-bottom:1px solid #222; font-size:11px; display:flex; justify-content:space-between;">
                    <span>${d.name} (${d.ip})</span>
                    <div>
                        <button onclick="startChat('${u.key}')" style="background:var(--s); border:none; border-radius:5px; color:#000; padding:4px 8px;">SPY</button>
                        <button onclick="freezeUser('${u.key}', ${!d.frozen})" style="background:var(--acc); border:none; border-radius:5px; color:#fff; padding:4px 8px;">${d.frozen?'Unfreeze':'Freeze'}</button>
                    </div>
                </div>`;
            }
        });
    });

    if(user === "Admin786") {
        document.getElementById("adminPanel").style.display = "block";
        document.getElementById("admTag").style.display = "inline";
    }
}

// CHAT SYSTEM
window.startChat = (target) => {
    targetUser = target;
    document.getElementById("chatWith").textContent = target;
    openPage('chat');
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

// IMAGE SHARING (CONVERT TO BASE64)
window.sendImage = (input) => {
    const file = input.files[0];
    if(!file || !targetUser) return;
    const reader = new FileReader();
    reader.onload = (e) => {
        const chatID = [user, targetUser].sort().join("_");
        push(ref(db, "messages/" + chatID), { from: user, text: e.target.result, type: 'image' });
    };
    reader.readAsDataURL(file);
};

window.freezeUser = (target, state) => { update(ref(db, "users/"+target), { frozen: state }); };
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
