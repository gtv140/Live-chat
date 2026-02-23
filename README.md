<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#08090a;
  --card:#14161a;
  --text:#f0f2f5;
  --muted:#94a3b8;
  --primary:#6366f1;
  --accent:#f43f5e;
  --success:#10b981;
  --glass: rgba(255, 255, 255, 0.03);
}
body{margin:0;font-family:'Inter', system-ui, -apple-system;background:var(--bg);color:var(--text);transition:.3s; height: 100dvh; display: flex; flex-direction: column; overflow: hidden;}
body.light{--bg:#f8fafc;--card:#ffffff;--text:#1e293b;--muted:#64748b;--glass: rgba(0,0,0,0.03);}

.app{max-width:480px;margin:auto;height:100%;display:flex;flex-direction:column; width: 100%; box-shadow: 0 0 50px rgba(0,0,0,0.5);}

header{padding:16px 20px;display:flex;justify-content:space-between;align-items:center;background:var(--card);border-bottom: 1px solid rgba(255,255,255,0.05); z-index: 10;}
header h1{margin:0;font-size:22px;font-weight:800; background: linear-gradient(90deg, var(--primary), var(--accent)); -webkit-background-clip: text; -webkit-text-fill-color: transparent;}
header button{background:var(--glass); border:1px solid rgba(255,255,255,0.1); width:40px; height:40px; border-radius:12px; color:var(--text); cursor:pointer; transition:.3s;}

.page{display:none;padding:20px;flex:1;overflow-y:auto; transition: 0.3s ease-in-out;}
.page.active{display:block; animation: fadeIn 0.4s ease;}
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

.hero{background:linear-gradient(135deg, var(--primary), var(--accent)); color:#fff; padding:32px 20px; border-radius:24px; text-align:center; box-shadow:0 20px 40px rgba(99, 102, 241, 0.2); margin-bottom:24px;}
.hero h2{margin:0 0 8px; font-size:24px; font-weight:800;}
.hero p{margin:0; font-size:14px; opacity:.85; letter-spacing: 0.5px;}

.dashboard-stat{background:var(--card); padding:20px; margin-bottom:16px; border-radius:20px; border: 1px solid rgba(255,255,255,0.05); display:flex; justify-content:space-between; align-items:center; transition:.3s;}
.dashboard-stat h4{margin:0; color:var(--muted); font-size:14px; font-weight:500; text-transform: uppercase; letter-spacing: 1px;}
.dashboard-stat span{font-weight:800; font-size:20px; color:var(--primary);}

.chat-box{background:var(--card); border-radius:24px; padding:16px; height:50vh; overflow-y:auto; margin-bottom:16px; border: 1px solid rgba(255,255,255,0.05); scroll-behavior: smooth;}
.msg{background:#1e293b; color:#fff; padding:12px 16px; border-radius:18px; border-bottom-left-radius: 4px; margin-bottom:12px; font-size:14px; position:relative; line-height: 1.5; max-width: 85%;}
.msg b{color: var(--primary); font-size: 12px; display: block; margin-bottom: 4px;}

.input-row{display:flex; gap:12px; background: var(--card); padding: 8px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.1);}
.input-row input{flex:1; padding:12px 16px; border:none; background:transparent; color:var(--text); outline:none; font-size: 15px;}
.input-row button{width: 48px; height: 48px; border:none; border-radius:16px; background:var(--primary); color:#fff; cursor:pointer; transition:.3s; display: grid; place-items: center;}
.input-row button:hover{transform: scale(1.05); box-shadow: 0 0 20px var(--primary);}

.user, .group{display:flex; align-items:center; gap:14px; margin-bottom:12px; padding:16px; background:var(--card); border-radius:20px; border: 1px solid rgba(255,255,255,0.05); transition:.3s;}
.user:hover{border-color: var(--primary); background: rgba(99, 102, 241, 0.05);}
.dot{width:10px; height:10px; background:var(--success); border-radius:50%; box-shadow: 0 0 10px var(--success);}

nav{display:flex; justify-content:space-around; background:var(--card); padding:16px 0; border-top: 1px solid rgba(255,255,255,0.05); padding-bottom: env(safe-area-inset-bottom);}
nav button{background:none; border:none; font-size:22px; color:var(--muted); cursor:pointer; transition:.3s; position: relative;}
nav button.active{color:var(--primary);}
nav button.active::after{content:''; position:absolute; bottom:-10px; left:50%; transform:translateX(-50%); width:5px; height:5px; background:var(--primary); border-radius:50%;}

.login-card{background:var(--card); padding:32px 24px; border-radius:28px; text-align: center; border: 1px solid rgba(255,255,255,0.05);}
.login-card h3{margin-top:0; font-size:22px;}
.login-card input{width:100%; padding:16px; border-radius:16px; margin-bottom:16px; border:1px solid rgba(255,255,255,0.1); background:rgba(0,0,0,0.2); color:#fff; outline:none; font-size: 16px;}
.login-card button{width:100%; padding:16px; border:none; border-radius:16px; background:var(--primary); color:#fff; font-weight:700; font-size:16px; cursor:pointer;}

.comment-input input{background: rgba(0,0,0,0.2); border: 1px solid rgba(255,255,255,0.05); padding: 10px; border-radius: 12px;}
.contact-card{background:var(--card); border: 1px solid rgba(255,255,255,0.05); padding: 16px; border-radius: 20px;}
</style>
</head>
<body>
<div class="app">
<header>
<h1>Live Connect</h1>
<div>
    <button onclick="toggleTheme()" style="margin-right:8px;"><i class="fa-solid fa-circle-half-stroke"></i></button>
    <button onclick="logout()"><i class="fa-solid fa-power-off"></i></button>
</div>
</header>

<div id="loginPage" class="page active">
    <div style="height:100%; display:flex; align-items:center; justify-content:center;">
        <div class="login-card">
            <div style="font-size:40px; margin-bottom:15px;">🛰️</div>
            <h3>Terminal Access</h3>
            <input type="text" id="usernameInput" placeholder="Enter Username">
            <button onclick="login()">Initialize Sync</button>
        </div>
    </div>
</div>

<div id="home" class="page">
    <div class="hero">
        <h2>Hi, <span id="dashUser"></span>!</h2>
        <p>Your secure communication hub is ready.</p>
    </div>
    <div class="dashboard-stat"><h4>Online Nodes</h4><span id="onlineCount">0</span></div>
    <div class="dashboard-stat"><h4>Active Clusters</h4><span id="groupCount">0</span></div>
</div>

<div id="chat" class="page">
    <div class="chat-box" id="chatBox"></div>
    <div class="input-row">
        <input id="msgInput" placeholder="Message…"/>
        <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div id="users" class="page">
    <h3 style="font-size:14px; color:var(--muted); text-transform:uppercase; margin-bottom:15px;">Network Users</h3>
    <div id="userList"></div>
    <h3 style="font-size:14px; color:var(--muted); text-transform:uppercase; margin-top:24px; margin-bottom:15px;">Groups</h3>
    <div id="groupList"></div>
    <div class="input-row" style="margin-top:20px;">
        <input id="groupInput" placeholder="New group name"/>
        <button onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
    </div>
</div>

<div id="about" class="page">
    <div class="contact-card" style="margin-top:10px;">
        <h3>About System 🚀</h3>
        <p style="font-size:14px; line-height:1.6; color:var(--muted);">Developed by <b>WebHub Technologies</b>. A robust architecture for real-time data streaming and private encrypted messaging.</p>
        <ul style="padding-left:20px; font-size:14px; color:var(--muted);">
            <li>Low-latency synchronization</li>
            <li>Hybrid Dark/Light architecture</li>
            <li>Fully responsive mobile experience</li>
        </ul>
    </div>
</div>

<div id="contact" class="page">
    <h3 style="margin-bottom:20px;">Support Channels</h3>
    <div class="contact-card" style="display:flex; align-items:center; gap:15px; margin-bottom:12px;">
        <i class="fa-solid fa-envelope" style="color:var(--primary); font-size:20px;"></i>
        <div><h4 style="margin:0; font-size:14px;">Support Email</h4><a href="mailto:webhub262@gmail.com" style="color:var(--muted); text-decoration:none; font-size:13px;">webhub262@gmail.com</a></div>
    </div>
    <div class="contact-card" style="display:flex; align-items:center; gap:15px; margin-bottom:12px;">
        <i class="fa-brands fa-instagram" style="color:var(--accent); font-size:20px;"></i>
        <div><h4 style="margin:0; font-size:14px;">Instagram</h4><a href="#" style="color:var(--muted); text-decoration:none; font-size:13px;">@mr_nazim073</a></div>
    </div>
</div>

<nav>
    <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house-user"></i></button>
    <button onclick="openPage('chat',this)"><i class="fa-solid fa-comment-dots"></i></button>
    <button onclick="openPage('users',this)"><i class="fa-solid fa-users-viewfinder"></i></button>
    <button onclick="openPage('about',this)"><i class="fa-solid fa-shield-halved"></i></button>
    <button onclick="openPage('contact',this)"><i class="fa-solid fa-headset"></i></button>
</nav>
</div>

<script type="module">
// --- SAARA JAVASCRIPT LOGIC WAHI HAI JO AAPNE BHEJA THA ---
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let currentUser=null;
let curChat="";
let isGroup=false;

const chatBox=document.getElementById("chatBox");
const userList=document.getElementById("userList");
const groupList=document.getElementById("groupList");
const dashUser=document.getElementById("dashUser");
const onlineCount=document.getElementById("onlineCount");
const groupCount=document.getElementById("groupCount");

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser=uname;
  dashUser.textContent=currentUser;
  const userRef=ref(db,"users/"+uname);
  set(userRef,{name:uname,online:true});
  onDisconnect(userRef).update({online:false});
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

window.logout=()=>{
  if(!currentUser) return;
  const userRef=ref(db,"users/"+currentUser);
  set(userRef,{name:currentUser,online:false});
  location.reload();
};

window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

window.toggleTheme=()=>{document.body.classList.toggle('light');};

onValue(ref(db,"users"),snap=>{
  let online=0;
  userList.innerHTML="";
  snap.forEach(u=>{
    if(u.val().online){
      online++;
      if(u.key!==currentUser){
        const d=document.createElement("div");
        d.className="user";
        d.innerHTML=`<div class="dot"></div>${u.val().name}`;
        d.onclick=()=>{
          curChat=u.key; isGroup=false; loadChat(); openPage('chat',document.querySelectorAll('nav button')[1]);
        };
        userList.appendChild(d);
      }
    }
  });
  onlineCount.textContent=online;
});

onValue(ref(db,"groups"),snap=>{
  groupList.innerHTML="";
  groupCount.textContent=snap.size || 0;
  snap.forEach(g=>{
    const d=document.createElement("div");
    d.className="group";
    d.textContent=g.key;
    d.onclick=()=>{
      curChat=g.key; isGroup=true; loadChat(); openPage('chat',document.querySelectorAll('nav button')[1]);
    };
    groupList.appendChild(d);
  });
});

window.createGroup=()=>{
  const g=document.getElementById("groupInput").value.trim();
  if(!g){alert("Enter group name");return;}
  set(ref(db,"groups/"+g),{createdBy:currentUser});
  document.getElementById("groupInput").value='';
};

function loadChat(){
  if(!curChat) return;
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  onValue(ref(db,path),snap=>{
    chatBox.innerHTML="";
    snap.forEach(m=>{
      const div=document.createElement("div");
      div.className="msg";
      let actions=`<div class="actions" style="opacity:0.5; font-size:10px;"><span onclick="deleteMsg('${path}','${m.key}')">🗑️</span></div>`;
      div.innerHTML=`<b>${m.val().from}</b> ${m.val().text} ${actions}`;
      chatBox.appendChild(div);
      chatBox.scrollTop=chatBox.scrollHeight;
    });
  });
}

window.sendMsg=()=>{
  if(!curChat) return alert("Select a user/group first");
  const input=document.getElementById('msgInput');
  if(!input.value) return;
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  push(ref(db,path),{from:currentUser,text:input.value});
  input.value='';
};

window.deleteMsg=(path,key)=>{ if(confirm("Delete message?")) remove(ref(db,path+"/"+key)); };
</script>
</body>
</html>
