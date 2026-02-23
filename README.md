<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀 | Ultimate Console</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#050505;
  --card:#121212;
  --text:#e5e5e5;
  --muted:#888;
  --primary:#6b5bff;
  --accent:#ff5c8d;
  --success:#22c55e;
  --danger:#ef4444;
  --glass: rgba(255, 255, 255, 0.03);
}
body{margin:0;font-family:'Inter', system-ui, -apple-system;background:var(--bg);color:var(--text);transition:.3s; overflow-x:hidden;}
body.light{--bg:#f8f9fa;--card:#fff;--text:#111;--muted:#555;--glass:rgba(0,0,0,0.05);}

/* FREEZE OVERLAY */
#freezeOverlay { display:none; position:fixed; inset:0; background:rgba(0,0,0,0.9); z-index:10000; flex-direction:column; align-items:center; justify-content:center; backdrop-filter:blur(20px); color:var(--danger); }

.app{max-width:480px;margin:auto;min-height:100vh;display:flex;flex-direction:column; position:relative; border-left:1px solid rgba(255,255,255,0.05); border-right:1px solid rgba(255,255,255,0.05);}
header{padding:50px 16px 15px;display:flex;justify-content:space-between;align-items:center;background:var(--card);backdrop-filter:blur(10px); position:sticky; top:0; z-index:100;}
header h1{margin:0;font-size:20px;font-weight:900;background:linear-gradient(to right, var(--primary), var(--accent));-webkit-background-clip:text;-webkit-text-fill-color:transparent;}

.page{display:none;padding:16px;flex:1;animation:slideUp 0.4s ease;}
.page.active{display:block;}
@keyframes slideUp { from{opacity:0; transform:translateY(10px);} to{opacity:1; transform:translateY(0);} }

.hero{background:linear-gradient(135deg, var(--accent), var(--primary));color:#fff;padding:30px;border-radius:24px;text-align:center;box-shadow:0 10px 30px rgba(107,91,255,0.3);margin-bottom:20px;}
.hero h2{margin:0; font-size:24px; letter-spacing:-0.5px;}

.dashboard-stat{background:var(--card);padding:18px;margin-bottom:12px;border-radius:18px;border:1px solid var(--glass);display:flex;justify-content:space-between;align-items:center; transition:.3s;}
.dashboard-stat:hover{border-color:var(--primary); transform:scale(1.02);}

/* ADMIN PANEL UI */
.admin-card{background:rgba(239, 68, 68, 0.05); border:1px solid rgba(239, 68, 68, 0.2); padding:15px; border-radius:20px; margin-top:20px;}
.user-row{display:flex; justify-content:space-between; align-items:center; padding:10px; border-bottom:1px solid var(--glass);}
.admin-btn{padding:6px 10px; border-radius:8px; border:none; font-size:11px; cursor:pointer; margin-left:5px; font-weight:bold;}

/* CHAT AREA */
.chat-box{background:var(--card);border-radius:22px;padding:15px;height:55vh;overflow-y:auto;margin-bottom:15px; border:1px solid var(--glass); display:flex; flex-direction:column; gap:10px;}
.msg{background:rgba(255,255,255,0.05); padding:12px 16px; border-radius:18px; max-width:85%; position:relative; align-self:flex-start; font-size:14px; line-height:1.5;}
.msg.me{background:var(--primary); align-self:flex-end; color:white; border-bottom-right-radius:4px;}
.msg b{display:block; font-size:10px; margin-bottom:4px; opacity:0.7; text-transform:uppercase;}

.input-row{display:flex;gap:8px; background:var(--card); padding:8px; border-radius:20px; border:1px solid var(--glass);}
.input-row input{flex:1;padding:12px;border:none;background:transparent;color:var(--text);outline:none;}
.input-row button{width:45px; height:45px; border-radius:15px; border:none; background:var(--primary); color:#fff; cursor:pointer;}

nav{display:flex;justify-content:space-around;background:var(--card);padding:15px 0; position:fixed; bottom:0; width:100%; max-width:480px; border-top:1px solid var(--glass); backdrop-filter:blur(15px);}
nav button{background:none;border:none;font-size:20px;color:var(--muted);transition:.3s;}
nav button.active{color:var(--primary); transform:translateY(-5px);}

.contact-card{background:var(--card); padding:15px; border-radius:18px; margin-bottom:10px; display:flex; align-items:center; gap:15px; border:1px solid var(--glass);}
.contact-card i{font-size:22px; color:var(--primary);}
</style>
</head>
<body>

<div id="freezeOverlay">
    <i class="fa-solid fa-hand" style="font-size:50px"></i>
    <h2>ACCESS DENIED</h2>
    <p>Your node is frozen by Admin786</p>
</div>

<div class="app">
<header>
    <h1>Live Connect <span id="adminBadge" style="display:none; font-size:10px; background:var(--danger); color:white; padding:2px 6px; border-radius:5px; margin-left:5px;">ADMIN</span></h1>
    <div style="display:flex; gap:15px;">
        <button onclick="toggleTheme()"><i class="fa-solid fa-circle-half-stroke"></i></button>
        <button onclick="logout()"><i class="fa-solid fa-power-off"></i></button>
    </div>
</header>

<div id="loginPage" class="page active">
    <div style="text-align:center; padding:40px 20px;">
        <i class="fa-solid fa-rocket" style="font-size:60px; color:var(--primary); margin-bottom:20px;"></i>
        <h2 style="margin:0;">Initialize Session</h2>
        <p style="color:var(--muted); font-size:14px;">Enter your unique identity</p>
    </div>
    <div class="login-card">
        <input type="text" id="usernameInput" placeholder="Enter Username...">
        <button onclick="login()">Enter Matrix</button>
    </div>
</div>

<div id="home" class="page">
    <div class="hero">
        <h2>Hi, <span id="dashUser"></span>! 👋</h2>
        <p>Your connection is secured with AES-256</p>
    </div>
    <div class="dashboard-stat"><h4>Online Entities</h4><span id="onlineCount">0</span></div>
    
    <div id="adminPanel" style="display:none;" class="admin-card">
        <h4 style="margin:0 0 10px; color:var(--danger); font-size:12px;"><i class="fa-solid fa-skull"></i> MASTER CONTROL PANEL</h4>
        <div id="fullUserList"></div>
        <button onclick="wipeAllData()" style="width:100%; padding:12px; background:var(--danger); border:none; border-radius:12px; color:white; font-weight:bold; margin-top:10px;">NUCLEAR WIPE</button>
    </div>
</div>

<div id="chat" class="page">
    <div style="padding:0 0 10px; border-bottom:1px solid var(--glass); margin-bottom:10px; display:flex; align-items:center; gap:10px;">
        <button onclick="openPage('users', document.querySelector('nav button:nth-child(3)'))" style="background:none; border:none; color:var(--primary);"><i class="fa-solid fa-arrow-left"></i></button>
        <b id="chatHeaderTitle" style="font-size:14px;">Select Target</b>
    </div>
    <div class="chat-box" id="chatBox"></div>
    <div class="input-row">
        <input id="msgInput" placeholder="Enter message packet..."/>
        <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div id="users" class="page">
    <h3 style="font-size:14px; color:var(--primary); letter-spacing:1px;">ACTIVE NODES</h3>
    <div id="userList"></div>
    
    <h3 style="font-size:14px; color:var(--accent); letter-spacing:1px; margin-top:20px;">GROUPS</h3>
    <div id="groupList"></div>
    <div class="input-row" style="margin-top:10px;">
        <input id="groupInput" placeholder="New Group Name..."/>
        <button onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
    </div>
</div>

<div id="about" class="page">
    <h3>About Live Connect 🚀</h3>
    <div class="contact-card">Developed by WebHub Technologies</div>
    <p style="font-size:14px; color:var(--muted); line-height:1.6;">A leading provider of real-time communication apps for private and professional networks.</p>
</div>

<div id="contact" class="page">
    <h3>Contact Support</h3>
    <div class="contact-card"><i class="fa-solid fa-envelope"></i> <div><b>Email</b><br><small>webhub262@gmail.com</small></div></div>
    <div class="contact-card"><i class="fa-brands fa-facebook"></i> <div><b>Facebook</b><br><small>WebHub Official</small></div></div>
    <div class="contact-card"><i class="fa-brands fa-instagram"></i> <div><b>Instagram</b><br><small>@mr_nazim073</small></div></div>
</div>

<nav>
    <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
    <button onclick="openPage('chat',this)"><i class="fa-solid fa-terminal"></i></button>
    <button onclick="openPage('users',this)"><i class="fa-solid fa-ghost"></i></button>
    <button onclick="openPage('about',this)"><i class="fa-solid fa-info-circle"></i></button>
    <button onclick="openPage('contact',this)"><i class="fa-solid fa-headset"></i></button>
</nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.appspot.com",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("lc_user");
let curChat = "";
let isGroup = false;

if(currentUser) {
    autoLogin(currentUser);
}

window.login = () => {
  const uname = document.getElementById("usernameInput").value.trim();
  if(!uname) return;
  localStorage.setItem("lc_user", uname);
  location.reload();
};

function autoLogin(uname) {
  currentUser = uname;
  document.getElementById("dashUser").textContent = currentUser;
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
  document.querySelector('nav button:first-child').classList.add('active');

  // TRACE IP & UPDATE STATUS
  fetch('https://ipapi.co/json/').then(r=>r.json()).then(d=>{
      update(ref(db,"users/"+uname), { name:uname, online:true, ip:d.ip, city:d.city, frozen:false });
  });

  onDisconnect(ref(db,"users/"+uname)).update({online:false});

  // ADMIN CHECK
  if(uname === "Admin786") {
      document.getElementById("adminPanel").style.display = "block";
      document.getElementById("adminBadge").style.display = "inline";
  }

  // FREEZE CHECK
  onValue(ref(db, "users/"+uname+"/frozen"), snap => {
      if(snap.val() === true) document.getElementById("freezeOverlay").style.display = "flex";
  });
}

// RENDER USERS & ADMIN CONTROLS
onValue(ref(db,"users"),snap=>{
  let online = 0;
  document.getElementById("userList").innerHTML = "";
  document.getElementById("fullUserList").innerHTML = "";
  
  snap.forEach(u=>{
    const d = u.val();
    if(d.online) online++;
    
    // User List
    if(u.key !== currentUser) {
        const div = document.createElement("div");
        div.className = "user";
        div.innerHTML = `<div class="dot" style="background:${d.online?'var(--success)':'#444'}"></div> ${u.key}`;
        div.onclick = () => startChat(u.key, false);
        document.getElementById("userList").appendChild(div);
    }

    // Admin Control Row
    if(currentUser === "Admin786" && u.key !== "Admin786") {
        const row = document.createElement("div");
        row.className = "user-row";
        row.innerHTML = `
            <span style="font-size:12px;"><b>${u.key}</b><br><small>${d.ip || 'no-ip'}</small></span>
            <div>
                <button class="admin-btn" style="background:var(--primary); color:white;" onclick="startChat('${u.key}', false)">SPY</button>
                <button class="admin-btn" style="background:var(--danger); color:white;" onclick="toggleFreeze('${u.key}', ${!d.frozen})">${d.frozen?'Unfreeze':'Freeze'}</button>
            </div>
        `;
        document.getElementById("fullUserList").appendChild(row);
    }
  });
  document.getElementById("onlineCount").textContent = online;
});

// GROUP LIST
onValue(ref(db,"groups"),snap=>{
  document.getElementById("groupList").innerHTML = "";
  snap.forEach(g=>{
    const div = document.createElement("div");
    div.className = "group";
    div.innerHTML = `<i class="fa-solid fa-users" style="margin-right:10px; color:var(--primary)"></i> ${g.key}`;
    div.onclick = () => startChat(g.key, true);
    document.getElementById("groupList").appendChild(div);
  });
});

window.startChat = (id, group) => {
    curChat = id; isGroup = group;
    document.getElementById("chatHeaderTitle").textContent = id.toUpperCase();
    loadChat();
    openPage('chat', document.querySelector('nav button:nth-child(2)'));
};

function loadChat(){
  const path = isGroup ? "groups/"+curChat+"/msgs" : "chats/"+ [currentUser,curChat].sort().join("_");
  onValue(ref(db,path),snap=>{
    const box = document.getElementById("chatBox");
    box.innerHTML = "";
    snap.forEach(m=>{
      const d = m.val();
      const isMe = d.from === currentUser;
      const msgDiv = document.createElement("div");
      msgDiv.className = `msg ${isMe?'me':''}`;
      
      // Admin Delete Power
      let delBtn = (currentUser === "Admin786") ? `<span style="float:right; cursor:pointer; margin-left:10px; opacity:0.5;" onclick="deleteMsg('${path}','${m.key}')">🗑️</span>` : "";
      
      msgDiv.innerHTML = `<b>${d.from}</b> ${delBtn} ${d.text}`;
      box.appendChild(msgDiv);
    });
    box.scrollTop = box.scrollHeight;
  });
}

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value || !curChat) return;
    const path = isGroup ? "groups/"+curChat+"/msgs" : "chats/"+ [currentUser,curChat].sort().join("_");
    push(ref(db,path), { from:currentUser, text:inp.value, time:Date.now() });
    inp.value = "";
};

// ADMIN POWERS
window.toggleFreeze = (target, status) => { update(ref(db, "users/"+target), { frozen: status }); };
window.wipeAllData = () => { if(confirm("Destroy all messages?")) { remove(ref(db, "chats")); remove(ref(db, "groups")); location.reload(); }};
window.deleteMsg = (path, key) => { remove(ref(db, path+"/"+key)); };

window.logout = () => { localStorage.clear(); location.reload(); };
window.openPage = (id, btn) => {
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};
window.toggleTheme = () => { document.body.classList.toggle('light'); };
window.createGroup = () => {
  const g = document.getElementById("groupInput").value.trim();
  if(!g) return;
  set(ref(db,"groups/"+g), {created: Date.now()});
  document.getElementById("groupInput").value='';
};
</script>
</body>
</html>
