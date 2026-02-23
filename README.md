<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0a0a0a; --card:#111; --text:#e5e5e5; --muted:#aaa; --primary:#6b5bff; --accent:#ff5c8d; --success:#22c55e;
}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);transition:.3s;}
body.light{--bg:#f1f1f1;--card:#fff;--text:#111;--muted:#555;}
.app{max-width:480px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 3px 15px rgba(0,0,0,.7);}
header h1{margin:0;font-size:22px;color:var(--primary);text-shadow:0 0 12px var(--accent);}
header button{background:none;border:none;font-size:20px;color:var(--text);cursor:pointer;transition:.3s;}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,#ff5c8d,#6b5bff);color:#fff;padding:28px;border-radius:20px;text-align:center;box-shadow:0 0 15px #6b5bff;}
.dashboard-stat{background:var(--card);padding:16px;margin-bottom:12px;border-radius:16px;box-shadow:0 0 12px var(--primary);display:flex;justify-content:space-between;align-items:center;}
.chat-box{background:var(--card);border-radius:18px;padding:14px;height:36vh;overflow-y:auto;margin-bottom:12px;box-shadow:0 0 12px var(--primary);}
.msg{background:#222;color:#fff;padding:10px 14px;border-radius:16px;margin-bottom:10px;font-size:14px;position:relative;box-shadow:0 0 6px var(--primary);}
.msg .actions{position:absolute;top:6px;right:8px;display:flex;gap:6px;font-size:13px;cursor:pointer;}
.input-row{display:flex;gap:10px;}
.input-row input{flex:1;padding:14px;border-radius:14px;border:1px solid #444;background:#111;color:#fff;outline:none;}
.input-row button{padding:14px 18px;border:none;border-radius:14px;background:linear-gradient(90deg,#ff5c8d,#6b5bff);color:#fff;cursor:pointer;}
.user,.group{display:flex;align-items:center;gap:10px;margin-bottom:12px;cursor:pointer;padding:12px;background:var(--card);border-radius:14px;box-shadow:0 0 8px var(--primary);}
.dot{width:12px;height:12px;background:var(--success);border-radius:50%;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:12px 0;box-shadow:0 -3px 12px rgba(0,0,0,.7);}
nav button{background:none;border:none;font-size:22px;color:var(--muted);cursor:pointer;}
nav button.active{color:var(--primary);}
.login-card{background:var(--card);padding:20px;border-radius:18px;box-shadow:0 0 15px var(--primary);}
.login-card input{width:100%;padding:14px;border-radius:14px;margin-bottom:12px;border:1px solid #444;background:#111;color:#fff;}
.login-card button{width:100%;padding:14px;border:none;border-radius:14px;background:linear-gradient(90deg,#ff5c8d,#6b5bff);color:#fff;}

/* Admin Extra Style */
.admin-only{display:none; border:2px solid var(--accent); padding:10px; border-radius:15px; margin-top:15px;}
</style>
</head>
<body>
<div class="app">
<header>
<h1>Live Connect</h1>
<button onclick="toggleTheme()"><i class="fa-solid fa-moon"></i></button>
<button onclick="logout()"><i class="fa-solid fa-right-from-bracket"></i></button>
</header>

<div id="loginPage" class="page active">
<div class="login-card"><h3>Enter Username</h3><input type="text" id="usernameInput" placeholder="Username"><button onclick="login()">Continue</button></div>
</div>

<div id="home" class="page">
<div class="hero"><h2>Welcome <span id="dashUser"></span> 🚀</h2><p>Real-Time Chat & Active Connections</p></div>
<div class="dashboard-stat"><h4>Total Users</h4><span id="onlineCount">0</span></div>
<div class="dashboard-stat"><h4>Total Groups</h4><span id="groupCount">0</span></div>

<div id="adminPanel" class="admin-only">
    <h3 style="color:var(--accent)">Admin Dashboard</h3>
    <div class="input-row">
        <input id="bcInput" placeholder="Global Broadcast Message...">
        <button onclick="sendBroadcast()">📢</button>
    </div>
    <div id="adminUserList" style="margin-top:10px;"></div>
</div>
</div>

<div id="chat" class="page">
<div class="chat-box" id="chatBox"></div>
<div class="input-row">
    <input id="msgInput" placeholder="Type message…"/>
    <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
</div>
</div>

<div id="users" class="page">
<h3>Online Users</h3><div id="userList"></div>
<h3 style="margin-top:16px;">Groups</h3><div id="groupList"></div>
<div class="input-row" style="margin-top:6px;"><input id="groupInput" placeholder="Create group"/><button onclick="createGroup()">➕</button></div>
</div>

<nav>
<button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
<button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
<button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
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

let currentUser=null;
let curChat="";
let isGroup=false;

// ADMIN LIST (ID YAHA ADD KREIN)
const admins = ["Muhammad Nazim", "Admin786"];

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser=uname;
  dashUser.textContent=currentUser;
  
  // Show Admin Panel if user is admin
  if(admins.includes(currentUser)){ document.getElementById("adminPanel").style.display="block"; }

  const userRef=ref(db,"users/"+uname);
  set(userRef,{name:uname,online:true});
  onDisconnect(userRef).update({online:false});
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

// ... Rest of your original functions (openPage, logout, toggleTheme, createGroup) ...
window.logout=()=>{ if(!currentUser) return; update(ref(db,"users/"+currentUser),{online:false}); location.reload(); };
window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};
window.toggleTheme=()=>{document.body.classList.toggle('light');};

onValue(ref(db,"users"),snap=>{
  let online=0; userList.innerHTML="";
  if(admins.includes(currentUser)){ adminUserList.innerHTML = "<h4>Ban Users:</h4>"; }
  snap.forEach(u=>{
    if(u.val().online){
      online++;
      if(u.key!==currentUser){
        const d=document.createElement("div"); d.className="user";
        d.innerHTML=`<div class="dot"></div>${u.val().name}`;
        d.onclick=()=>{ curChat=u.key; isGroup=false; loadChat(); openPage('chat',document.querySelectorAll('nav button')[1]); };
        userList.appendChild(d);
      }
    }
    // Admin Ban List Logic
    if(admins.includes(currentUser) && u.key !== currentUser){
        adminUserList.innerHTML += `<div class="dashboard-stat">${u.key} <button onclick="banUser('${u.key}')" style="background:red; color:white; border:none; padding:5px; border-radius:5px;">Ban</button></div>`;
    }
  });
  onlineCount.textContent=online;
});

// Broadcast Logic
window.sendBroadcast=()=>{
    const msg = document.getElementById("bcInput").value;
    if(msg){ set(ref(db,"broadcast"), {text:msg, from:currentUser}); alert("Sent!"); }
};
onValue(ref(db,"broadcast"), snap=>{
    if(snap.exists()){ alert("📢 BROADCAST: " + snap.val().text); }
});

// Ban User Logic
window.banUser=(target)=>{ if(confirm("Ban this user?")){ remove(ref(db,"users/"+target)); } };

// Original Chat Logic
function loadChat(){
  if(!curChat) return;
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  onValue(ref(db,path),snap=>{
    chatBox.innerHTML="";
    snap.forEach(m=>{
      const div=document.createElement("div"); div.className="msg";
      div.innerHTML=`<b>${m.val().from}:</b> ${m.val().text} <div class="actions" onclick="deleteMsg('${path}','${m.key}')">🗑️</div>`;
      chatBox.appendChild(div);
      chatBox.scrollTop=chatBox.scrollHeight;
    });
  });
}
window.sendMsg=()=>{
  if(!curChat) return;
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  push(ref(db,path),{from:currentUser,text:document.getElementById('msgInput').value});
  document.getElementById('msgInput').value='';
};
window.deleteMsg=(path,key)=>{ remove(ref(db,path+"/"+key)); };
window.createGroup=()=>{
  const g=document.getElementById("groupInput").value.trim();
  if(g){ set(ref(db,"groups/"+g),{createdBy:currentUser}); document.getElementById("groupInput").value=''; }
};
onValue(ref(db,"groups"),snap=>{
  groupList.innerHTML=""; groupCount.textContent=snap.size;
  snap.forEach(g=>{
    const d=document.createElement("div"); d.className="group"; d.textContent=g.key;
    d.onclick=()=>{ curChat=g.key; isGroup=true; loadChat(); openPage('chat',document.querySelectorAll('nav button')[1]); };
    groupList.appendChild(d);
  });
});
</script>
</body>
</html>
