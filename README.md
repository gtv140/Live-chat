<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --bg:#0a0a0a; --card:#111; --text:#e5e5e5; --muted:#aaa; --primary:#6b5bff; --accent:#ff5c8d; --success:#22c55e; }
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);overflow:hidden;height:100dvh;}

/* HEADER */
header{padding:45px 16px 15px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 3px 15px rgba(0,0,0,.7);}
header h1{margin:0;font-size:22px;color:var(--primary);text-shadow:0 0 12px var(--accent);}

/* SCARY LOCK SCREEN */
#freezeOverlay{display:none;position:fixed;inset:0;background:#000;z-index:10000;flex-direction:column;align-items:center;justify-content:center;color:#ff0000;text-align:center;font-family:monospace;}
.glitch{font-size:24px;font-weight:bold;animation:shake 0.2s infinite;}
@keyframes shake{0%{transform:translate(0)} 25%{transform:translate(2px)} 50%{transform:translate(-2px)} 100%{transform:translate(0)}}

/* PAGES */
.page{display:none;padding:16px;flex:1;flex-direction:column;overflow-y:auto;height:calc(100dvh - 160px);}
.page.active{display:flex;}

/* ORIGINAL WELCOME CARD */
.hero{background:linear-gradient(135deg,#6b5bff,#ff5c8d);color:#fff;padding:40px 20px;border-radius:25px;text-align:center;box-shadow:0 10px 30px rgba(107,91,255,0.3);margin-bottom:20px;}
.hero h2{margin:0;font-size:24px;}
.hero p{margin:5px 0 0;font-size:12px;opacity:0.8;border-top:1px solid rgba(255,255,255,0.3);display:inline-block;padding-top:5px;}

/* DASH STATS */
.stats-grid{display:grid;grid-template-columns:1fr 1fr;gap:15px;}
.stat-box{background:var(--card);padding:20px;border-radius:20px;text-align:center;box-shadow:0 4px 15px rgba(0,0,0,0.5);}
.stat-box h4{margin:0 0 5px;font-size:11px;letter-spacing:1px;text-transform:uppercase;}
.stat-box span{font-size:24px;font-weight:bold;}

/* CHAT BOX */
#chatBox{flex:1;overflow-y:auto;padding:10px 0;display:flex;flex-direction:column;gap:10px;}
.msg{background:#1a1a1a;padding:12px 15px;border-radius:18px;max-width:80%;font-size:14px;position:relative;box-shadow:0 4px 10px rgba(0,0,0,0.3);}
.msg.me{align-self:flex-end;background:var(--primary);color:white;border-bottom-right-radius:2px;}
.msg.other{align-self:flex-start;background:#262626;border-bottom-left-radius:2px;}
.msg b{display:block;font-size:10px;margin-bottom:4px;color:rgba(255,255,255,0.6);}
.msg img{max-width:100%;border-radius:12px;margin-top:8px;}

/* INPUT ROW */
.input-row{display:flex;gap:10px;padding:15px;background:var(--card);border-top:1px solid #222;position:fixed;bottom:70px;width:100%;box-sizing:border-box;}
.input-row input{flex:1;padding:12px 18px;border-radius:25px;border:1px solid #333;background:#050505;color:#fff;outline:none;}
.input-row button{width:45px;height:45px;border-radius:50%;border:none;background:var(--primary);color:#fff;cursor:pointer;}

/* NAVIGATION */
nav{display:flex;justify-content:space-around;background:var(--card);padding:15px 0;position:fixed;bottom:0;width:100%;border-top:1px solid #222;}
nav button{background:none;border:none;font-size:22px;color:var(--muted);cursor:pointer;}
nav button.active{color:var(--primary);}

/* ADMIN ITEMS */
.node-item{background:var(--card);padding:15px;border-radius:15px;margin-bottom:10px;display:flex;justify-content:space-between;align-items:center;}
</style>
</head>
<body>

<div id="freezeOverlay">
    <i class="fa-solid fa-skull-crossbones" style="font-size:60px;margin-bottom:20px;"></i>
    <div class="glitch">SYSTEM HIJACKED</div>
    <p style="font-size:11px;margin-top:15px;opacity:0.6;">Protocol 666 Active.<br>Administrator has revoked your node access.</p>
</div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div style="display:flex;gap:15px;">
            <i class="fa-solid fa-ghost" id="admIcon" style="display:none;color:var(--accent);cursor:pointer;" onclick="openPage('adminTab',this)"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--accent);cursor:pointer;"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="hero">
            <h2 id="welcomeText">Welcome, User</h2>
            <p>Secure Network Active</p>
        </div>
        <div class="stats-grid">
            <div class="stat-box">
                <h4 style="color:var(--primary)">Online</h4>
                <span id="onlineCount">0</span>
            </div>
            <div class="stat-box">
                <h4 style="color:var(--success)">Groups</h4>
                <span id="groupCount">0</span>
            </div>
        </div>
    </div>

    <div id="chat" class="page">
        <div style="font-size:10px;color:var(--primary);margin-bottom:10px;" id="chatTitle">GLOBAL_NETWORK</div>
        <div id="chatBox"></div>
        <div class="input-row">
            <label for="imgInp"><i class="fa-solid fa-camera" style="color:var(--muted);padding:10px;"></i></label>
            <input type="file" id="imgInp" hidden accept="image/*" onchange="sendPhoto(this)">
            <input id="msgInput" placeholder="Enter secure packet...">
            <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="nodes" class="page">
        <h3 style="font-size:12px;opacity:0.5;letter-spacing:2px;">DETECTED NODES</h3>
        <div id="userList"></div>
        <h3 style="font-size:12px;opacity:0.5;letter-spacing:2px;margin-top:20px;">ACTIVE GROUPS</h3>
        <div id="groupList"></div>
    </div>

    <div id="adminTab" class="page">
        <h3 style="color:var(--accent);font-size:14px;margin-bottom:20px;">> MASTER_CONSOLE</h3>
        <div id="admList"></div>
        <button onclick="wipeAll()" style="width:100%;padding:15px;background:var(--accent);color:white;border:none;border-radius:12px;font-weight:bold;margin-top:20px;">NUCLEAR WIPE</button>
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

const config = { 
    apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", 
    databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" 
};
const app = initializeApp(config);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user") || prompt("Username:");
if(!user) location.reload(); else localStorage.setItem("lc_user", user);

document.getElementById("welcomeText").textContent = "Welcome, " + user;
const myRef = ref(db, "users/"+user);
update(myRef, { online:true, frozen:false });
onDisconnect(myRef).update({ online:false });

if(user === "Admin786") {
    document.getElementById("admIcon").style.display = "block";
}

onValue(ref(db, "users/"+user+"/frozen"), snap => {
    if(snap.val()) document.getElementById("freezeOverlay").style.display = "flex";
});

// DATA SYNC
onValue(ref(db, "users"), snap => {
    const ul = document.getElementById("userList");
    const al = document.getElementById("admList");
    let count = 0; ul.innerHTML = ""; al.innerHTML = "";
    snap.forEach(u => {
        const d = u.val();
        if(d.online) count++;
        if(u.key !== user) {
            ul.innerHTML += `<div class="node-item" onclick="startChat('${u.key}', false)">
                <span><span style="width:8px;height:8px;background:${d.online?'#22c55e':'#333'};border-radius:50%;display:inline-block;margin-right:10px;"></span>${u.key}</span>
                <i class="fa-solid fa-chevron-right" style="opacity:0.2"></i>
            </div>`;
        }
        if(user === "Admin786" && u.key !== user) {
            al.innerHTML += `<div class="node-item">
                <span style="font-size:12px;">${u.key}</span>
                <button onclick="hijack('${u.key}', ${!d.frozen})" style="background:${d.frozen?var(--success):var(--accent)};color:white;border:none;padding:6px 12px;border-radius:6px;font-size:10px;">${d.frozen?'RESTORE':'HIJACK'}</button>
            </div>`;
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

onValue(ref(db, "groups"), snap => {
    const gl = document.getElementById("groupList"); gl.innerHTML = "";
    document.getElementById("groupCount").textContent = snap.size;
    snap.forEach(g => {
        gl.innerHTML += `<div class="node-item" onclick="startChat('${g.key}', true)">
            <span><i class="fa-solid fa-users" style="margin-right:10px;color:var(--primary)"></i>${g.key}</span>
        </div>`;
    });
});

let chatPath = "global";
window.startChat = (t, isG) => {
    chatPath = isG ? "groups/"+t : "pvt/"+[user, t].sort().join("_");
    document.getElementById("chatTitle").textContent = "CONNECTION: " + t.toUpperCase();
    openPage('chat', document.querySelectorAll('nav button')[1]);
    loadMsgs();
};

function loadMsgs() {
    onValue(ref(db, chatPath), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const isMe = d.from === user;
            const body = d.type === 'img' ? `<img src="${d.text}">` : d.text;
            const del = (user === "Admin786") ? `<i class="fa-solid fa-trash" onclick="delM('${chatPath}','${m.key}')" style="margin-left:10px;color:red;font-size:11px;cursor:pointer;"></i>` : "";
            
            box.innerHTML += `<div class="msg ${isMe?'me':'other'}">
                <b>${d.from}</b> ${body} ${del}
                <div style="font-size:8px;opacity:0.3;text-align:right;margin-top:4px;">${new Date(d.time).toLocaleTimeString()}</div>
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
window.wipeAll = () => { if(confirm("CONFIRM NUCLEAR WIPE?")) remove(ref(db)); location.reload(); };
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
