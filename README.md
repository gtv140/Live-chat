<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --bg:#0a0a0a; --card:#111; --text:#e5e5e5; --muted:#aaa; --primary:#6b5bff; --accent:#ff5c8d; --success:#22c55e; }
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);overflow:hidden;height:100dvh;}

/* LOGIN SCREEN FIX */
#loginPage{position:fixed;inset:0;background:var(--bg);z-index:2000;display:flex;align-items:center;justify-content:center;padding:20px;}
.login-card{background:var(--card);padding:30px;border-radius:25px;width:100%;max-width:350px;text-align:center;box-shadow:0 0 30px rgba(107,91,255,0.2);border:1px solid rgba(255,255,255,0.05);}
.login-card h3{margin-bottom:20px;color:var(--primary);letter-spacing:1px;}
.login-card input{width:100%;padding:15px;border-radius:15px;border:1px solid #333;background:#050505;color:#fff;margin-bottom:20px;outline:none;box-sizing:border-box;}
.login-card button{width:100%;padding:15px;border-radius:15px;border:none;background:linear-gradient(90deg, var(--primary), var(--accent));color:white;font-weight:bold;cursor:pointer;}

header{padding:45px 16px 15px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 3px 15px rgba(0,0,0,.7);z-index:100;}
header h1{margin:0;font-size:22px;color:var(--primary);text-shadow:0 0 12px var(--accent);}

/* SCARY LOCK */
#freezeOverlay{display:none;position:fixed;inset:0;background:#000;z-index:10000;flex-direction:column;align-items:center;justify-content:center;color:#ff0000;text-align:center;font-family:monospace;}

.page{display:none;padding:16px;flex:1;flex-direction:column;overflow-y:auto;height:calc(100dvh - 170px);}
.page.active{display:flex;}

/* HERO CARD */
.hero{background:linear-gradient(135deg,#6b5bff,#ff5c8d);color:#fff;padding:40px 20px;border-radius:25px;text-align:center;margin-bottom:20px;}
.hero h2{margin:0;font-size:24px;}
.hero p{margin:5px 0 0;font-size:12px;opacity:0.8;border-top:1px solid rgba(255,255,255,0.3);display:inline-block;padding-top:5px;}

.stats-grid{display:grid;grid-template-columns:1fr 1fr;gap:15px;}
.stat-box{background:var(--card);padding:20px;border-radius:20px;text-align:center;border:1px solid rgba(255,255,255,0.05);}

#chatBox{flex:1;overflow-y:auto;display:flex;flex-direction:column;gap:12px;padding:10px 0;}
.msg{background:#1a1a1a;padding:12px 15px;border-radius:18px;max-width:80%;font-size:14px;}
.msg.me{align-self:flex-end;background:var(--primary);color:white;border-bottom-right-radius:2px;}
.msg.other{align-self:flex-start;background:#262626;border-bottom-left-radius:2px;}
.msg img{max-width:100%;border-radius:12px;margin-top:8px;}

.input-row{display:flex;gap:10px;padding:15px;background:var(--card);position:fixed;bottom:75px;width:100%;max-width:480px;box-sizing:border-box;}
.input-row input{flex:1;padding:12px 18px;border-radius:25px;border:1px solid #333;background:#050505;color:#fff;outline:none;}
.send-btn{width:45px;height:45px;border-radius:50%;border:none;background:var(--primary);color:#fff;}

nav{display:flex;justify-content:space-around;background:var(--card);padding:15px 0;position:fixed;bottom:0;width:100%;max-width:480px;border-top:1px solid #222;}
nav button{background:none;border:none;font-size:22px;color:var(--muted);cursor:pointer;}
nav button.active{color:var(--primary);}
</style>
</head>
<body>

<div id="loginPage">
    <div class="login-card">
        <i class="fa-solid fa-shield-halved" style="font-size:50px; color:var(--primary); margin-bottom:15px;"></i>
        <h3>Identify Node</h3>
        <input type="text" id="usernameInput" placeholder="Enter Username...">
        <button onclick="login()">INITIALIZE SESSION</button>
    </div>
</div>

<div id="freezeOverlay">
    <i class="fa-solid fa-user-slash" style="font-size:60px;margin-bottom:20px;"></i>
    <div style="font-size:20px; font-weight:bold;">PROTOCOL 666 ACTIVE</div>
    <p style="font-size:11px;margin-top:15px;opacity:0.6;">Your session has been terminated by Admin.</p>
</div>

<div class="app" style="max-width:480px; margin:auto; background:var(--bg); height:100dvh;">
    <header>
        <h1>Live Connect</h1>
        <div style="display:flex;gap:15px;align-items:center;">
            <i class="fa-solid fa-ghost" id="admIcon" style="display:none;color:var(--accent);cursor:pointer;" onclick="openPage('adminTab',this)"></i>
            <i class="fa-solid fa-right-from-bracket" onclick="logout()" style="color:var(--accent);cursor:pointer;"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="hero">
            <h2 id="dashUser">Welcome</h2>
            <p>Secure Network Active</p>
        </div>
        <div class="stats-grid">
            <div class="stat-box"><h4 style="color:var(--primary); font-size:10px;">ONLINE</h4><span id="onlineCount">0</span></div>
            <div class="stat-box"><h4 style="color:var(--success); font-size:10px;">GROUPS</h4><span id="groupCount">0</span></div>
        </div>
    </div>

    <div id="chat" class="page" style="padding-bottom:120px;">
        <div style="font-size:10px;color:var(--primary);margin-bottom:10px;font-weight:bold;" id="chatTitle">GLOBAL_TERMINAL</div>
        <div id="chatBox"></div>
        <div class="input-row">
            <label for="imgInp" style="padding:10px; color:var(--muted);"><i class="fa-solid fa-camera"></i></label>
            <input type="file" id="imgInp" hidden accept="image/*" onchange="sendPhoto(this)">
            <input id="msgInput" placeholder="Secure message...">
            <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="nodes" class="page">
        <h3 style="font-size:11px;opacity:0.4;letter-spacing:2px;">NODES</h3>
        <div id="userList"></div>
        <div style="margin-top:30px; display:flex; justify-content:space-between; align-items:center; margin-bottom:15px;">
            <h3 style="font-size:11px;opacity:0.4;letter-spacing:2px;">GROUPS</h3>
            <button onclick="createGroupPrompt()" style="background:none; border:1px solid var(--primary); color:var(--primary); font-size:10px; padding:4px 10px; border-radius:10px;">+ NEW</button>
        </div>
        <div id="groupList"></div>
    </div>

    <div id="adminTab" class="page">
        <h3 style="color:var(--accent);font-size:14px;margin-bottom:20px;">> ROOT_ACCESS</h3>
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

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");
if(user) { 
    document.getElementById("loginPage").style.display = "none";
    initUser(user);
}

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return alert("Enter Username");
    localStorage.setItem("lc_user", val);
    location.reload();
};

function initUser(u) {
    document.getElementById("dashUser").textContent = "Welcome, " + u;
    const myRef = ref(db, "users/"+u);
    update(myRef, { online:true, frozen:false });
    onDisconnect(myRef).update({ online:false });

    if(u === "Admin786") document.getElementById("admIcon").style.display = "block";

    onValue(ref(db, "users/"+u+"/frozen"), snap => {
        if(snap.val()) document.getElementById("freezeOverlay").style.display = "flex";
    });

    syncData();
}

function syncData() {
    onValue(ref(db, "users"), snap => {
        const ul = document.getElementById("userList"), al = document.getElementById("admList");
        let count = 0; ul.innerHTML = ""; al.innerHTML = "";
        snap.forEach(un => {
            const d = un.val();
            if(d.online) count++;
            if(un.key !== user) {
                ul.innerHTML += `<div style="background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px; display:flex; justify-content:space-between;" onclick="startChat('${un.key}', false)">
                    <span>${un.key}</span><i class="fa-solid fa-chevron-right" style="opacity:0.2"></i>
                </div>`;
                if(user === "Admin786") {
                    al.innerHTML += `<div style="background:var(--card); padding:10px; border-radius:10px; margin-bottom:5px; display:flex; justify-content:space-between;">
                        <span>${un.key}</span><button onclick="hijack('${un.key}', ${!d.frozen})" style="background:${d.frozen?'#22c55e':var(--accent)}; color:white; border:none; padding:4px 8px; border-radius:5px; font-size:10px;">${d.frozen?'RESTORE':'HIJACK'}</button>
                    </div>`;
                }
            }
        });
        document.getElementById("onlineCount").textContent = count;
    });

    onValue(ref(db, "groups"), snap => {
        const gl = document.getElementById("groupList"); gl.innerHTML = "";
        document.getElementById("groupCount").textContent = snap.size;
        snap.forEach(g => {
            gl.innerHTML += `<div style="background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px;" onclick="startChat('${g.key}', true)">${g.key}</div>`;
        });
    });
}

let chatPath = "global";
window.startChat = (t, isG) => {
    chatPath = isG ? "groups/"+t : "pvt/"+[user, t].sort().join("_");
    document.getElementById("chatTitle").textContent = "NODE: " + t.toUpperCase();
    openPage('chat', document.querySelectorAll('nav button')[1]);
    loadMsgs();
};

function loadMsgs() {
    onValue(ref(db, chatPath), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val(), isMe = d.from === user;
            const content = d.type === 'img' ? `<img src="${d.text}">` : d.text;
            const del = (user === "Admin786") ? `<i class="fa-solid fa-trash" onclick="delM('${chatPath}','${m.key}')" style="margin-left:10px; color:red; font-size:10px;"></i>` : "";
            box.innerHTML += `<div class="msg ${isMe?'me':'other'}"><b>${d.from}</b>${content}${del}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
}

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

window.createGroupPrompt = () => { const g = prompt("Group Name:"); if(g) set(ref(db, "groups/"+g), { created: Date.now() }); };
window.hijack = (t, s) => update(ref(db, "users/"+t), { frozen:s });
window.delM = (p, k) => remove(ref(db, p+"/"+k));
window.wipeAll = () => { if(confirm("WIPE?")) remove(ref(db)); location.reload(); };
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
