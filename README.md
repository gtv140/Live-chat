<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | V16 Clean</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#007bff; --s:#ff2d55; --bg:#f4f7f6; --text:#1a1a1a; --bubble-me:#007bff; --bubble-other:#ffffff; }
body.dark-theme { --bg:#0b141a; --text:#e9edef; --bubble-me:#005c4b; --bubble-other:#202c33; }

body { margin:0; font-family:-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto; background:var(--bg); color:var(--text); transition: 0.3s; }
.app { max-width:500px; margin:auto; height:100dvh; display:flex; flex-direction:column; position:relative; }

/* HEADER - Clean Look */
header { padding:50px 20px 15px; background:var(--bubble-other); display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid rgba(0,0,0,0.1); z-index:100; }
header h1 { font-size:20px; margin:0; font-weight:700; color:var(--p); }

/* CHAT MESSAGES - Bubbles Fix */
#chatBox { flex:1; overflow-y:auto; padding:15px; display:flex; flex-direction:column; gap:8px; background-image: url('https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png'); background-size: contain; }
.msg { padding:8px 12px; border-radius:12px; max-width:85%; font-size:15px; position:relative; box-shadow:0 1px 2px rgba(0,0,0,0.1); line-height:1.4; }
.msg.me { align-self:flex-end; background:var(--bubble-me); color:white; border-bottom-right-radius:2px; }
.msg.other { align-self:flex-start; background:var(--bubble-other); color:var(--text); border-bottom-left-radius:2px; }
.msg b { display:block; font-size:11px; margin-bottom:2px; opacity:0.8; }
.msg img { max-width:100%; border-radius:8px; margin-top:5px; }
.msg .time { font-size:10px; float:right; margin-top:4px; opacity:0.7; margin-left:8px; }

/* INPUT SYSTEM - No Overlap */
.input-area { background:var(--bubble-other); padding:10px 15px 30px; display:flex; align-items:center; gap:10px; border-top:1px solid rgba(0,0,0,0.05); }
.input-container { flex:1; background:var(--bg); border-radius:25px; padding:5px 15px; display:flex; align-items:center; }
.input-container input { flex:1; background:none; border:none; color:var(--text); padding:10px; outline:none; font-size:15px; }
.icon-btn { color:gray; font-size:20px; cursor:pointer; padding:5px; }
.send-btn { width:45px; height:45px; border-radius:50%; background:var(--p); color:white; border:none; display:flex; align-items:center; justify-content:center; box-shadow:0 2px 5px rgba(0,0,0,0.2); }

/* ADMIN SYSTEM */
.admin-badge { background:red; color:white; font-size:9px; padding:2px 5px; border-radius:3px; vertical-align:middle; margin-left:5px; }
.page { display:none; flex:1; flex-direction:column; }
.page.active { display:flex; }

/* NAV */
nav { display:flex; justify-content:space-around; background:var(--bubble-other); padding:12px 0; border-top:1px solid rgba(0,0,0,0.05); }
nav button { background:none; border:none; font-size:22px; color:gray; cursor:pointer; }
nav button.active { color:var(--p); }
</style>
</head>
<body class="dark-theme">

<div class="app">
    <header>
        <h1>Live Connect <span id="godTag" class="admin-badge" style="display:none">GOD</span></h1>
        <div style="display:flex; gap:15px; align-items:center;">
            <i class="fa-solid fa-circle-half-stroke" onclick="document.body.classList.toggle('dark-theme')" style="cursor:pointer"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--s); cursor:pointer"></i>
        </div>
    </header>

    <div id="chat" class="page active">
        <div style="padding:8px 15px; background:rgba(0,0,0,0.03); font-size:12px; font-weight:bold; color:var(--p);" id="targetName">Global Group</div>
        <div id="chatBox"></div>
        <div class="input-area">
            <div class="input-container">
                <label for="fileIn" class="icon-btn"><i class="fa-solid fa-paperclip"></i></label>
                <input type="file" id="fileIn" hidden accept="image/*" onchange="sendImg(this)">
                <input id="msgInput" placeholder="Message...">
                <i class="fa-solid fa-microphone icon-btn"></i>
            </div>
            <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="nodes" class="page" style="padding:15px;">
        <h4 style="opacity:0.5; font-size:12px; margin-bottom:15px;">ONLINE USERS</h4>
        <div id="userList"></div>
    </div>

    <div id="admin" class="page" style="padding:15px;">
        <h3 style="color:red; font-size:16px;">MASTER CONTROL</h3>
        <div id="adminList"></div>
        <button onclick="clearAll()" style="width:100%; padding:15px; background:red; color:white; border:none; border-radius:10px; margin-top:20px; font-weight:bold;">ERASE ALL DATA</button>
    </div>

    <nav>
        <button onclick="openPage('chat',this)" class="active"><i class="fa-solid fa-comment-dots"></i></button>
        <button onclick="openPage('nodes',this)"><i class="fa-solid fa-users"></i></button>
        <button onclick="openPage('admin',this)" id="admBtn" style="display:none"><i class="fa-solid fa-shield-cat"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user") || prompt("Apna Naam Likhen:");
if(user) localStorage.setItem("lc_user", user); else location.reload();

const myRef = ref(db, "users/"+user);
update(myRef, { name:user, online:true, frozen:false });
onDisconnect(myRef).update({ online:false });

if(user === "Admin786") {
    document.getElementById("godTag").style.display = "inline";
    document.getElementById("admBtn").style.display = "block";
}

// LOAD USERS & ADMIN
onValue(ref(db, "users"), snap => {
    const list = document.getElementById("userList");
    const adm = document.getElementById("adminList");
    list.innerHTML = ""; adm.innerHTML = "";
    snap.forEach(u => {
        const d = u.val();
        if(u.key !== user) {
            list.innerHTML += `<div style="background:var(--bubble-other); padding:15px; border-radius:10px; margin-bottom:8px; display:flex; justify-content:space-between;" onclick="startChat('${u.key}')">
                <span><span style="width:8px; height:8px; background:${d.online?'#22c55e':'#555'}; border-radius:50%; display:inline-block; margin-right:10px;"></span>${u.key}</span>
                <i class="fa-solid fa-chevron-right" style="opacity:0.2"></i>
            </div>`;
        }
        if(user === "Admin786" && u.key !== user) {
            adm.innerHTML += `<div style="display:flex; justify-content:space-between; padding:10px; border-bottom:1px solid rgba(0,0,0,0.1);">
                <span>${u.key}</span>
                <button onclick="freezeUser('${u.key}', ${!d.frozen})" style="background:${d.frozen?'green':'red'}; color:white; border:none; border-radius:5px; padding:5px;">${d.frozen?'Unfreeze':'Freeze'}</button>
            </div>`;
        }
    });
});

let chatPath = "global";
window.startChat = (t) => {
    chatPath = "pvt/"+[user, t].sort().join("_");
    document.getElementById("targetName").textContent = "Chatting with: " + t;
    openPage('chat', document.querySelector('nav button:first-child'));
    loadMsgs();
};

function loadMsgs() {
    onValue(ref(db, chatPath), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const isMe = d.from === user;
            const content = d.type === 'img' ? `<img src="${d.text}">` : d.text;
            const delBtn = (user === "Admin786") ? `<i class="fa-solid fa-trash" onclick="delMsg('${chatPath}','${m.key}')" style="font-size:10px; margin-left:10px; color:red;"></i>` : "";
            
            box.innerHTML += `<div class="msg ${isMe?'me':'other'}">
                <b>${d.from}</b> ${content} ${delBtn}
                <span class="time">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</span>
            </div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
}
loadMsgs();

window.sendMsg = () => {
    const inp = document.getElementById("msgInput"); if(!inp.value) return;
    push(ref(db, chatPath), { from:user, text:inp.value, type:'text', time:Date.now() });
    inp.value = "";
};

window.sendImg = (input) => {
    const reader = new FileReader();
    reader.onload = (e) => push(ref(db, chatPath), { from:user, text:e.target.result, type:'img', time:Date.now() });
    reader.readAsDataURL(input.files[0]);
};

window.freezeUser = (t, s) => update(ref(db, "users/"+t), { frozen:s });
window.delMsg = (p, k) => remove(ref(db, p+"/"+k));
window.clearAll = () => { if(confirm("Clear All?")) remove(ref(db)); location.reload(); };

window.logout = () => { localStorage.clear(); location.reload(); };
window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    if(btn) btn.classList.add('active');
};
</script>
</body>
</html>
