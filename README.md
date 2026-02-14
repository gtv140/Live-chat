<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#f6f7fb;--card:#fff;--text:#111827;--muted:#6b7280;--primary:#2563eb;
}
body.dark{
  --bg:#0f172a;--card:#111827;--text:#e5e7eb;--muted:#9ca3af;--primary:#3b82f6;
}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text);}
.app{max-width:420px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:45vh;overflow-y:auto;margin-bottom:10px;}
.msg{background:#e5e7eb;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;position:relative;}
body.dark .msg{background:#1f2937;color:#fff;}
.actions{position:absolute;top:4px;right:6px;font-size:12px;display:flex;gap:6px;cursor:pointer;}
.input-row{display:flex;gap:8px;}
.input-row input{flex:1;padding:10px;border-radius:10px;border:1px solid #ccc;}
.input-row button{padding:10px 14px;border:none;border-radius:10px;background:var(--primary);color:#fff;}
.user,.group{padding:8px;border-radius:10px;background:var(--card);margin-bottom:6px;cursor:pointer;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;}
nav button{background:none;border:none;font-size:18px;color:var(--muted);}
nav button.active{color:var(--primary);}
.login-card{background:var(--card);padding:16px;border-radius:16px;}
.login-card input{width:100%;padding:10px;margin-bottom:8px;border-radius:10px;border:1px solid #ccc;}
.login-card button{width:100%;padding:10px;border:none;border-radius:10px;background:var(--primary);color:#fff;}
</style>
</head>
<body>
<div class="app">

<header>
<h2>Live Connect</h2>
<button onclick="document.body.classList.toggle('dark')">
<i class="fa-solid fa-moon"></i>
</button>
</header>

<!-- LOGIN -->
<div id="loginPage" class="page active">
<div class="login-card">
<h3>Enter Username</h3>
<input id="usernameInput" placeholder="Username">
<button onclick="login()">Continue</button>
</div>
</div>

<!-- HOME -->
<div id="home" class="page">
<h3>Welcome to Live Connect 🚀</h3>
<p>Simple • Fast • Real-time chat</p>
</div>

<!-- CHAT -->
<div id="chat" class="page">
<div id="chatBox" class="chat-box"></div>
<div class="input-row">
<input id="msgInput" placeholder="Type message">
<button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
</div>
</div>

<!-- USERS -->
<div id="users" class="page">
<h3>Online Users</h3>
<div id="userList"></div>
<h3>Groups</h3>
<div id="groupList"></div>
<div class="input-row">
<input id="groupInput" placeholder="Create group">
<button onclick="createGroup()">+</button>
</div>
</div>

<nav>
<button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
<button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
<button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
</nav>

</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, off } 
from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_AUTH_DOMAIN",
  databaseURL: "YOUR_DB_URL",
  projectId: "YOUR_PROJECT_ID",
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser=null;
let curChat=null;
let isGroup=false;
let currentRef=null;

const chatBox=document.getElementById("chatBox");
const userList=document.getElementById("userList");
const groupList=document.getElementById("groupList");

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname)return alert("Enter username");
  currentUser=uname;
  set(ref(db,"users/"+uname),{online:true});
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

onValue(ref(db,"users"),snap=>{
  userList.innerHTML="";
  snap.forEach(u=>{
    if(u.key!==currentUser){
      const d=document.createElement("div");
      d.className="user";
      d.textContent=u.key;
      d.onclick=()=>{
        curChat=u.key;
        isGroup=false;
        loadChat();
        openPage('chat',document.querySelectorAll("nav button")[1]);
      };
      userList.appendChild(d);
    }
  });
});

onValue(ref(db,"groups"),snap=>{
  groupList.innerHTML="";
  snap.forEach(g=>{
    const d=document.createElement("div");
    d.className="group";
    d.textContent=g.key;
    d.onclick=()=>{
      curChat=g.key;
      isGroup=true;
      loadChat();
      openPage('chat',document.querySelectorAll("nav button")[1]);
    };
    groupList.appendChild(d);
  });
});

window.createGroup=()=>{
  const g=document.getElementById("groupInput").value.trim();
  if(!g)return;
  set(ref(db,"groups/"+g),{createdBy:currentUser});
  document.getElementById("groupInput").value='';
};

function loadChat(){
  if(!curChat)return;

  const path=isGroup 
  ? "groupChats/"+curChat
  : "chats/"+[currentUser,curChat].sort().join("_");

  if(currentRef) off(currentRef);
  currentRef=ref(db,path);

  onValue(currentRef,snap=>{
    chatBox.innerHTML="";
    snap.forEach(m=>{
      const div=document.createElement("div");
      div.className="msg";

      div.innerHTML=`
      <b>${m.val().from}</b>: ${m.val().text}
      <div class="actions">
      <span onclick="likeMsg('${path}','${m.key}',${m.val().likes||0})">👍 ${m.val().likes||0}</span>
      <span onclick="deleteMsg('${path}','${m.key}')">🗑️</span>
      </div>`;

      chatBox.appendChild(div);
    });
    chatBox.scrollTop=chatBox.scrollHeight;
  });
}

window.sendMsg=()=>{
  if(!curChat)return alert("Select chat first");
  const input=document.getElementById("msgInput");
  if(!input.value)return;
  const path=isGroup 
  ? "groupChats/"+curChat
  : "chats/"+[currentUser,curChat].sort().join("_");
  push(ref(db,path),{
    from:currentUser,
    text:input.value,
    likes:0
  });
  input.value='';
};

window.likeMsg=(path,key,likes)=>{
  update(ref(db,path+"/"+key),{likes:likes+1});
};

window.deleteMsg=(path,key)=>{
  remove(ref(db,path+"/"+key));
};

window.addEventListener("beforeunload",()=>{
  if(currentUser){
    set(ref(db,"users/"+currentUser+"/online"),false);
  }
});
</script>

</body>
</html>
