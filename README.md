<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect | Pro Edition</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#050505;
  --card: rgba(255, 255, 255, 0.03);
  --border: rgba(255, 255, 255, 0.1);
  --primary:#00d2ff;
  --accent:#9d50bb;
  --danger:#ff0055;
  --success:#00ff41;
}
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
body{
    margin:0; font-family:'Inter', sans-serif; background:var(--bg); color:#fff;
    background: radial-gradient(circle at center, #1a1a2e 0%, #050505 100%);
    height: 100vh; overflow: hidden;
}
.app{max-width:480px; margin:auto; height:100vh; display:flex; flex-direction:column; position:relative; border-left: 1px solid var(--border); border-right: 1px solid var(--border);}

header{
    padding: 20px; display:flex; justify-content:space-between; align-items:center;
    background:rgba(0,0,0,0.5); backdrop-filter: blur(10px); border-bottom: 1px solid var(--border);
}
header h1{ margin:0; font-size:18px; letter-spacing: 2px; font-weight: 800; background: linear-gradient(to right, var(--primary), var(--accent)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

/* Admin Floating Action */
#adminTrigger { display:none; position:fixed; right:20px; bottom:100px; background:gold; color:#000; width:50px; height:50px; border-radius:50%; display:none; align-items:center; justify-content:center; box-shadow:0 0 20px gold; cursor:pointer; z-index:1001; }

.page{display:none; padding:20px; flex:1; overflow-y:auto; animation: slideIn 0.3s ease; }
.page.active{display:flex; flex-direction:column;}
@keyframes slideIn { from{opacity:0; transform:translateY(10px);} to{opacity:1; transform:translateY(0);} }

.hero{
    background: var(--card); border: 1px solid var(--border); padding:25px; border-radius:24px; text-align:center;
    box-shadow: 0 10px 30px rgba(0,0,0,0.5); margin-bottom:20px;
}
.dashboard-stat{
    background:var(--card); padding:15px 20px; margin-bottom:12px; border-radius:20px;
    border: 1px solid var(--border); display:flex; justify-content:space-between; align-items:center;
}
.dashboard-stat h4{margin:0; color:var(--primary); font-size:14px;}

.chat-box{ flex:1; overflow-y:auto; padding:10px; margin-bottom:15px; display:flex; flex-direction:column; gap:10px;}
.msg{
    background:var(--card); border: 1px solid var(--border); padding:12px 16px; border-radius:20px;
    max-width:85%; font-size:14px; align-self: flex-start;
}
.msg.me { align-self: flex-end; background: linear-gradient(135deg, var(--primary), var(--accent)); border:none; }
.msg b { display:block; font-size:10px; margin-bottom:4px; opacity:0.7; }

.input-row{ display:flex; gap:10px; padding:10px 0; }
.input-row input{
    flex:1; padding:15px; border-radius:15px; border:1px solid var(--border);
    background:rgba(255,255,255,0.05); color:#fff;
}
.input-row button{
    width:50px; height:50px; border:none; border-radius:15px;
    background:var(--primary); color:#000; cursor:pointer; font-size:18px;
}

/* User & Group Cards */
.user, .group{
    background:var(--card); padding:15px; border-radius:18px; margin-bottom:10px;
    border: 1px solid var(--border); display:flex; align-items:center; gap:12px;
}
.dot{ width:10px; height:10px; background:var(--success); border-radius:50%; box-shadow:0 0 8px var(--success);}

nav{
    display:flex; justify-content:space-around; background:rgba(0,0,0,0.8);
    backdrop-filter: blur(20px); padding:15px 0; border-top: 1px solid var(--border);
}
nav button{ background:none; border:none; font-size:20px; color:var(--muted); cursor:pointer; opacity:0.4; }
nav button.active{ color:var(--primary); opacity:1; transform: translateY(-5px); }

/* Trap Overlay */
#trapScreen { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:9999; flex-direction:column; align-items:center; justify-content:center; text-align:center; }
</style>
</head>
<body>

<div id="trapScreen">
    <i class="fa-solid fa-triangle-exclamation" style="font-size:60px; color:var(--danger)"></i>
    <h2 style="color:#fff">ACCESS DENIED</h2>
    <p style="color:var(--success)">SYSTEM_OVERRIDE_BY_NAZIM</p>
</div>

<div id="adminTrigger" onclick="toggleHackerPanel()"><i class="fa-solid fa-wand-magic-sparkles"></i></div>

<div class="app">
<header>
    <h1>LIVE_CONNECT.SYS</h1>
    <div style="display:flex; gap:15px;">
        <button onclick="logout()" style="background:none; border:none; color:var(--danger); cursor:pointer;"><i class="fa-solid fa-power-off"></i></button>
    </div>
</header>

<div id="loginPage" class="page active">
    <div style="text-align:center; margin-top:100px;">
        <i class="fa-solid fa-fingerprint" style="font-size:60px; color:var(--primary); margin-bottom:20px;"></i>
        <h3>IDENTITY AUTH</h3>
        <input type="text" id="usernameInput" placeholder="Enter Codename" style="width:100%; padding:18px; border-radius:18px; background:rgba(255,255,255,0.05); border:1px solid var(--border); color:#fff; text-align:center;">
        <button onclick="login()" style="width:100%; padding:18px; border-radius:18px; border:none; background:var(--primary); color:#000; font-weight:bold; margin-top:20px; cursor:pointer;">BOOT SYSTEM</button>
    </div>
</div>

<div id="home" class="page">
    <div class="hero">
        <h2 id="dashUser">Welcome Agent</h2>
        <div style="font-size:12px; color:var(--success); margin-top:5px;"><i class="fa-solid fa-shield-halved"></i> GRID STATUS: SECURE</div>
    </div>
    <div class="dashboard-stat"><h4>NODES ONLINE</h4><span id="onlineCount">0</span></div>
    <div class="dashboard-stat"><h4>ACTIVE GRIDS</h4><span id="groupCount">0</span></div>
</div>

<div id="chat" class="page">
    <div id="hackerTools" style="display:none; background:rgba(255,0,0,0.1); padding:10px; border-radius:15px; margin-bottom:10px; border:1px solid var(--danger); grid-template-columns: repeat(3,1fr); gap:5px;">
        <button onclick="setTrap('freeze')" style="font-size:10px; padding:8px; background:#000; color:red; border:1px solid red;">FREEZE</button>
        <button onclick="setTrap('scare')" style="font-size:10px; padding:8px; background:#000; color:red; border:1px solid red;">MATRIX</button>
        <button onclick="setTrap('restore')" style="font-size:10px; padding:8px; background:#000; color:lime; border:1px solid lime;">RESTORE</button>
    </div>
    <div class="chat-box" id="chatBox"></div>
    <div class="input-row">
        <input id="msgInput" placeholder="Type signal..."/>
        <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div id="users" class="page">
    <h3 style="font-size:14px; opacity:0.6;">ONLINE_TARGETS</h3>
    <div id="userList"></div>
</div>

<nav>
    <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-home"></i></button>
    <button onclick="openPage('chat',this)"><i class="fa-solid fa-message"></i></button>
    <button onclick="openPage('users',this)"><i class="fa-solid fa-ghost"></i></button>
    <button onclick="openPage('contact',this)"><i class="fa-solid fa-headset"></i></button>
</nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
let currentUser=null;

window.login=()=>{
    const uname=document.getElementById("usernameInput").value.trim();
    if(!uname) return;
    currentUser=uname;
    document.getElementById("dashUser").textContent= "Welcome, " + currentUser;
    
    if(currentUser === "Nazim") document.getElementById("adminTrigger").style.display="flex";
    
    const userRef=ref(db,"users/"+uname);
    set(userRef,{name:uname,online:true});
    onDisconnect(userRef).update({online:false});

    // Listen for Traps
    onValue(ref(db, "traps/"+currentUser), snap => {
        const t = snap.val();
        if(t === "freeze" || t === "scare") document.getElementById("trapScreen").style.display="flex";
        if(t === "restore") document.getElementById("trapScreen").style.display="none";
    });

    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("home").classList.add("active");
};

window.openPage=(id,btn)=>{
    document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');
};

window.toggleHackerPanel = () => {
    const p = document.getElementById("hackerTools");
    p.style.display = p.style.display === "none" ? "grid" : "none";
};

window.setTrap = (type) => {
    onValue(ref(db, "users"), snap => {
        snap.forEach(u => {
            if(u.key !== "Nazim") set(ref(db, "traps/"+u.key), type);
        });
    }, {onlyOnce: true});
};

onValue(ref(db,"users"),snap=>{
    let count = 0;
    const list = document.getElementById("userList");
    list.innerHTML = "";
    snap.forEach(u => {
        if(u.val().online){
            count++;
            if(u.key !== currentUser){
                let d = document.createElement("div");
                d.className = "user";
                d.innerHTML = `<div class="dot"></div> ${u.key}`;
                d.onclick = () => openPage('chat', document.querySelector('nav button:nth-child(2)'));
                list.appendChild(d);
            }
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

window.sendMsg=()=>{
    const input=document.getElementById('msgInput');
    if(!input.value) return;
    push(ref(db,"global_chat"), {from:currentUser, text:input.value});
    input.value='';
};

onValue(ref(db,"global_chat"), snap => {
    const box = document.getElementById("chatBox");
    box.innerHTML = "";
    snap.forEach(m => {
        const div = document.createElement("div");
        div.className = "msg " + (m.val().from === currentUser ? "me" : "");
        div.innerHTML = `<b>${m.val().from}</b> ${m.val().text}`;
        box.appendChild(div);
    });
    box.scrollTop = box.scrollHeight;
});

window.logout=()=> location.reload();
</script>
</body>
</html>
