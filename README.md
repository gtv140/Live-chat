<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#050505; --card:#111111; --text:#ffffff;
  --primary:#6b5bff; --accent:#ff5c8d; --success:#00ff88;
  --glass: rgba(255, 255, 255, 0.03);
}
*{box-sizing: border-box; -webkit-tap-highlight-color: transparent;}
body{margin:0; font-family:'Segoe UI', Roboto, sans-serif; background:var(--bg); color:var(--text); height: 100dvh; display: flex; flex-direction: column; overflow: hidden;}

/* Header - Modern Glow */
header{padding:18px; display:flex; justify-content:space-between; align-items:center; background:var(--card); border-bottom:1px solid #222; z-index: 100;}
header h1{margin:0; font-size:24px; font-weight: 800; background: linear-gradient(90deg, var(--primary), var(--accent)); -webkit-background-clip: text; -webkit-text-fill-color: transparent;}
header .btns i{font-size: 20px; margin-left: 15px; cursor: pointer; color: #666; transition: 0.3s;}
header .btns i:hover{color: var(--primary);}

/* App Container */
.app{max-width:480px; margin:auto; width: 100%; flex:1; display:flex; flex-direction:column; overflow: hidden;}
.page{display:none; padding:15px; flex:1; overflow-y:auto; animation: slideIn 0.3s ease-out;}
.page.active{display:block;}
@keyframes slideIn { from{opacity:0; transform: translateY(10px);} to{opacity:1; transform: translateY(0);} }

/* Dashboard - Extra Modern */
.hero-card{
    background: linear-gradient(135deg, #ff5c8d, #6b5bff);
    padding: 35px 20px; border-radius: 25px; text-align: center;
    box-shadow: 0 10px 30px rgba(107, 91, 255, 0.3); margin-bottom: 25px;
    position: relative; overflow: hidden;
}
.hero-card::after{content:''; position:absolute; top:-50%; left:-50%; width:200%; height:200%; background: radial-gradient(circle, rgba(255,255,255,0.1) 0%, transparent 70%);}

.stat-grid{display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-bottom: 20px;}
.stat-item{background: var(--card); border: 1px solid #222; padding: 20px; border-radius: 20px; text-align: center; transition: 0.3s;}
.stat-item:hover{border-color: var(--primary); transform: translateY(-3px);}
.stat-item h4{margin: 0 0 5px; font-size: 12px; color: var(--muted); text-transform: uppercase;}
.stat-item span{font-size: 24px; font-weight: 800; color: var(--primary);}

/* Admin Features */
.admin-section{margin-top: 25px; border-top: 1px dashed #333; padding-top: 20px;}
.admin-title{color: var(--primary); font-size: 12px; font-weight: bold; margin-bottom: 10px; display: block; letter-spacing: 1px;}
.broadcast-box{background: #1a1a1a; padding: 15px; border-radius: 20px; border: 1px solid #333;}
.broadcast-box input{width: 100%; background: #000; border: 1px solid #222; padding: 12px; border-radius: 12px; color: #fff; outline: none; margin-bottom: 10px;}
.broadcast-btn{width: 100%; padding: 12px; background: var(--primary); border: none; border-radius: 12px; color: white; font-weight: bold; cursor: pointer;}

/* Chat UI */
.chat-box{background:var(--card); border-radius:24px; padding:15px; height:55vh; overflow-y:auto; margin-bottom:12px; border:1px solid #222; display:flex; flex-direction:column; gap:12px;}
.msg{max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; line-height: 1.5;}
.msg.received{background: #222; align-self: flex-start; border-bottom-left-radius: 4px;}
.msg.sent{background: linear-gradient(to right, var(--primary), #8b5cf6); align-self: flex-end; border-bottom-right-radius: 4px;}
.msg img{width:100%; border-radius:12px; margin-top: 8px;}
.msg .actions{position: absolute; top: 4px; right: 8px; font-size: 10px; opacity: 0.4; cursor: pointer;}

/* Input UI */
.input-area{background:var(--card); padding:10px 15px; display:flex; gap:12px; align-items:center; border-radius: 20px; border:1px solid #333; margin-bottom: 10px; flex-shrink: 0;}
.input-area input{flex:1; background:transparent; border:none; color:#fff; outline:none; font-size: 15px;}
.input-area i{font-size: 20px; color: var(--primary); cursor: pointer;}

/* Nav Bar */
nav{display:flex; justify-content:space-around; background:var(--card); padding:18px 0; border-top:1px solid #222; flex-shrink: 0;}
nav i{font-size:22px; color:#444; cursor:pointer; transition: 0.3s;}
nav i.active{color:var(--primary); filter: drop-shadow(0 0 8px var(--primary));}

/* Ban User Card */
.user-card{background: var(--card); padding: 15px; border-radius: 18px; border: 1px solid #222; display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px;}
.ban-btn{background: var(--accent); border: none; padding: 8px 15px; border-radius: 10px; color: white; font-size: 12px; cursor: pointer;}
</style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div class="btns">
            <i class="fa-solid fa-circle-half-stroke" onclick="toggleTheme()"></i>
            <i class="fa-solid fa-power-off" onclick="logout()"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="hero-card">
            <h2 style="margin:0;">Welcome back, <span id="dashUser">...</span>!</h2>
            <p style="opacity: 0.8; font-size: 14px;">Your network is online and secure.</p>
        </div>

        <div class="stat-grid">
            <div class="stat-item"><h4>Online Nodes</h4><span id="onlineCount">0</span></div>
            <div class="stat-item"><h4>Active Groups</h4><span id="groupCount">0</span></div>
        </div>

        <div id="adminPanel" style="display:none;" class="admin-section">
            <span class="admin-title">⚡ ADMIN COMMAND CENTER</span>
            <div class="broadcast-box">
                <input type="text" id="bcMsg" placeholder="Global Announcement...">
                <button class="broadcast-btn" onclick="sendBroadcast()">SEND BROADCAST</button>
            </div>
            
            <span class="admin-title" style="margin-top: 20px;">🛡️ USER MANAGEMENT</span>
            <div id="banList"></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatHeader" style="padding-bottom:10px; border-bottom:1px solid #222; margin-bottom:10px; font-weight:bold; color:var(--primary);">Select a chat</div>
        <div class="chat-box" id="chatBox"></div>
        <div class="input-area">
            <label for="imgInp"><i class="fa-solid fa-paperclip"></i></label>
            <input type="file" id="imgInp" hidden accept="image/*" onchange="uploadPhoto(this)">
            <input id="msgInput" placeholder="Write something...">
            <i class="fa-solid fa-paper-plane" onclick="sendMsg()"></i>
        </div>
    </div>

    <div id="usersPage" class="page">
        <span class="admin-title">ACTIVE CONNECTIONS</span>
        <div id="userList"></div>
        
        <span class="admin-title" style="margin-top:20px;">CHANNELS</span>
        <div id="groupList"></div>
        <div class="input-area" style="margin-top:10px;">
            <input id="groupInput" placeholder="New group name">
            <i class="fa-solid fa-plus" onclick="createGroup()"></i>
        </div>
    </div>

    <nav>
        <i class="fa-solid fa-house active" onclick="openPage('home',this)"></i>
        <i class="fa-solid fa-comment-dots" onclick="openPage('chat',this)"></i>
        <i class="fa-solid fa-users" onclick="openPage('usersPage',this)"></i>
        <i class="fa-solid fa-circle-info" onclick="openPage('home',this)"></i>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("chat_user") || prompt("Enter Username:");
if(!user) location.reload();

localStorage.setItem("chat_user", user);
document.getElementById("dashUser").textContent = user;

// Admin Identification
const isAdmin = (user === "Muhammad Nazim" || user === "Admin786");
if(isAdmin) document.getElementById("adminPanel").style.display = "block";

// Online Status Logic
const myStatusRef = ref(db, "users/" + user);
update(myStatusRef, {online: true, name: user});
onDisconnect(myStatusRef).update({online: false});

window.openPage = (id, el) => {
    document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav i').forEach(i=>i.classList.remove('active'));
    el.classList.add('active');
};

let curChat = "";
let isGroup = false;

// Global Stats & User List
onValue(ref(db, "users"), snap => {
    let online = 0;
    const uList = document.getElementById("userList");
    const bList = document.getElementById("banList");
    uList.innerHTML = ""; bList.innerHTML = "";
    
    snap.forEach(child => {
        const d = child.val();
        if(d.online) online++;
        
        if(child.key !== user){
            // Normal List
            uList.innerHTML += `<div class="user-card" onclick="startChat('${child.key}', false)">
                <span>${child.key}</span> <div class="dot"></div>
            </div>`;
            // Admin Ban List
            if(isAdmin){
                bList.innerHTML += `<div class="user-card">
                    <span>${child.key}</span>
                    <button class="ban-btn" onclick="banUser('${child.key}')">BAN</button>
                </div>`;
            }
        }
    });
    document.getElementById("onlineCount").textContent = online;
});

// Groups
onValue(ref(db, "groups"), snap => {
    const gList = document.getElementById("groupList");
    gList.innerHTML = "";
    document.getElementById("groupCount").textContent = snap.size || 0;
    snap.forEach(g => {
        gList.innerHTML += `<div class="user-card" onclick="startChat('${g.key}', true)">
            <span># ${g.key}</span> <i class="fa-solid fa-chevron-right" style="font-size:12px; opacity:0.3;"></i>
        </div>`;
    });
});

window.startChat = (target, groupFlag) => {
    curChat = target; isGroup = groupFlag;
    document.getElementById("chatHeader").textContent = (isGroup ? "Group: " : "Chat with: ") + target;
    openPage('chat', document.querySelectorAll('nav i')[1]);
    loadMessages();
};

function loadMessages(){
    const path = (isGroup ? "groupChats/" : "chats/") + [user, curChat].sort().join("_");
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const msg = m.val();
            const div = document.createElement("div");
            div.className = `msg ${msg.from === user ? 'sent' : 'received'}`;
            let content = msg.type === 'img' ? `<img src="${msg.text}">` : msg.text;
            div.innerHTML = `
                <b style="font-size:10px; color:rgba(255,255,255,0.5);">${msg.from}</b>
                <div>${content}</div>
                <div class="actions" onclick="deleteMsg('${path}','${m.key}')">🗑️</div>
            `;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value || !curChat) return;
    const path = (isGroup ? "groupChats/" : "chats/") + [user, curChat].sort().join("_");
    push(ref(db, path), {from: user, text: inp.value, type: 'text'});
    inp.value = "";
};

window.uploadPhoto = (e) => {
    const reader = new FileReader();
    reader.onload = (f) => {
        const path = (isGroup ? "groupChats/" : "chats/") + [user, curChat].sort().join("_");
        push(ref(db, path), {from: user, text: f.target.result, type: 'img'});
    };
    reader.readAsDataURL(e.files[0]);
};

// Admin Commands
window.sendBroadcast = () => {
    const msg = document.getElementById("bcMsg").value;
    if(!msg) return;
    set(ref(db, "broadcast"), {msg: msg, from: user, time: Date.now()});
    alert("Broadcast Sent!");
    document.getElementById("bcMsg").value = "";
};

window.banUser = (target) => {
    if(confirm("Ban " + target + "?")) remove(ref(db, "users/" + target));
};

window.deleteMsg = (path, key) => { if(confirm("Delete message?")) remove(ref(db, path + "/" + key)); };
window.logout = () => { update(myStatusRef, {online: false}).then(() => { localStorage.removeItem("chat_user"); location.reload(); }); };
window.createGroup = () => {
    const g = document.getElementById("groupInput").value.trim();
    if(g) set(ref(db, "groups/" + g), {createdBy: user});
    document.getElementById("groupInput").value = "";
};

// Broadcast Listener
onValue(ref(db, "broadcast"), snap => {
    if(snap.exists()){
        const d = snap.val();
        alert("📢 BROADCAST FROM ADMIN:\n" + d.msg);
    }
});
</script>
</body>
</html>
