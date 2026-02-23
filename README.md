<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | V19 Ultra</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#3b82f6; --s:#ef4444; --bg:#020617; --card:#0f172a; --text:#f8fafc; --me:#2563eb; --other:#1e293b; }
body { margin:0; font-family:'Inter', sans-serif; background:var(--bg); color:var(--text); overflow:hidden; height:100dvh; }
.app { max-width:480px; margin:auto; height:100dvh; display:flex; flex-direction:column; background:var(--bg); position:relative; }

/* HEADER */
header { padding:45px 15px 15px; background:var(--card); display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid rgba(255,255,255,0.1); z-index:100; }
header h1 { font-size:16px; margin:0; font-weight:900; color:var(--p); letter-spacing:2px; }

/* CHAT BOX - FULL SCREEN FIX */
.chat-container { flex:1; display:flex; flex-direction:column; position:relative; overflow:hidden; background: radial-gradient(circle at center, #0f172a 0%, #020617 100%); }
#chatBox { flex:1; overflow-y:auto; padding:15px; display:flex; flex-direction:column; gap:12px; padding-bottom:100px; }
.msg { padding:12px 16px; border-radius:18px; max-width:80%; font-size:14px; position:relative; line-height:1.5; box-shadow: 0 4px 15px rgba(0,0,0,0.2); }
.msg.me { align-self:flex-end; background:var(--me); color:white; border-bottom-right-radius:2px; }
.msg.other { align-self:flex-start; background:var(--other); color:white; border-bottom-left-radius:2px; }
.msg b { display:block; font-size:10px; margin-bottom:5px; color:var(--p); text-transform:uppercase; }
.msg img { max-width:100%; border-radius:12px; margin-top:8px; border:1px solid rgba(255,255,255,0.1); }

/* INPUT BAR FIX - NOT OVERLAPPING */
.input-fixed { position:absolute; bottom:0; width:100%; background:var(--card); padding:10px 15px 35px; border-top:1px solid rgba(255,255,255,0.1); display:flex; gap:12px; align-items:center; box-sizing:border-box; }
.input-row { flex:1; background:rgba(255,255,255,0.03); border-radius:30px; padding:5px 18px; display:flex; align-items:center; border:1px solid rgba(255,255,255,0.1); }
.input-row input { flex:1; background:none; border:none; color:white; padding:10px; outline:none; font-size:14px; }
.cam-btn { color:#64748b; font-size:20px; cursor:pointer; margin-right:8px; }
.send-btn { width:46px; height:46px; border-radius:50%; background:var(--p); color:white; border:none; cursor:pointer; box-shadow:0 0 20px rgba(37,99,235,0.4); }

/* SCARY LOCK SCREEN */
#freezeOverlay { display:none; position:fixed; inset:0; background:#000; z-index:10000; flex-direction:column; align-items:center; justify-content:center; color:#ff0000; text-align:center; font-family:monospace; }
.terminal-text { font-size:10px; color:#330000; text-align:left; width:80%; margin-top:20px; overflow:hidden; white-space:nowrap; border-right: 2px solid red; animation: typing 3s steps(40) infinite; }
@keyframes typing { from { width: 0 } to { width: 80% } }

/* NAV */
nav { display:flex; justify-content:space-around; background:var(--card); padding:15px 0; border-top:1px solid rgba(255,255,255,0.1); }
nav button { background:none; border:none; font-size:20px; color:#475569; cursor:pointer; }
nav button.active { color:var(--p); }

.page { display:none; flex:1; overflow-y:auto; padding:15px; }
.page.active { display:block; }
</style>
</head>
<body>

<div id="freezeOverlay">
    <i class="fa-solid fa-biohazard" style="font-size:70px; margin-bottom:20px;"></i>
    <div style="font-size:22px; font-weight:bold;">SYSTEM HIJACKED</div>
    <p style="color:#660000; font-size:12px; margin-top:10px;">PROTOCOL_666: ACTIVE<br>Wiping local storage... 45%<br>Uploading data to server...</p>
    <div class="terminal-text">> root@breach: access_denied_err_102</div>
    <div class="terminal-text">> root@breach: trace_route_successful</div>
</div>

<div class="app">
    <header>
        <h1>LIVE CONNECT <span id="godBadge" style="display:none; color:var(--s); font-size:9px; border:1px solid var(--s); padding:2px 6px; margin-left:8px; border-radius:4px;">GOD MODE</span></h1>
        <div style="display:flex; gap:18px; align-items:center;">
            <i class="fa-solid fa-ghost" id="admIcon" style="display:none; color:var(--s); cursor:pointer;" onclick="openPage('adminTab', this)"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--s); cursor:pointer;"></i>
        </div>
    </header>

    <div id="chat" class="page active" style="padding:0; height:calc(100dvh - 160px);">
        <div class="chat-container">
            <div style="padding:8px 15px; background:rgba(59,130,246,0.1); font-size:11px; color:var(--p);" id="chatTitle">PUBLIC TERMINAL</div>
            <div id="chatBox"></div>
            <div class="input-fixed">
                <div class="input-row">
                    <label for="imgUp" class="cam-btn"><i class="fa-solid fa-camera"></i></label>
                    <input type="file" id="imgUp" hidden accept="image/*" onchange="sendPhoto(this)">
                    <input id="msgInp" placeholder="Enter secure packet...">
                </div>
                <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <div id="users" class="page">
        <h4 style="opacity:0.4; font-size:10px; letter-spacing:2px; margin-bottom:20px;">CONNECTED NODES</h4>
        <div id="uListPublic"></div>
    </div>

    <div id="adminTab" class="page">
        <h3 style="color:var(--s); font-size:14px; margin-bottom:20px;">> MASTER_CONTROL_CONSOLE</h3>
        <div id="uListAdmin"></div>
        <button onclick="nuclearWipe()" style="width:100%; padding:15px; background:var(--s); color:white; border:none; border-radius:12px; font-weight:bold; margin-top:20px;">PURGE ALL DATA</button>
    </div>

    <nav>
        <button onclick="openPage('chat',this)" class="active"><i class="fa-solid fa-terminal"></i></button>
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
            pub.innerHTML += `<div style="background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px; display:flex; justify-content:space-between; align-items:center;" onclick="startChat('${u.key}')">
                <span><span style="width:8px; height:8px; background:${d.online?'#22c55e':'#334155'}; border-radius:50%; display:inline-block; margin-right:12px;"></span>${u.key}</span>
                <i class="fa-solid fa-chevron-right" style="opacity:0.2"></i>
            </div>`;
        }
        if(user === "Admin786" && u.key !== user) {
            adm.innerHTML += `<div style="background:rgba(255,255,255,0.02); padding:12px; border-radius:10px; margin-bottom:8px; display:flex; justify-content:space-between; align-items:center;">
                <span style="font-size:12px;">${u.key}</span>
                <button onclick="toggleFreeze('${u.key}', ${!d.frozen})" style="background:${d.frozen?'#22c55e':var(--s)}; color:white; border:none; padding:6px 14px; border-radius:6px; font-size:10px; font-weight:bold;">${d.frozen?'RESTORE':'HIJACK'}</button>
            </div>`;
        }
    });
});

let chatTarget = "global";
window.startChat = (t) => {
    chatTarget = "pvt/"+[user, t].sort().join("_");
    document.getElementById("chatTitle").textContent = "> TARGET: " + t.toUpperCase();
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
            const del = (user === "Admin786") ? `<i class="fa-solid fa-trash-can" onclick="delMsg('${chatTarget}','${m.key}')" style="font-size:11px; margin-left:12px; color:red; cursor:pointer;"></i>` : "";
            
            box.innerHTML += `<div class="msg ${isMe?'me':'other'}">
                <b>${d.from}</b> ${body} ${del}
                <div style="font-size:8px; opacity:0.3; margin-top:5px; text-align:right;">${new Date(d.time).toLocaleTimeString()}</div>
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
window.nuclearWipe = () => { if(confirm("EXECUTE SYSTEM WIPE?")) remove(ref(db)); location.reload(); };

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
