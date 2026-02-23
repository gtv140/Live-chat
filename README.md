<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | Mobile Ghost</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#c026d3; --s:#0ea5e9; --bg:#05050a; --acc:#f43f5e; --glass:rgba(15,15,25,0.85); --border:rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Inter', sans-serif; -webkit-tap-highlight-color: transparent; }
body { margin:0; background:var(--bg); color:#f8fafc; overflow:hidden; position: fixed; width: 100%; height: 100%; }

/* MOBILE RESPONSIVE FIXES */
.app { width:100%; height:100dvh; display:flex; flex-direction:column; position:relative; overflow: hidden; }

/* FREEZE OVERLAY */
#freezeOverlay { display:none; position:fixed; inset:0; background:#000; z-index:99999; flex-direction:column; align-items:center; justify-content:center; color:#f43f5e; text-align:center; padding: 20px; }

/* HACK OVERLAY */
#hackOverlay { display:none; position:fixed; inset:0; background:#000; z-index:88888; color:#22c55e; padding:20px; font-family: monospace; font-size:12px; overflow: hidden; }

/* MODERN LOGIN - MOBILE SCALE */
#loginPage { position:fixed; inset:0; background:#000; z-index:10000; display:flex; flex-direction:column; align-items:center; justify-content:center; padding:30px; }
#loginPage img { width:130px; height:130px; border-radius:50%; border:2px solid var(--s); box-shadow: 0 0 40px var(--p); margin-bottom:20px; }
#loginPage h1 { font-size: 28px; font-weight: 900; margin: 10px 0; background: linear-gradient(to right, #fff, var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
#loginPage input { width:100%; max-width:280px; padding:15px; border-radius:15px; background:var(--glass); border:1px solid var(--border); color:#fff; text-align:center; margin-bottom:15px; outline:none; font-size: 16px; }
#loginPage button { width:100%; max-width:280px; padding:15px; border:none; border-radius:15px; background: linear-gradient(135deg, var(--p), var(--s)); color:#fff; font-weight:900; cursor:pointer; }

/* HEADER - COMPACT */
header { padding: 45px 15px 15px; background:rgba(0,0,0,0.5); backdrop-filter: blur(15px); border-bottom: 1px solid var(--border); display:flex; justify-content:space-between; align-items:center; }
header h2 { margin:0; font-size:18px; font-weight: 800; }

.page { display:none; padding:15px; flex:1; overflow-y:auto; padding-bottom:100px; -webkit-overflow-scrolling: touch; }
.page.active { display:block; animation: slideUp 0.3s ease; }
@keyframes slideUp { from { opacity:0; transform:translateY(15px); } to { opacity:1; transform:translateY(0); } }

/* CARDS - MOBILE FRIENDLY */
.card { background:var(--glass); padding:15px; border-radius:20px; border:1px solid var(--border); margin-bottom:12px; }

/* CHAT BOX - RESPONSIVE HEIGHT */
#chatHeader { padding:12px; background:var(--glass); border-radius:15px; margin-bottom:12px; display:flex; align-items:center; gap:12px; border:1px solid var(--border); }
#chatBox { height:calc(100dvh - 280px); overflow-y:auto; display:flex; flex-direction:column; gap:10px; padding-bottom: 20px; }
.msg { padding:12px 14px; border-radius:18px; font-size:13.5px; max-width:85%; line-height: 1.4; word-wrap: break-word; }
.msg.me { background: var(--p); align-self:flex-end; border-bottom-right-radius:2px; }
.msg.them { background:#1e1e2e; align-self:flex-start; border-bottom-left-radius:2px; border:1px solid var(--border); }
.msg img { max-width: 100%; border-radius: 12px; margin-top: 8px; }
.msg b { display:block; font-size:9px; margin-bottom:3px; color:var(--s); }

/* INPUT AREA - FIXED AT BOTTOM */
.input-area { display:flex; gap:8px; background:var(--glass); padding:8px; border-radius:25px; border:1px solid var(--border); align-items: center; margin-bottom: 5px; }
.input-area input { flex:1; background:transparent; border:none; color:#fff; padding:8px; outline:none; font-size: 14px; }
.input-area label { color: var(--s); font-size: 18px; padding-left: 10px; }
.input-area button { width:42px; height:42px; border-radius:50%; border:none; background:var(--s); color:#fff; }

/* NAV - BOTTOM STICKY */
nav { position:fixed; bottom:15px; left:50%; transform:translateX(-50%); width:92%; background:rgba(10,10,20,0.9); padding:12px 0; border-radius:25px; border:1px solid var(--border); display:flex; justify-content:space-around; backdrop-filter: blur(20px); z-index: 2000; }
nav button { background:none; border:none; font-size:20px; color:#475569; }
nav button.active { color:var(--s); transform: translateY(-3px); }

.entity { background:var(--glass); padding:12px 15px; border-radius:15px; border:1px solid var(--border); margin-bottom:10px; display:flex; justify-content:space-between; align-items:center; font-size: 14px; }
.admin-card { border: 1px solid var(--acc) !important; background: rgba(244,63,94,0.05) !important; }
</style>
</head>
<body>

<div id="freezeOverlay"><i class="fa-solid fa-skull-crossbones" style="font-size:60px; color:#f43f5e"></i><h2>SYSTEM LOCKED</h2><p>Access Revoked by Admin786</p></div>
<div id="hackOverlay"></div>

<div class="app">
    <div id="loginPage">
        <img src="https://i.pinimg.com/736x/8e/4a/1b/8e4a1b802a8069d273e04098675402a7.jpg">
        <h1>Live Connect</h1>
        <p style="color:#64748b; font-size:10px; letter-spacing:3px;">MOBILE OVERRIDE V8.5</p>
        <input type="text" id="usernameInput" placeholder="Secure ID" autocomplete="off">
        <button onclick="initiateHack()">CONNECT NODE</button>
    </div>

    <header>
        <h2>LIVE CONNECT <span id="admTag" style="display:none; color:var(--acc); font-size:9px;">• GOD</span></h2>
        <button onclick="logout()" style="background:none; border:none; color:var(--acc); font-size:18px;"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="home" class="page active">
        <div class="card" style="background: linear-gradient(135deg, rgba(192,38,211,0.1), rgba(14,165,233,0.1)); text-align: center;">
            <p style="margin:0; font-weight:700; color:var(--s);"><span id="dashUser">...</span></p>
            <p style="font-size:10px; opacity:0.6; margin-top:5px;">Ghost Node: Online</p>
        </div>
        <div class="card" style="display:flex; justify-content:space-between; font-size:14px;">
            <span>Live Nodes:</span><span id="onlineCount" style="color:var(--s); font-weight:800;">0</span>
        </div>
        <div id="adminPanel" style="display:none;">
            <p style="color:var(--acc); font-size:12px; font-weight:800; margin-left:5px;">ADMIN CONSOLE</p>
            <div id="adminUserList"></div>
            <button onclick="wipeAll()" style="width:100%; padding:15px; background:var(--acc); border:none; border-radius:15px; color:white; font-weight:900; margin-top:10px;">NUCLEAR WIPE</button>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatHeader">
            <button onclick="openPage('usersPage')" style="background:none; border:none; color:var(--s);"><i class="fa-solid fa-arrow-left"></i></button>
            <span id="chatWith" style="font-size:14px; font-weight:800; color:#fff;">SELECT NODE</span>
        </div>
        <div id="chatBox"></div>
        <div class="input-area">
            <label for="imgInput"><i class="fa-solid fa-camera"></i></label>
            <input type="file" id="imgInput" style="display:none" accept="image/*" onchange="sendImage(this)">
            <input id="msgInput" placeholder="Message..."/>
            <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="usersPage" class="page">
        <p style="color:#64748b; font-size:11px; font-weight:800; margin-bottom:15px;">ONLINE ENTITIES</p>
        <div id="publicUserList"></div>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-home"></i></button>
        <button onclick="openPage('usersPage',this)"><i class="fa-solid fa-ghost"></i></button>
        <button onclick="openPage('chat',this)" id="chatNav"><i class="fa-solid fa-comment-dots"></i></button>
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
    const logs = ["TRACING IP...", "BYPASSING FIREWALL...", "ACCESS GRANTED."];
    let i = 0;
    const itv = setInterval(() => {
        overlay.innerHTML += `<p>> ${logs[i]}</p>`; i++;
        if(i >= logs.length) { clearInterval(itv); setTimeout(() => { localStorage.setItem("lc_user", val); location.reload(); }, 600); }
    }, 400);
};

if(user) {
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("dashUser").textContent = user;
    fetch('https://ipapi.co/json/').then(r=>r.json()).then(data=>{
        update(ref(db, "users/"+user), { name:user, online:true, ip:data.ip, city:data.city, frozen:false });
    });
    onDisconnect(ref(db, "users/"+user)).update({ online:false });

    onValue(ref(db, "users/"+user+"/frozen"), snap => {
        document.getElementById("freezeOverlay").style.display = snap.val() ? "flex" : "none";
    });

    if(user === "Admin786") {
        document.getElementById("adminPanel").style.display = "block";
        document.getElementById("admTag").style.display = "inline";
    }

    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("publicUserList"); 
        const adminList = document.getElementById("adminUserList");
        let count = 0; list.innerHTML = ""; adminList.innerHTML = "";
        snap.forEach(u => {
            const d = u.val();
            if(d.online) count++;
            if(u.key === user) return;
            list.innerHTML += `<div class="entity" onclick="startChat('${u.key}')"><span>${d.name}</span><i class="fa-solid fa-chevron-right" style="opacity:0.3"></i></div>`;
            if(user === "Admin786") {
                adminList.innerHTML += `<div class="card admin-card" style="padding:10px; font-size:12px;">
                    <div style="display:flex; justify-content:space-between; align-items:center;">
                        <span>${d.name} (${d.ip})</span>
                        <div>
                            <button onclick="startChat('${u.key}')" style="background:var(--s); border:none; padding:5px 10px; border-radius:8px; color:#000;"><i class="fa-solid fa-eye"></i></button>
                            <button onclick="freezeUser('${u.key}', ${!d.frozen})" style="background:var(--acc); border:none; padding:5px 10px; border-radius:8px; color:#fff;"><i class="fa-solid fa-lock"></i></button>
                        </div>
                    </div>
                </div>`;
            }
        });
        document.getElementById("onlineCount").textContent = count;
    });
}

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
    const file = input.files[0];
    const reader = new FileReader();
    reader.onload = (e) => {
        const chatID = [user, targetUser].sort().join("_");
        push(ref(db, "messages/" + chatID), { from: user, text: e.target.result, type: 'image' });
    };
    reader.readAsDataURL(file);
};

window.freezeUser = (target, state) => { update(ref(db, "users/"+target), { frozen: state }); };
window.wipeAll = () => { if(confirm("NUCLEAR WIPE ALL?")) { remove(ref(db, "messages")); remove(ref(db, "users")); localStorage.clear(); location.reload(); } };
window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    if(btn) { document.querySelectorAll('nav button').forEach(b => b.classList.remove('active')); btn.classList.add('active'); }
};
window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
