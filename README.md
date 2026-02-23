<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | God Mode</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#2563eb; --s:#dc2626; --bg:#0f172a; --card:#1e293b; --text:#f8fafc; --me:#1e40af; --other:#334155; }
body { margin:0; font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background:var(--bg); color:var(--text); overflow:hidden; height:100dvh; }
.app { max-width:480px; margin:auto; height:100dvh; display:flex; flex-direction:column; background:var(--bg); border:1px solid rgba(255,255,255,0.1); }

/* HEADER */
header { padding:45px 15px 15px; background:var(--card); display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid rgba(255,255,255,0.05); }
header h1 { font-size:18px; margin:0; font-weight:800; color:var(--p); text-transform:uppercase; letter-spacing:1px; }

/* CHAT AREA */
#chatBox { flex:1; overflow-y:auto; padding:15px; display:flex; flex-direction:column; gap:10px; background: radial-gradient(circle at top, #1e293b 0%, #0f172a 100%); }
.msg { padding:10px 14px; border-radius:15px; max-width:80%; font-size:14px; position:relative; box-shadow:0 4px 6px -1px rgba(0,0,0,0.1); line-height:1.5; }
.msg.me { align-self:flex-end; background:var(--me); color:white; border-bottom-right-radius:2px; }
.msg.other { align-self:flex-start; background:var(--other); color:white; border-bottom-left-radius:2px; }
.msg b { display:block; font-size:10px; margin-bottom:4px; color:#94a3b8; text-transform:uppercase; }
.msg img { max-width:100%; border-radius:10px; margin-top:5px; border:1px solid rgba(255,255,255,0.1); }
.msg .time { font-size:9px; float:right; margin-top:5px; opacity:0.5; margin-left:10px; }

/* INPUT BOX - FIX */
.input-panel { background:var(--card); padding:10px 15px 30px; border-top:1px solid rgba(255,255,255,0.05); display:flex; gap:10px; align-items:center; }
.input-row { flex:1; background:rgba(255,255,255,0.03); border-radius:25px; padding:5px 15px; display:flex; align-items:center; border:1px solid rgba(255,255,255,0.1); }
.input-row input { flex:1; background:none; border:none; color:white; padding:10px; outline:none; font-size:14px; }
.cam-btn { color:#94a3b8; font-size:20px; cursor:pointer; margin-right:5px; transition:0.2s; }
.cam-btn:hover { color:var(--p); }
.send-btn { width:45px; height:45px; border-radius:50%; background:var(--p); color:white; border:none; cursor:pointer; transition:0.2s; box-shadow:0 0 15px rgba(37,99,235,0.3); }

/* ADMIN UI */
.adm-badge { background:var(--s); color:white; font-size:9px; padding:2px 6px; border-radius:4px; font-weight:900; margin-left:5px; }
.user-item { background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px; display:flex; justify-content:space-between; align-items:center; border:1px solid rgba(255,255,255,0.05); }

/* NAV */
nav { display:flex; justify-content:space-around; background:var(--card); padding:12px 0; border-top:1px solid rgba(255,255,255,0.05); }
nav button { background:none; border:none; font-size:20px; color:#475569; cursor:pointer; transition:0.3s; }
nav button.active { color:var(--p); transform:translateY(-2px); }

.page { display:none; flex:1; flex-direction:column; overflow:hidden; }
.page.active { display:flex; }
</style>
</head>
<body>

<div id="freezeOverlay" style="display:none; position:fixed; inset:0; background:#000; z-index:10000; flex-direction:column; align-items:center; justify-content:center; color:white; text-align:center;">
    <i class="fa-solid fa-lock" style="font-size:60px; color:var(--s); margin-bottom:20px;"></i>
    <h2 style="margin:0;">ACCESS DENIED</h2>
    <p style="opacity:0.6;">Your node has been frozen by Admin786</p>
</div>

<div class="app">
    <header>
        <h1>LIVE CONNECT <span id="godBadge" class="adm-badge" style="display:none">GOD MODE</span></h1>
        <div style="display:flex; gap:15px;">
            <i class="fa-solid fa-ghost" id="admIcon" style="display:none; color:var(--s); cursor:pointer;" onclick="openPage('adminTab', this)"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--s); cursor:pointer;"></i>
        </div>
    </header>

    <div id="chat" class="page active">
        <div style="padding:10px 15px; background:rgba(37,99,235,0.1); font-size:12px; font-weight:bold; color:var(--p); border-bottom:1px solid rgba(37,99,235,0.2);" id="chatTitle">Global Network</div>
        <div id="chatBox"></div>
        <div class="input-panel">
            <div class="input-row">
                <label for="imgUp" class="cam-btn"><i class="fa-solid fa-image"></i></label>
                <input type="file" id="imgUp" hidden accept="image/*" onchange="sendPhoto(this)">
                <input id="msgInp" placeholder="Type a message...">
            </div>
            <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="users" class="page" style="padding:15px; overflow-y:auto;">
        <h4 style="opacity:0.4; font-size:11px; margin-bottom:15px; letter-spacing:1px;">ACTIVE NODES</h4>
        <div id="uListPublic"></div>
    </div>

    <div id="adminTab" class="page" style="padding:15px; overflow-y:auto;">
        <h3 style="color:var(--s); font-size:16px; margin-bottom:20px;"><i class="fa-solid fa-terminal"></i> MASTER CONSOLE</h3>
        <div id="uListAdmin"></div>
        <button onclick="nuclearWipe()" style="width:100%; padding:15px; background:var(--s); color:white; border:none; border-radius:12px; font-weight:bold; margin-top:20px; box-shadow:0 0 20px rgba(220,38,38,0.3);">NUCLEAR WIPE</button>
    </div>

    <nav>
        <button onclick="openPage('chat',this)" class="active"><i class="fa-solid fa-comment-dots"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-users"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user") || prompt("Identity:");
if(user) localStorage.setItem("lc_user", user); else location.reload();

const myRef = ref(db, "users/"+user);
update(myRef, { online:true, frozen:false });
onDisconnect(myRef).update({ online:false });

if(user === "Admin786") {
    document.getElementById("godBadge").style.display = "inline";
    document.getElementById("admIcon").style.display = "block";
}

onValue(ref(db, "users/"+user+"/frozen"), snap => {
    if(snap.val()) document.getElementById("freezeOverlay").style.display = "flex";
});

// LOAD NODES
onValue(ref(db, "users"), snap => {
    const pub = document.getElementById("uListPublic");
    const adm = document.getElementById("uListAdmin");
    pub.innerHTML = ""; adm.innerHTML = "";
    snap.forEach(u => {
        const d = u.val();
        if(u.key !== user) {
            pub.innerHTML += `<div class="user-item" onclick="startChat('${u.key}')">
                <span><span style="width:8px; height:8px; background:${d.online?'#22c55e':'#475569'}; border-radius:50%; display:inline-block; margin-right:10px;"></span>${u.key}</span>
                <i class="fa-solid fa-chevron-right" style="opacity:0.2"></i>
            </div>`;
        }
        if(user === "Admin786" && u.key !== user) {
            adm.innerHTML += `<div class="user-item">
                <div style="font-size:12px;"><b>${u.key}</b><br><small style="color:#64748b">Status: ${d.online?'Live':'Offline'}</small></div>
                <button onclick="toggleFreeze('${u.key}', ${!d.frozen})" style="background:${d.frozen?'#22c55e':var(--s)}; color:white; border:none; padding:6px 12px; border-radius:6px; font-size:10px; font-weight:bold;">${d.frozen?'UNFREEZE':'FREEZE'}</button>
            </div>`;
        }
    });
});

let chatTarget = "global";
window.startChat = (t) => {
    chatTarget = "pvt/"+[user, t].sort().join("_");
    document.getElementById("chatTitle").textContent = "Secured Connection: " + t;
    openPage('chat', document.querySelector('nav button:first-child'));
    loadMsgs();
};

function loadMsgs() {
    onValue(ref(db, chatTarget), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const isMe = d.from === user;
            const body = d.type === 'img' ? `<img src="${d.text}">` : d.text;
            const del = (user === "Admin786") ? `<i class="fa-solid fa-trash" onclick="delMsg('${chatTarget}','${m.key}')" style="font-size:10px; margin-left:10px; opacity:0.3; cursor:pointer;"></i>` : "";
            
            box.innerHTML += `<div class="msg ${isMe?'me':'other'}">
                <b>${d.from}</b> ${body} ${del}
                <div class="time">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</div>
            </div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
}
loadMsgs();

window.sendMsg = () => {
    const inp = document.getElementById("msgInp"); if(!inp.value) return;
    push(ref(db, chatTarget), { from:user, text:inp.value, type:'text', time:Date.now() });
    inp.value = "";
};

window.sendPhoto = (input) => {
    const reader = new FileReader();
    reader.onload = (e) => push(ref(db, chatTarget), { from:user, text:e.target.result, type:'img', time:Date.now() });
    reader.readAsDataURL(input.files[0]);
};

// ADMIN ACTIONS
window.toggleFreeze = (t, s) => update(ref(db, "users/"+t), { frozen:s });
window.delMsg = (p, k) => remove(ref(db, p+"/"+k));
window.nuclearWipe = () => { if(confirm("ERASE ALL SYSTEM DATA?")) remove(ref(db)); location.reload(); };

window.logout = () => { localStorage.clear(); location.reload(); };
window.openPage = (p, b) => {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
};
</script>
</body>
</html>
