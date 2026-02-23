<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | V20 Ultra</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --bg:#020617; --card:#0f172a; --text:#f8fafc; --muted:#64748b; --primary:#3b82f6; --accent:#ef4444; --success:#22c55e; --me:#2563eb; --other:#1e293b; }
body{margin:0;font-family:'Inter', sans-serif;background:var(--bg);color:var(--text);overflow:hidden;height:100dvh;}
.app{max-width:480px;margin:auto;height:100dvh;display:flex;flex-direction:column;position:relative;}

/* HEADER */
header{padding:45px 16px 15px;display:flex;justify-content:space-between;align-items:center;background:var(--card);border-bottom:1px solid rgba(255,255,255,0.05);}
header h1{margin:0;font-size:18px;color:var(--primary);text-transform:uppercase;letter-spacing:2px;font-weight:900;}

/* SCARY LOCK */
#freezeOverlay{display:none;position:fixed;inset:0;background:#000;z-index:10000;flex-direction:column;align-items:center;justify-content:center;color:var(--accent);text-align:center;font-family:monospace;}
.glitch{font-size:24px;font-weight:bold;animation:shake 0.2s infinite;}
@keyframes shake{0%{transform:translate(0)} 25%{transform:translate(2px)} 50%{transform:translate(-2px)} 75%{transform:translate(2px)} 100%{transform:translate(0)}}

/* PAGES */
.page{display:none;flex:1;flex-direction:column;overflow-y:auto;padding:15px;padding-bottom:100px;}
.page.active{display:flex;}

/* CHAT BOX */
#chatBox{flex:1;overflow-y:auto;display:flex;flex-direction:column;gap:12px;padding:10px 0;}
.msg{padding:12px 16px;border-radius:18px;max-width:80%;font-size:14px;position:relative;line-height:1.5;box-shadow:0 4px 10px rgba(0,0,0,0.3);}
.msg.me{align-self:flex-end;background:var(--me);border-bottom-right-radius:2px;}
.msg.other{align-self:flex-start;background:var(--other);border-bottom-left-radius:2px;}
.msg b{display:block;font-size:10px;margin-bottom:4px;color:var(--primary);text-transform:uppercase;}
.msg img{max-width:100%;border-radius:12px;margin-top:8px;}

/* INPUT AREA */
.input-fixed{position:absolute;bottom:0;left:0;width:100%;background:var(--card);padding:10px 15px 35px;display:flex;gap:10px;align-items:center;border-top:1px solid rgba(255,255,255,0.1);box-sizing:border-box;}
.input-row{flex:1;background:rgba(255,255,255,0.03);border-radius:30px;padding:5px 15px;display:flex;align-items:center;border:1px solid rgba(255,255,255,0.1);}
.input-row input{flex:1;background:none;border:none;color:white;padding:10px;outline:none;}
.send-btn{width:45px;height:45px;border-radius:50%;background:var(--primary);color:white;border:none;box-shadow:0 0 15px rgba(59,130,246,0.4);}

/* ADMIN DASH */
.adm-card{background:rgba(255,255,255,0.02);padding:15px;border-radius:15px;margin-bottom:10px;display:flex;justify-content:space-between;align-items:center;border:1px solid rgba(255,255,255,0.05);}

/* NAV */
nav{display:flex;justify-content:space-around;background:var(--card);padding:15px 0;border-top:1px solid rgba(255,255,255,0.1);z-index:100;}
nav button{background:none;border:none;font-size:20px;color:var(--muted);cursor:pointer;}
nav button.active{color:var(--primary);}
</style>
</head>
<body>

<div id="freezeOverlay">
    <i class="fa-solid fa-radiation" style="font-size:60px;margin-bottom:20px;"></i>
    <div class="glitch">CRITICAL BREACH</div>
    <p style="font-size:12px;margin-top:15px;opacity:0.6;">Protocol 666: Access Revoked.<br>Tracing user node...</p>
</div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div style="display:flex;gap:15px;">
            <i class="fa-solid fa-ghost" id="admBtn" style="display:none;color:var(--accent);cursor:pointer;" onclick="openPage('adminTab',this)"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--accent);cursor:pointer;"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div style="background:linear-gradient(135deg, var(--primary), #8b5cf6); padding:25px; border-radius:20px; text-align:center; margin-bottom:20px;">
            <h2 style="margin:0;">Welcome, <span id="uName">User</span></h2>
            <p style="font-size:12px;opacity:0.8;">Secure Network Active</p>
        </div>
        <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px;">
            <div style="background:var(--card);padding:15px;border-radius:15px;text-align:center;">
                <h4 style="margin:0;color:var(--primary);font-size:12px;">ONLINE</h4>
                <span id="onlineCount" style="font-size:20px;font-weight:bold;">0</span>
            </div>
            <div style="background:var(--card);padding:15px;border-radius:15px;text-align:center;">
                <h4 style="margin:0;color:var(--success);font-size:12px;">GROUPS</h4>
                <span id="groupCount" style="font-size:20px;font-weight:bold;">0</span>
            </div>
        </div>
    </div>

    <div id="chat" class="page" style="padding-bottom:110px;">
        <div style="padding:5px 10px; color:var(--primary); font-size:10px; font-weight:bold;" id="chatT">GLOBAL TERMINAL</div>
        <div id="chatBox"></div>
        <div class="input-fixed">
            <div class="input-row">
                <label for="imgInp"><i class="fa-solid fa-image" style="color:var(--muted);padding:10px;cursor:pointer;"></i></label>
                <input type="file" id="imgInp" hidden accept="image/*" onchange="sendPhoto(this)">
                <input id="msgInput" placeholder="Secure message...">
            </div>
            <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="nodes" class="page">
        <h4 style="font-size:10px;letter-spacing:2px;opacity:0.4;">ACTIVE NODES</h4>
        <div id="userList"></div>
        <h4 style="font-size:10px;letter-spacing:2px;opacity:0.4;margin-top:20px;">GROUPS</h4>
        <div id="groupList"></div>
    </div>

    <div id="adminTab" class="page">
        <h3 style="color:var(--accent);font-size:14px;margin-bottom:20px;">> MASTER_CONTROL_V20</h3>
        <div id="admList"></div>
        <button onclick="fullPurge()" style="width:100%;background:var(--accent);color:white;border:none;padding:15px;border-radius:12px;font-weight:bold;margin-top:20px;">NUCLEAR WIPE</button>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
        <button onclick="openPage('nodes',this)"><i class="fa-solid fa-fingerprint"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user") || prompt("Identity:");
if(!user) location.reload(); else localStorage.setItem("lc_user", user);

document.getElementById("uName").textContent = user;
const myRef = ref(db, "users/"+user);
update(myRef, { online:true, frozen:false });
onDisconnect(myRef).update({ online:false });

if(user === "Admin786") {
    document.getElementById("admBtn").style.display = "block";
}

onValue(ref(db, "users/"+user+"/frozen"), snap => {
    if(snap.val()) document.getElementById("freezeOverlay").style.display = "flex";
});

// SYNC DATA
onValue(ref(db, "users"), snap => {
    const list = document.getElementById("userList");
    const adm = document.getElementById("admList");
    let count = 0; list.innerHTML = ""; adm.innerHTML = "";
    snap.forEach(u => {
        const d = u.val();
        if(d.online) count++;
        if(u.key !== user) {
            list.innerHTML += `<div class="adm-card" onclick="startChat('${u.key}', false)">
                <span><span style="width:8px;height:8px;background:${d.online?'#22c55e':'#334155'};border-radius:50%;display:inline-block;margin-right:10px;"></span>${u.key}</span>
                <i class="fa-solid fa-chevron-right" style="opacity:0.2"></i>
            </div>`;
        }
        if(user === "Admin786" && u.key !== user) {
            adm.innerHTML += `<div class="adm-card">
                <span style="font-size:12px;">${u.key}</span>
                <button onclick="hijack('${u.key}', ${!d.frozen})" style="background:${d.frozen?'#22c55e':var(--accent)};color:white;border:none;padding:5px 10px;border-radius:5px;font-size:10px;">${d.frozen?'RESTORE':'HIJACK'}</button>
            </div>`;
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

onValue(ref(db, "groups"), snap => {
    const gl = document.getElementById("groupList"); gl.innerHTML = "";
    document.getElementById("groupCount").textContent = snap.size;
    snap.forEach(g => {
        gl.innerHTML += `<div class="adm-card" onclick="startChat('${g.key}', true)">
            <span><i class="fa-solid fa-layer-group" style="margin-right:10px;color:var(--primary)"></i>${g.key}</span>
        </div>`;
    });
});

let chatPath = "global";
window.startChat = (t, isG) => {
    chatPath = isG ? "groups/"+t : "pvt/"+[user, t].sort().join("_");
    document.getElementById("chatT").textContent = "> CONNECTION: " + t.toUpperCase();
    openPage('chat', document.querySelectorAll('nav button')[1]);
    loadMsgs();
};

function loadMsgs() {
    onValue(ref(db, chatPath), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const isMe = d.from === user;
            const content = d.type === 'img' ? `<img src="${d.text}">` : d.text;
            const del = (user === "Admin786") ? `<i class="fa-solid fa-trash" onclick="delM('${chatPath}','${m.key}')" style="margin-left:10px;color:red;font-size:10px;cursor:pointer;"></i>` : "";
            
            box.innerHTML += `<div class="msg ${isMe?'me':'other'}">
                <b>${d.from}</b> ${content} ${del}
                <div style="font-size:8px;opacity:0.3;text-align:right;margin-top:5px;">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</div>
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

window.sendPhoto = (input) => {
    const reader = new FileReader();
    reader.onload = (e) => push(ref(db, chatPath), { from:user, text:e.target.result, type:'img', time:Date.now() });
    reader.readAsDataURL(input.files[0]);
};

window.hijack = (t, s) => update(ref(db, "users/"+t), { frozen:s });
window.delM = (p, k) => remove(ref(db, p+"/"+k));
window.fullPurge = () => { if(confirm("PURGE SYSTEM?")) remove(ref(db)); location.reload(); };
window.logout = () => { localStorage.clear(); location.reload(); };
window.openPage = (p, b) => {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b) b.classList.add('active');
};
</script>
</body>
</html>
