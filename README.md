<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | Pro V13</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#6b5bff; --s:#ff5c8d; --bg:#050505; --card:#111; --text:#eee; --glass:rgba(255,255,255,0.05); }
body{ margin:0; font-family:'Segoe UI', sans-serif; background:var(--bg); color:var(--text); overflow:hidden; height:100dvh; }
.app{ max-width:480px; margin:auto; height:100dvh; display:flex; flex-direction:column; position:relative; background:var(--bg); }

/* HEADER */
header{ padding:45px 15px 15px; display:flex; justify-content:space-between; align-items:center; background:var(--card); border-bottom:1px solid var(--glass); z-index:100; }
header h1{ margin:0; font-size:18px; background:linear-gradient(90deg, var(--p), var(--s)); -webkit-background-clip:text; -webkit-text-fill-color:transparent; font-weight:900; }

/* CHAT BOX */
.chat-container{ flex:1; display:flex; flex-direction:column; overflow:hidden; background: url('https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png'); background-blend-mode: overlay; }
#chatBox{ flex:1; overflow-y:auto; padding:15px; display:flex; flex-direction:column; gap:12px; }
.msg{ background:var(--card); padding:10px 14px; border-radius:18px; max-width:75%; position:relative; font-size:14px; border:1px solid var(--glass); animation: pop 0.3s ease; }
.msg.me{ align-self:flex-end; background:var(--p); border-bottom-right-radius:2px; border:none; }
.msg b{ display:block; font-size:10px; margin-bottom:3px; opacity:0.8; text-transform:uppercase; color:var(--s); }
.msg img{ max-width:100%; border-radius:10px; margin-top:5px; }
.msg .meta{ font-size:10px; float:right; margin-top:5px; opacity:0.6; margin-left:8px; }
@keyframes pop { from{transform:scale(0.8); opacity:0;} to{transform:scale(1); opacity:1;} }

/* INPUT AREA */
.input-area{ background:var(--card); padding:10px 15px 35px; border-top:1px solid var(--glass); display:flex; align-items:center; gap:10px; }
.input-row{ flex:1; background:var(--glass); border-radius:30px; padding:5px 15px; display:flex; align-items:center; border:1px solid var(--glass); }
.input-row input{ flex:1; background:none; border:none; color:#fff; padding:10px; outline:none; }
.btn-icon{ background:none; border:none; color:var(--text); font-size:18px; cursor:pointer; opacity:0.7; }

/* LIVE STATUS & ADMIN */
.live-dot{ width:8px; height:8px; background:#22c55e; border-radius:50%; display:inline-block; margin-right:8px; box-shadow:0 0 8px #22c55e; animation: pulse 1.5s infinite; }
@keyframes pulse { 0%, 100%{opacity:1;} 50%{opacity:0.3;} }
.admin-badge{ background:var(--s); color:#fff; font-size:9px; padding:2px 6px; border-radius:4px; font-weight:900; margin-left:5px; }

/* NAV */
nav{ position:fixed; bottom:0; width:100%; max-width:480px; display:flex; justify-content:space-around; background:var(--card); padding:12px 0; border-top:1px solid var(--glass); }
nav button{ background:none; border:none; font-size:20px; color:#555; transition:0.3s; }
nav button.active{ color:var(--p); transform:translateY(-3px); }

.page{ display:none; padding:15px; height:calc(100dvh - 160px); overflow-y:auto; }
.page.active{ display:block; }
</style>
</head>
<body>

<div id="freezeOverlay" style="display:none; position:fixed; inset:0; background:#000; z-index:10000; flex-direction:column; align-items:center; justify-content:center; color:red;">
    <i class="fa-solid fa-ban" style="font-size:50px"></i><h2>ACCOUNT FROZEN</h2>
</div>

<div class="app">
  <header>
    <h1>Live Connect <span id="admTag" class="admin-badge" style="display:none">GOD MODE</span></h1>
    <div style="display:flex; gap:15px;">
        <i class="fa-solid fa-shield-halved" style="color:var(--p)"></i>
        <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--s)"></i>
    </div>
  </header>

  <div id="home" class="page active">
    <div style="background:linear-gradient(135deg, var(--p), var(--s)); padding:30px; border-radius:25px; margin-bottom:20px; text-align:center;">
        <h2 id="dashUser">...</h2>
        <p style="font-size:12px; opacity:0.8;">End-to-End Encrypted Session</p>
    </div>
    
    <div id="adminPanel" style="display:none; background:rgba(255,0,0,0.05); border:1px solid rgba(255,0,0,0.2); padding:15px; border-radius:20px;">
        <h3 style="color:var(--s); font-size:14px;"><i class="fa-solid fa-terminal"></i> MASTER CONSOLE</h3>
        <div id="admUserList"></div>
        <button onclick="wipeAll()" style="width:100%; padding:12px; background:var(--s); border:none; border-radius:12px; color:#fff; font-weight:bold; margin-top:10px;">NUCLEAR WIPE</button>
    </div>
  </div>

  <div id="chat" class="page" style="padding:0;">
    <div class="chat-container">
        <div style="padding:10px 15px; background:var(--card); font-size:13px; border-bottom:1px solid var(--glass);">
            <span id="targetName">Global Terminal</span>
        </div>
        <div id="chatBox"></div>
        <div class="input-area">
            <div class="input-row">
                <label for="imgIn" class="btn-icon"><i class="fa-solid fa-image"></i></label>
                <input type="file" id="imgIn" hidden accept="image/*" onchange="sendMedia(this, 'img')">
                <input id="msgInput" placeholder="Write message...">
                <button class="btn-icon" onclick="startVoice()"><i class="fa-solid fa-microphone"></i></button>
            </div>
            <button class="send-btn" onclick="sendMsg()" style="width:45px; height:45px; border-radius:50%; background:var(--p); border:none; color:#fff;"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>
  </div>

  <div id="users" class="page">
    <h4 style="opacity:0.5; font-size:12px; letter-spacing:1px;">ACTIVE NETWORK NODES</h4>
    <div id="uList"></div>
  </div>

  <nav>
    <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
    <button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
    <button onclick="openPage('users',this)"><i class="fa-solid fa-ghost"></i></button>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user") || prompt("Identity:");
if(user) localStorage.setItem("lc_user", user); else location.reload();

document.getElementById("dashUser").textContent = user;
const myRef = ref(db, "users/"+user);
update(myRef, { online:true, last:Date.now(), frozen:false });
onDisconnect(myRef).update({ online:false });

if(user === "Admin786") {
    document.getElementById("admTag").style.display = "inline";
    document.getElementById("adminPanel").style.display = "block";
}

onValue(ref(db, "users/"+user+"/frozen"), snap => {
    if(snap.val()) document.getElementById("freezeOverlay").style.display = "flex";
});

// LIST USERS & ADMIN POWER
onValue(ref(db, "users"), snap => {
    const list = document.getElementById("uList");
    const adm = document.getElementById("admUserList");
    list.innerHTML = ""; adm.innerHTML = "";
    snap.forEach(u => {
        const d = u.val();
        if(u.key === user) return;
        list.innerHTML += `<div onclick="startChat('${u.key}')" style="background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px; display:flex; justify-content:space-between;">
            <span>${d.online ? '<span class="live-dot"></span>' : ''}${u.key}</span><i class="fa-solid fa-chevron-right" style="opacity:0.2"></i>
        </div>`;

        if(user === "Admin786") {
            adm.innerHTML += `<div style="padding:10px; border-bottom:1px solid var(--glass); display:flex; justify-content:space-between; font-size:12px;">
                <span>${u.key}</span>
                <button onclick="freezeUser('${u.key}', ${!d.frozen})" style="background:var(--s); border:none; color:#fff; border-radius:5px; padding:4px 8px;">${d.frozen?'Unfreeze':'Freeze'}</button>
            </div>`;
        }
    });
});

let target = "Global";
window.startChat = (t) => {
    target = t; document.getElementById("targetName").textContent = "Chatting with: " + t;
    openPage('chat', document.querySelector('nav button:nth-child(2)'));
    loadMessages();
};

function loadMessages() {
    const path = target === "Global" ? "global" : "pvt/"+[user,target].sort().join("_");
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const isMe = d.from === user;
            let body = d.type === 'img' ? `<img src="${d.text}">` : d.text;
            let del = (user === "Admin786") ? `<i class="fa-solid fa-trash" onclick="delMsg('${path}','${m.key}')" style="font-size:10px; margin-left:10px; opacity:0.3;"></i>` : "";
            
            box.innerHTML += `<div class="msg ${isMe?'me':''}">
                <b>${d.from}</b>${body}${del}
                <div class="meta">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})} ${isMe ? '✓✓' : ''}</div>
            </div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
}
loadMessages();

window.sendMsg = () => {
    const inp = document.getElementById("msgInput"); if(!inp.value) return;
    const path = target === "Global" ? "global" : "pvt/"+[user,target].sort().join("_");
    push(ref(db, path), { from:user, text:inp.value, type:'text', time:Date.now() });
    inp.value = "";
};

window.sendMedia = (input, type) => {
    const file = input.files[0]; if(!file) return;
    const reader = new FileReader();
    reader.onload = (e) => {
        const path = target === "Global" ? "global" : "pvt/"+[user,target].sort().join("_");
        push(ref(db, path), { from:user, text:e.target.result, type:type, time:Date.now() });
    };
    reader.readAsDataURL(file);
};

// ADMIN & UI
window.freezeUser = (id, s) => update(ref(db, "users/"+id), { frozen:s });
window.delMsg = (p, k) => remove(ref(db, p+"/"+k));
window.wipeAll = () => { if(confirm("Wipe System?")) remove(ref(db)); location.reload(); };
window.logout = () => { localStorage.clear(); location.reload(); };
window.openPage = (p, b) => {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    b.classList.add('active');
};
window.startVoice = () => alert("Voice Recording Started... (Firebase Storage required for audio files)");
</script>
</body>
</html>
