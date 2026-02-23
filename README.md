<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | God Mode 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#05050a; --card:rgba(20,20,30,0.8); --text:#f8fafc;
  --primary:#c026d3; --s:#0ea5e9; --acc:#f43f5e; --success:#22c55e;
  --glass: rgba(255, 255, 255, 0.05); --border: rgba(255, 255, 255, 0.1);
}
* { box-sizing: border-box; font-family: 'Inter', sans-serif; transition: 0.3s; }
body { margin:0; background:var(--bg); color:var(--text); overflow:hidden; background-image: radial-gradient(circle at 0% 0%, #1e1b4b 0%, #05050a 100%); height: 100dvh; }

/* DANGEROUS OVERLAYS */
#freezeOverlay { display:none; position:fixed; inset:0; background:#000; z-index:99999; flex-direction:column; align-items:center; justify-content:center; color:var(--acc); text-align:center; backdrop-filter: blur(40px); }
#hackOverlay { display:none; position:fixed; inset:0; background:#000; z-index:88888; color:#0f0; padding:30px; font-family: 'Courier New', monospace; font-size:12px; }

.app { max-width:480px; margin:auto; height:100dvh; display:flex; flex-direction:column; border-left:1px solid var(--border); border-right:1px solid var(--border); position: relative; }

/* HEADER */
header { padding:50px 20px 15px; background:rgba(0,0,0,0.4); backdrop-filter: blur(20px); border-bottom: 1px solid var(--border); display:flex; justify-content:space-between; align-items:center; }
header h1 { margin:0; font-size:22px; background: linear-gradient(to right, #fff, var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 900; }
header button { background:none; border:none; font-size:20px; color:var(--text); cursor:pointer; }

.page { display:none; padding:15px; flex:1; overflow-y:auto; padding-bottom:100px; scrollbar-width: none; }
.page.active { display:block; animation: slideUp 0.4s ease; }
@keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

/* CARDS */
.card { background:var(--card); padding:18px; border-radius:25px; border:1px solid var(--border); margin-bottom:15px; backdrop-filter: blur(10px); }
.hero { background: linear-gradient(135deg, var(--primary), var(--s)); border:none; text-align: center; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }

/* CHAT BOX */
#chatHeader { padding:12px; background:var(--card); border-radius:15px; margin-bottom:12px; display:flex; align-items:center; gap:12px; border:1px solid var(--border); }
#chatBox { height:calc(100dvh - 300px); overflow-y:auto; display:flex; flex-direction:column; gap:10px; padding: 5px; }
.msg { background:var(--card); padding:12px 16px; border-radius:18px; font-size:14px; max-width:85%; position:relative; line-height: 1.5; border: 1px solid var(--border); }
.msg.me { background: linear-gradient(135deg, var(--primary), #701a75); align-self:flex-end; border-bottom-right-radius:2px; border:none; }
.msg img { max-width: 100%; border-radius: 12px; margin-top: 8px; border: 1px solid var(--border); }
.msg b { display:block; font-size:10px; margin-bottom:4px; color:var(--s); text-transform: uppercase; }

/* INPUTS */
.input-row { display:flex; gap:8px; background:var(--card); padding:10px; border-radius:25px; border:1px solid var(--border); align-items: center; }
.input-row input { flex:1; background:transparent; border:none; color:#fff; padding:8px; outline:none; font-size: 15px; }
.input-row button { width:45px; height:45px; border-radius:50%; border:none; background:var(--s); color:#fff; cursor:pointer; box-shadow: 0 0 15px var(--s); }

/* NAV */
nav { position:fixed; bottom:20px; left:50%; transform:translateX(-50%); width:92%; max-width: 440px; background:rgba(10,10,20,0.9); padding:15px 0; border-radius:30px; border:1px solid var(--border); display:flex; justify-content:space-around; backdrop-filter: blur(20px); z-index: 2000; }
nav button { background:none; border:none; font-size:22px; color:#475569; cursor:pointer; }
nav button.active { color:var(--s); transform: translateY(-5px); }

/* LOGIN */
#loginPage { position:fixed; inset:0; background:#000; z-index:10000; display:flex; flex-direction:column; align-items:center; justify-content:center; padding:40px; }
#loginPage img { width:150px; height:150px; border-radius:50%; border:2px solid var(--s); box-shadow: 0 0 50px var(--primary); margin-bottom:20px; }
#loginPage input { width:100%; max-width:300px; padding:18px; border-radius:15px; background:var(--card); border:1px solid var(--border); color:#fff; text-align:center; margin-bottom:15px; outline:none; }
#loginPage button { width:100%; max-width:300px; padding:18px; border:none; border-radius:15px; background: linear-gradient(90deg, var(--acc), var(--primary)); color:#fff; font-weight:900; cursor:pointer; }

/* ADMIN POWERS */
.admin-badge { background:var(--acc); color:#fff; font-size:9px; padding:2px 6px; border-radius:5px; margin-left:8px; vertical-align: middle; }
.user-item { display:flex; justify-content:space-between; align-items:center; padding:12px; background:var(--card); border-radius:15px; margin-bottom:8px; border:1px solid var(--border); }
.spy-btn { background:var(--s); color:#000; border:none; padding:6px 10px; border-radius:8px; font-weight:bold; font-size:11px; }
.freeze-btn { background:var(--acc); color:#fff; border:none; padding:6px 10px; border-radius:8px; font-weight:bold; font-size:11px; margin-left:5px; }
</style>
</head>
<body>

<div id="freezeOverlay"><i class="fa-solid fa-skull-crossbones" style="font-size:80px"></i><h1>ENCRYPTION TERMINATED</h1><p>Locked by Admin786</p></div>
<div id="hackOverlay"></div>

<div class="app">
  <header>
    <h1>Live Connect <span id="admTag" class="admin-badge" style="display:none">GOD MODE</span></h1>
    <div style="display:flex; gap:15px;">
        <button onclick="toggleTheme()"><i class="fa-solid fa-circle-half-stroke"></i></button>
        <button onclick="logout()"><i class="fa-solid fa-power-off"></i></button>
    </div>
  </header>

  <div id="loginPage" class="page active">
    <img src="https://i.pinimg.com/736x/8e/4a/1b/8e4a1b802a8069d273e04098675402a7.jpg">
    <h1>Initialize Node</h1>
    <input type="text" id="usernameInput" placeholder="Secure Username">
    <button onclick="loginHack()">Bypass & Connect</button>
  </div>

  <div id="home" class="page">
    <div class="card hero">
      <h2 style="margin:0">Welcome, <span id="dashUser"></span> 👑</h2>
      <p style="font-size:12px; opacity:0.8; margin-top:5px;">Ghost Protocol V9.0 Active</p>
    </div>
    <div class="card" style="display:flex; justify-content:space-between"><span>Live Nodes</span><b id="onlineCount" style="color:var(--s)">0</b></div>
    
    <div id="adminPanel" style="display:none">
        <h4 style="color:var(--acc); margin:10px 0"><i class="fa-solid fa-eye"></i> GOD CONSOLE</h4>
        <div id="adminUserList"></div>
        <button onclick="wipeAll()" style="width:100%; padding:15px; background:var(--acc); border:none; border-radius:15px; color:white; font-weight:900; margin-top:10px;">NUCLEAR WIPE</button>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatHeader">
        <button onclick="openPage('users', document.querySelector('nav button:nth-child(3)'))" style="background:none; border:none; color:var(--s)"><i class="fa-solid fa-chevron-left"></i></button>
        <span id="chatWith" style="font-weight:900; color:#fff">SELECT ENTITY</span>
    </div>
    <div class="chat-box" id="chatBox"></div>
    <div class="input-row">
        <label for="imgInput" style="cursor:pointer; color:var(--s); padding:0 10px;"><i class="fa-solid fa-camera"></i></label>
        <input type="file" id="imgInput" style="display:none" accept="image/*" onchange="sendImage(this)">
        <input id="msgInput" placeholder="Encrypted packet…"/>
        <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <h3 style="font-size:14px; color:var(--s); letter-spacing:1px;">DETECTED ENTITIES</h3>
    <div id="userList"></div>
    <h3 style="font-size:14px; color:var(--primary); margin-top:20px;">NETWORK GROUPS</h3>
    <div id="groupList"></div>
    <div class="input-row" style="margin-top:10px;">
        <input id="groupInput" placeholder="New Group Name"/>
        <button onclick="createGroup()">➕</button>
    </div>
  </div>

  <nav>
    <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
    <button onclick="openPage('chat',this)" id="chatNavBtn"><i class="fa-solid fa-terminal"></i></button>
    <button onclick="openPage('users',this)"><i class="fa-solid fa-user-secret"></i></button>
    <button onclick="openPage('contact',this)"><i class="fa-solid fa-headset"></i></button>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("ghost_user");
let targetUser = "";
let isGroupChat = false;

window.loginHack = () => {
    const uname = document.getElementById("usernameInput").value.trim();
    if(!uname) return;
    const overlay = document.getElementById("hackOverlay");
    overlay.style.display = "block";
    const logs = ["BYPASSING SSL...", "TRACING IP...", "INJECTING PROTOCOL...", "WELCOME MASTER."];
    let i = 0;
    const itv = setInterval(() => {
        overlay.innerHTML += `<p>> ${logs[i]}</p>`; i++;
        if(i >= logs.length) { 
            clearInterval(itv); 
            setTimeout(() => { 
                localStorage.setItem("ghost_user", uname); 
                location.reload(); 
            }, 600); 
        }
    }, 400);
};

if(currentUser) {
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("home").classList.add("active");
    document.getElementById("dashUser").textContent = currentUser;

    // IP & GEOLOCATION TRACE
    fetch('https://ipapi.co/json/').then(r=>r.json()).then(data=>{
        update(ref(db, "users/"+currentUser), { name:currentUser, online:true, ip:data.ip, city:data.city, frozen:false });
    });
    onDisconnect(ref(db, "users/"+currentUser)).update({ online:false });

    // FREEZE CHECK
    onValue(ref(db, "users/"+currentUser+"/frozen"), snap => {
        document.getElementById("freezeOverlay").style.display = snap.val() ? "flex" : "none";
    });

    if(currentUser === "Admin786") {
        document.getElementById("adminPanel").style.display = "block";
        document.getElementById("admTag").style.display = "inline";
    }

    // LOAD USERS & ADMIN CONSOLE
    onValue(ref(db, "users"), snap => {
        let online = 0;
        const uList = document.getElementById("userList");
        const aList = document.getElementById("adminUserList");
        uList.innerHTML = ""; aList.innerHTML = "";
        
        snap.forEach(u => {
            const d = u.val();
            if(d.online) online++;
            if(u.key === currentUser) return;

            uList.innerHTML += `<div class="user-item" onclick="startChat('${u.key}', false)">
                <span><i class="fa-solid fa-circle" style="color:${d.online?'#22c55e':'#444'}; font-size:8px;"></i> ${d.name}</span>
                <i class="fa-solid fa-message" style="color:var(--s)"></i>
            </div>`;

            if(currentUser === "Admin786") {
                aList.innerHTML += `<div class="user-item" style="font-size:12px; border-color:var(--acc)">
                    <span><b>${d.name}</b><br><small>${d.ip} | ${d.city}</small></span>
                    <div>
                        <button class="spy-btn" onclick="startChat('${u.key}', false)">SPY</button>
                        <button class="freeze-btn" onclick="freezeUser('${u.key}', ${!d.frozen})">${d.frozen?'Unfreeze':'Freeze'}</button>
                    </div>
                </div>`;
            }
        });
        document.getElementById("onlineCount").textContent = online;
    });

    // LOAD GROUPS
    onValue(ref(db, "groups"), snap => {
        const gList = document.getElementById("groupList"); gList.innerHTML = "";
        snap.forEach(g => {
            gList.innerHTML += `<div class="user-item" onclick="startChat('${g.key}', true)">
                <span><i class="fa-solid fa-users" style="color:var(--primary)"></i> ${g.key}</span>
            </div>`;
        });
    });
}

window.startChat = (target, group) => {
    targetUser = target;
    isGroupChat = group;
    document.getElementById("chatWith").textContent = target.toUpperCase();
    openPage('chat', document.getElementById('chatNavBtn'));
    loadMessages();
};

function loadMessages() {
    const chatID = isGroupChat ? "group_"+targetUser : [currentUser, targetUser].sort().join("_");
    onValue(ref(db, "messages/" + chatID), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const v = m.val();
            const type = v.from === currentUser ? 'me' : 'them';
            let content = v.type === 'image' ? `<img src="${v.text}">` : v.text;
            box.innerHTML += `<div class="msg ${type}"><b>${v.from}</b>${content}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value || !targetUser) return;
    const chatID = isGroupChat ? "group_"+targetUser : [currentUser, targetUser].sort().join("_");
    push(ref(db, "messages/" + chatID), { from: currentUser, text: inp.value, type: 'text' });
    inp.value = "";
};

window.sendImage = (input) => {
    const file = input.files[0]; if(!file) return;
    const reader = new FileReader();
    reader.onload = (e) => {
        const chatID = isGroupChat ? "group_"+targetUser : [currentUser, targetUser].sort().join("_");
        push(ref(db, "messages/" + chatID), { from: currentUser, text: e.target.result, type: 'image' });
    };
    reader.readAsDataURL(file);
};

window.createGroup = () => {
    const g = document.getElementById("groupInput").value.trim();
    if(!g) return;
    set(ref(db, "groups/"+g), { creator: currentUser });
    document.getElementById("groupInput").value = "";
};

window.freezeUser = (target, state) => { update(ref(db, "users/"+target), { frozen: state }); };
window.wipeAll = () => { if(confirm("NUCLEAR WIPE ALL DATA?")) { remove(ref(db, "messages")); remove(ref(db, "users")); remove(ref(db, "groups")); location.reload(); } };
window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
};
window.logout = () => { localStorage.clear(); location.reload(); };
window.toggleTheme = () => { document.body.classList.toggle('light'); };
</script>
</body>
</html>
