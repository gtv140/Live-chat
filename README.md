<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | V18 Elite</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#3b82f6; --s:#ef4444; --bg:#020617; --card:#0f172a; --text:#f8fafc; --me:#2563eb; --other:#1e293b; }
body { margin:0; font-family:'Inter', sans-serif; background:var(--bg); color:var(--text); overflow:hidden; height:100dvh; }
.app { max-width:480px; margin:auto; height:100dvh; display:flex; flex-direction:column; background:var(--bg); border:1px solid rgba(255,255,255,0.05); }

/* HEADER */
header { padding:45px 15px 15px; background:var(--card); display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid rgba(255,255,255,0.1); }
header h1 { font-size:16px; margin:0; font-weight:900; color:var(--p); letter-spacing:2px; text-transform:uppercase; }

/* CHAT BOX */
#chatBox { flex:1; overflow-y:auto; padding:15px; display:flex; flex-direction:column; gap:12px; background: radial-gradient(circle at center, #0f172a 0%, #020617 100%); }
.msg { padding:12px 16px; border-radius:18px; max-width:80%; font-size:14px; position:relative; line-height:1.5; box-shadow: 0 4px 15px rgba(0,0,0,0.2); }
.msg.me { align-self:flex-end; background:var(--me); color:white; border-bottom-right-radius:2px; }
.msg.other { align-self:flex-start; background:var(--other); color:white; border-bottom-left-radius:2px; }
.msg b { display:block; font-size:10px; margin-bottom:5px; color:var(--p); text-transform:uppercase; opacity:0.8; }
.msg img { max-width:100%; border-radius:12px; margin-top:8px; border:1px solid rgba(255,255,255,0.1); }
.msg .time { font-size:9px; float:right; margin-top:6px; opacity:0.4; margin-left:10px; }

/* INPUT PANEL */
.input-panel { background:var(--card); padding:10px 15px 35px; border-top:1px solid rgba(255,255,255,0.1); display:flex; gap:12px; align-items:center; }
.input-row { flex:1; background:rgba(255,255,255,0.03); border-radius:30px; padding:5px 18px; display:flex; align-items:center; border:1px solid rgba(255,255,255,0.1); }
.input-row input { flex:1; background:none; border:none; color:white; padding:10px; outline:none; font-size:14px; }
.cam-btn { color:#64748b; font-size:20px; cursor:pointer; margin-right:8px; transition:0.3s; }
.cam-btn:hover { color:var(--p); }
.send-btn { width:46px; height:46px; border-radius:50%; background:var(--p); color:white; border:none; cursor:pointer; box-shadow:0 0 20px rgba(37,99,235,0.4); }

/* SCARY OVERLAY */
#freezeOverlay { display:none; position:fixed; inset:0; background:#000; z-index:10000; flex-direction:column; align-items:center; justify-content:center; color:#ff0000; text-align:center; font-family:monospace; }
.glitch { font-size:24px; font-weight:bold; text-transform:uppercase; animation: shake 0.2s infinite; }
@keyframes shake { 0%{transform:translate(0)} 25%{transform:translate(2px)} 50%{transform:translate(-2px)} 75%{transform:translate(2px)} 100%{transform:translate(0)} }

/* NAV */
nav { display:flex; justify-content:space-around; background:var(--card); padding:15px 0; border-top:1px solid rgba(255,255,255,0.1); }
nav button { background:none; border:none; font-size:20px; color:#475569; cursor:pointer; transition:0.3s; }
nav button.active { color:var(--p); transform:translateY(-2px); }

.page { display:none; flex:1; flex-direction:column; overflow:hidden; }
.page.active { display:flex; }
</style>
</head>
<body>

<div id="freezeOverlay">
    <i class="fa-solid fa-triangle-exclamation" style="font-size:70px; margin-bottom:20px;"></i>
    <div class="glitch">CRITICAL SYSTEM BREACH</div>
    <p style="margin-top:20px; color:#660000; font-size:12px;">Protocol 666 Initiated.<br>Unauthorized access detected.<br>Device ID: [HIDDEN]<br>Tracing Location...</p>
    <div style="margin-top:30px; font-size:10px; color:#330000;">DO NOT CLOSE THIS TAB</div>
</div>

<div class="app">
    <header>
        <h1>LIVE CONNECT <span id="godBadge" style="display:none; color:var(--s); font-size:9px; border:1px solid var(--s); padding:2px 6px; margin-left:8px; border-radius:4px;">GOD MODE</span></h1>
        <div style="display:flex; gap:18px; align-items:center;">
            <i class="fa-solid fa-skull" id="admIcon" style="display:none; color:var(--s); cursor:pointer;" onclick="openPage('adminTab', this)"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--s); cursor:pointer;"></i>
        </div>
    </header>

    <div id="chat" class="page active">
        <div style="padding:8px 15px; background:rgba(59,130,246,0.1); font-size:11px; color:var(--p); border-bottom:1px solid rgba(59,130,246,0.2);" id="chatTitle">PUBLIC TERMINAL</div>
        <div id="chatBox"></div>
        <div class="input-panel">
            <div class="input-row">
                <label for="imgUp" class="cam-btn"><i class="fa-solid fa-camera-retro"></i></label>
                <input type="file" id="imgUp" hidden accept="image/*" onchange="sendPhoto(this)">
                <input id="msgInp" placeholder="Enter secure packet...">
            </div>
            <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="users" class="page" style="padding:15px; overflow-y:auto;">
        <h4 style="opacity:0.4; font-size:10px; letter-spacing:2px; margin-bottom:20px;">CONNECTED NODES</h4>
        <div id="uListPublic"></div>
    </div>

    <div id="adminTab" class="page" style="padding:15px; overflow-y:auto;">
        <h3 style="color:var(--s); font-size:14px; margin-bottom:20px; font-family:monospace;">> MASTER_CONTROL_V18</h3>
        <div id="uListAdmin"></div>
        <button onclick="nuclearWipe()" style="width:100%; padding:15px; background:var(--s); color:white; border:none; border-radius:12px; font-weight:bold; margin-top:20px; font-family:monospace;">[!] EXECUTE_WIPE</button>
    </div>

    <nav>
        <button onclick="openPage('chat',this)" class="active"><i class="fa-solid fa-code-merge"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-fingerprint"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user") || prompt("Identify Yourself:");
if(user) {
    localStorage.setItem("lc_user", user);
    // AUTO WELCOME MESSAGE
    if(!localStorage.getItem("welcomed")) {
        setTimeout(()=> {
            const welcomePath = "global";
            push(ref(db, welcomePath), { 
                from: "SYSTEM", 
                text: "Welcome " + user + ". Your activity is being monitored under Protocol 666. Stay Secure.", 
                type: 'text', 
                time: Date.now() 
            });
            localStorage.setItem("welcomed", "true");
        }, 2000);
    }
} else location.reload();

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
            pub.innerHTML += `<div style="background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px; display:flex; justify-content:space-between; align-items:center; border:1px solid rgba(255,255,255,0.05);" onclick="startChat('${u.key}')">
                <span><span style="width:8px; height:8px; background:${d.online?'#22c55e':'#334155'}; border-radius:50%; display:inline-block; margin-right:12px; box-shadow:${d.online?'0 0 10px #22c55e':'none'}"></span>${u.key}</span>
                <i class="fa-solid fa-shield-halved" style="opacity:0.2"></i>
            </div>`;
        }
        if(user === "Admin786" && u.key !== user) {
            adm.innerHTML += `<div style="background:rgba(255,255,255,0.02); padding:12px; border-radius:10px; margin-bottom:8px; display:flex; justify-content:space-between; align-items:center;">
                <span style="font-size:12px;">${u.key}</span>
                <button onclick="toggleFreeze('${u.key}', ${!d.frozen})" style="background:${d.frozen?'#22c55e':var(--s)}; color:white; border:none; padding:6px 14px; border-radius:6px; font-size:10px; font-weight:bold;">${d.frozen?'RESTORE':'NEUTRALIZE'}</button>
            </div>`;
        }
    });
});

let chatTarget = "global";
window.startChat = (t) => {
    chatTarget = "pvt/"+[user, t].sort().join("_");
    document.getElementById("chatTitle").textContent = "> CONNECTION: " + t.toUpperCase();
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
            const del = (user === "Admin786") ? `<i class="fa-solid fa-trash-can" onclick="delMsg('${chatTarget}','${m.key}')" style="font-size:11px; margin-left:12px; opacity:0.3; cursor:pointer; color:red;"></i>` : "";
            
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
window.nuclearWipe = () => { if(confirm("CONFIRM SYSTEM PURGE?")) remove(ref(db)); location.reload(); };

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
