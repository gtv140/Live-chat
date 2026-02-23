<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --bg: #0a0a0c;
  --card: #16161e;
  --text: #e2e8f0;
  --primary: #6366f1; /* Modern Indigo */
  --accent: #f43f5e;  /* Rose accent */
  --success: #10b981;
  --glass: rgba(255, 255, 255, 0.03);
}

body {
  margin: 0;
  font-family: 'Inter', system-ui, sans-serif;
  background: var(--bg);
  color: var(--text);
  height: 100dvh;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

/* Header Styling */
header {
  padding: 16px 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: var(--card);
  border-bottom: 1px solid rgba(255,255,255,0.05);
}
header h1 {
  margin: 0;
  font-size: 20px;
  background: linear-gradient(90deg, var(--primary), var(--accent));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  font-weight: 800;
}
header button {
  background: var(--glass);
  border: 1px solid rgba(255,255,255,0.1);
  color: var(--text);
  padding: 8px 12px;
  border-radius: 10px;
  cursor: pointer;
}

/* Page Transitions */
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; }
.page.active { display: block; animation: slideUp 0.3s ease; }
@keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

/* Hero Section */
.hero {
  background: linear-gradient(135deg, var(--primary), var(--accent));
  padding: 30px 20px;
  border-radius: 24px;
  text-align: center;
  box-shadow: 0 10px 30px rgba(99, 102, 241, 0.3);
  margin-bottom: 20px;
}
.hero h2 { margin: 0; font-size: 22px; }
.hero p { margin: 5px 0 0; font-size: 13px; opacity: 0.8; }

/* Dashboard Stats */
.dashboard-stat {
  background: var(--card);
  padding: 18px;
  margin-bottom: 12px;
  border-radius: 18px;
  border: 1px solid rgba(255,255,255,0.05);
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.dashboard-stat h4 { margin: 0; color: var(--accent); font-size: 14px; }
.dashboard-stat span { font-weight: 800; font-size: 18px; color: var(--primary); }

/* Chat Box */
.chat-box {
  background: var(--card);
  border-radius: 20px;
  padding: 15px;
  height: 50vh;
  overflow-y: auto;
  margin-bottom: 15px;
  border: 1px solid rgba(255,255,255,0.05);
}
.msg {
  background: #23232e;
  padding: 12px 16px;
  border-radius: 16px;
  border-bottom-left-radius: 4px;
  margin-bottom: 12px;
  font-size: 14px;
  position: relative;
  border: 1px solid rgba(255,255,255,0.03);
}
.msg b { color: var(--primary); display: block; margin-bottom: 3px; font-size: 12px; }

/* Input Styling */
.input-row { display: flex; gap: 10px; background: var(--card); padding: 5px; border-radius: 15px; }
.input-row input {
  flex: 1; padding: 12px; border: none; background: transparent; color: white; outline: none;
}
.input-row button {
  background: var(--primary); border: none; padding: 12px 20px; border-radius: 12px; color: white; cursor: pointer;
}

/* User & Group List */
.user, .group {
  display: flex; align-items: center; gap: 12px; padding: 15px;
  background: var(--card); border-radius: 16px; margin-bottom: 10px; cursor: pointer;
  border: 1px solid rgba(255,255,255,0.05); transition: 0.2s;
}
.user:hover { border-color: var(--primary); }
.dot { width: 10px; height: 10px; background: var(--success); border-radius: 50%; box-shadow: 0 0 8px var(--success); }

/* Navigation */
nav {
  display: flex; justify-content: space-around; background: var(--card);
  padding: 15px 0; border-top: 1px solid rgba(255,255,255,0.05);
}
nav button { background: none; border: none; font-size: 20px; color: #64748b; cursor: pointer; }
nav button.active { color: var(--primary); }

/* Login Card */
.login-card { background: var(--card); padding: 30px; border-radius: 25px; text-align: center; }
.login-card input {
  width: 100%; padding: 14px; border-radius: 12px; margin-bottom: 15px;
  background: #000; border: 1px solid #333; color: white; outline: none;
}
.login-card button {
  width: 100%; padding: 14px; border: none; border-radius: 12px; background: var(--primary); color: white; font-weight: bold;
}
</style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div>
            <button onclick="toggleTheme()"><i class="fa-solid fa-moon"></i></button>
            <button onclick="logout()"><i class="fa-solid fa-power-off"></i></button>
        </div>
    </header>

    <div id="loginPage" class="page active">
        <div style="height: 100%; display: flex; align-items: center; justify-content: center;">
            <div class="login-card">
                <h3>Welcome Back</h3>
                <input type="text" id="usernameInput" placeholder="Enter Username">
                <button onclick="login()">Get Started</button>
            </div>
        </div>
    </div>

    <div id="home" class="page">
        <div class="hero">
            <h2>Hello, <span id="dashUser"></span> 🚀</h2>
            <p>Your connections are live and secure.</p>
        </div>
        <div class="dashboard-stat"><h4>Online Users</h4><span id="onlineCount">0</span></div>
        <div class="dashboard-stat"><h4>Active Groups</h4><span id="groupCount">0</span></div>
    </div>

    <div id="chat" class="page">
        <div class="chat-box" id="chatBox"></div>
        <div class="input-row">
            <input id="msgInput" placeholder="Type a message..."/>
            <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="users" class="page">
        <h3 style="font-size: 14px; color: var(--accent); text-transform: uppercase;">Direct Messages</h3>
        <div id="userList"></div>
        <h3 style="font-size: 14px; color: var(--accent); text-transform: uppercase; margin-top: 20px;">Channels</h3>
        <div id="groupList"></div>
        <div class="input-row" style="margin-top: 15px;">
            <input id="groupInput" placeholder="Group name..."/>
            <button onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
        </div>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openPage('chat',this)"><i class="fa-solid fa-comment"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-users"></i></button>
        <button onclick="openPage('contact',this)"><i class="fa-solid fa-envelope"></i></button>
    </nav>
</div>

<script type="module">
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
  const target = document.getElementById(id);
  if(target) target.classList.add('active');
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
      div.innerHTML=`<b>${m.val().from}</b> ${m.val().text}`;
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
</script>
</body>
</html>
