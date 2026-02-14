<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0c0c0c; --card:#111; --text:#fff; --muted:#aaa; --primary:#ff0055; --secondary:#00f0ff;
  --glow-red:0 0 10px #ff0055, 0 0 20px #ff0055, 0 0 30px #ff0055;
  --glow-blue:0 0 10px #00f0ff, 0 0 20px #00f0ff, 0 0 30px #00f0ff;
}
body{
  margin:0;font-family:'Segoe UI',sans-serif;background:var(--bg);color:var(--text);
}
.app{max-width:480px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{
  padding:16px;display:flex;justify-content:space-between;align-items:center;
  background:#111;box-shadow:0 0 10px #00f0ff inset,0 0 20px #ff0055 inset;
}
header h1{margin:0;font-size:20px;text-shadow:var(--glow-red);}
header button{background:none;border:none;font-size:20px;color:var(--text);text-shadow:var(--glow-blue);cursor:pointer;}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,var(--primary),var(--secondary));color:#fff;padding:24px;border-radius:20px;text-align:center;box-shadow:0 0 20px var(--primary);}
.hero h2{text-shadow:var(--glow-red);}
.hero p{text-shadow:var(--glow-blue);}
.chat-box{background:#111;border-radius:16px;padding:12px;height:40vh;overflow-y:auto;margin-bottom:10px;box-shadow:0 0 15px var(--secondary);}
.msg{background:#222;color:#fff;padding:10px 14px;border-radius:16px;margin-bottom:10px;font-size:14px;position:relative;box-shadow:0 0 8px var(--glow-blue);}
.msg .actions{position:absolute;top:6px;right:10px;display:flex;gap:8px;font-size:14px;cursor:pointer;}
.msg .actions span{cursor:pointer;text-shadow:var(--glow-red);}
.msg .comments{margin-top:6px;padding-left:14px;border-left:2px solid var(--primary);}
.input-row{display:flex;gap:8px;}
.input-row input{flex:1;padding:12px;border-radius:14px;border:1px solid var(--secondary);background:#111;color:#fff;outline:none;box-shadow:0 0 8px var(--secondary);}
.input-row input:focus{box-shadow:0 0 15px var(--glow-blue);}
.input-row button{padding:12px 16px;border:none;border-radius:14px;background:var(--primary);color:#fff;cursor:pointer;box-shadow:var(--glow-red);}
.user{display:flex;align-items:center;gap:10px;margin-bottom:10px;cursor:pointer;transition:0.3s;}
.user:hover{transform:scale(1.03);}
.dot{width:12px;height:12px;background:#22ff55;border-radius:50%;box-shadow:0 0 10px #22ff55;}
nav{display:flex;justify-content:space-around;background:#111;padding:12px 0;box-shadow:0 -2px 10px var(--secondary);}
nav button{background:none;border:none;font-size:22px;color:var(--muted);cursor:pointer;}
nav button.active{color:var(--primary);text-shadow:var(--glow-red);}
.login-card{background:#111;padding:20px;border-radius:20px;box-shadow:0 0 15px var(--secondary);}
.login-card input{width:100%;padding:12px;border-radius:14px;margin-bottom:12px;border:1px solid var(--secondary);background:#111;color:#fff;outline:none;box-shadow:0 0 8px var(--secondary);}
.login-card input:focus{box-shadow:0 0 15px var(--glow-blue);}
.login-card button{width:100%;padding:12px;border:none;border-radius:14px;background:var(--primary);color:#fff;cursor:pointer;box-shadow:var(--glow-red);}
.group{background:#111;padding:10px;margin-bottom:8px;border-radius:14px;cursor:pointer;box-shadow:0 0 8px var(--glow-blue);}
.group:hover{box-shadow:0 0 15px var(--glow-red);}
.comment-input{margin-top:6px;display:flex;gap:6px;}
.comment-input input{flex:1;padding:8px;border-radius:10px;border:1px solid var(--secondary);background:#111;color:#fff;}
.comment-input input:focus{box-shadow:0 0 12px var(--glow-blue);}
.comment-input button{padding:6px 10px;border:none;border-radius:10px;background:var(--primary);color:#fff;cursor:pointer;box-shadow:var(--glow-red);}
.username-display{text-align:center;font-size:18px;margin-bottom:12px;text-shadow:var(--glow-red);}
</style>
</head>
<body>
<div class="app">
<header>
<h1>Live Connect</h1>
<button onclick="document.body.classList.toggle('dark')"><i class="fa-solid fa-moon"></i></button>
</header>

<!-- LOGIN PAGE -->
<div id="loginPage" class="page active">
<div class="login-card">
<h3>Enter Username</h3>
<input type="text" id="usernameInput" placeholder="Username">
<button onclick="login()">Continue</button>
</div>
</div>

<!-- HOME PAGE -->
<div id="home" class="page">
<div class="username-display" id="userDisplay">Welcome!</div>
<div class="hero">
<h2>Real-Time Chat</h2>
<p>Fast • Secure • Neon Theme</p>
</div>
<p style="margin-top:16px;color:var(--muted)">Connect instantly, see who’s online, chat privately or in groups, like and comment messages in real-time.</p>
</div>

<!-- CHAT PAGE -->
<div id="chat" class="page">
<div class="chat-box" id="chatBox"></div>
<div class="input-row">
<input id="msgInput" placeholder="Type message…" />
<button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
</div>
</div>

<!-- USERS PAGE -->
<div id="users" class="page">
<h3>Online Users</h3>
<div id="userList"></div>
<h3 style="margin-top:16px;">Groups</h3>
<div id="groupList"></div>
<div class="input-row" style="margin-top:6px;">
<input id="groupInput" placeholder="Create group"/>
<button onclick="createGroup()">➕</button>
</div>
</div>

<!-- ABOUT PAGE -->
<div id="about" class="page">
<h3>About Live Connect 🚀</h3>
<p>Live Connect is a modern neon chat platform. You can:</p>
<ul>
<li>Chat privately or in groups</li>
<li>See who’s online</li>
<li>Like & comment messages</li>
<li>Use dark/light toggle</li>
</ul>
</div>

<!-- CONTACT PAGE -->
<div id="contact" class="page">
<h3>Contact & Support</h3>
<p><i class="fa-solid fa-envelope"></i> webhub262@gmail.com</p>
<p>
<a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank"><i class="fab fa-facebook"></i> Facebook</a> | 
<a href="https://www.instagram.com/mr_nazim073" target="_blank"><i class="fab fa-instagram"></i> Instagram</a> | 
<a href="https://youtube.com/@crazykhantv" target="_blank"><i class="fab fa-youtube"></i> YouTube</a>
</p>
</div>

<nav>
<button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
<button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
<button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
<button onclick="openPage('about',this)"><i class="fa-solid fa-circle-info"></i></button>
<button onclick="openPage('contact',this)"><i class="fa-solid fa-envelope"></i></button>
</nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, get, child } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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
const userDisplay=document.getElementById("userDisplay");

// LOGIN
window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser=uname;
  set(ref(db,"users/"+uname),{name:uname,online:true});
  userDisplay.textContent=`Welcome, ${currentUser}`;
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

// PAGE SWITCH
window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

// ONLINE USERS
onValue(ref(db,"users"),snap=>{
  userList.innerHTML=""; groupList.innerHTML="";
  snap.forEach(u=>{
    if(u.val().online && u.key!==currentUser){
      const d=document.createElement("div");
      d.className="user";
      d.innerHTML=`<div class="dot"></div>${u.val().name}`;
      d.onclick=()=>{curChat=u.key; isGroup=false; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'));};
      userList.appendChild(d);
    }
  });
});

// GROUPS
onValue(ref(db,"groups"),snap=>{
  groupList.innerHTML="";
  snap.forEach(g=>{
    const d=document.createElement("div");
    d.className="group";
    d.textContent=g.key;
    d.onclick=()=>{curChat=g.key; isGroup=true; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'));};
    groupList.appendChild(d);
  });
});

// CREATE GROUP
window.createGroup=()=>{
  const g=document.getElementById("groupInput").value.trim();
  if(!g){alert("Enter group name");return;}
  set(ref(db,"groups/"+g),{createdBy:currentUser});
  document.getElementById("groupInput").value='';
};

// CHAT LOAD
function loadChat(){
  if(!curChat) return;
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  onValue(ref(db,path),snap=>{
    chatBox.innerHTML="";
    snap.forEach(m=>{
      const div=document.createElement("div");
      div.className="msg";
      let actions=`<div class="actions"><span onclick="deleteMsg('${path}','${m.key}')">🗑️</span></div>`;
      let commentsHTML='<div class="comments">'+(m.val().comments?Object.values(m.val().comments).map(c=>`<div><b>${c.from}:</b> ${c.text}</div>`).join(''):'')+'</div>';
      let commentInput=`<div class="comment-input"><input placeholder="Comment"/><button onclick="addComment('${path}','${m.key}',this)">💬</button></div>`;
      div.innerHTML=`<b>${m.val().from}:</b> ${m.val().text} ${actions} ${commentsHTML} ${commentInput}`;
      chatBox.appendChild(div);
      chatBox.scrollTop=chatBox.scrollHeight;
    });
  });
}

// SEND MESSAGE
window.sendMsg=()=>{
  if(!curChat) return alert("Select a user/group first");
  const input=document.getElementById('msgInput');
  if(!input.value) return;
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  push(ref(db,path),{from:currentUser,text:input.value});
  input.value='';
};

// DELETE MESSAGE
window.deleteMsg=(path,key)=>{remove(ref(db,path+"/"+key));};

// ADD COMMENT
window.addComment=(path,key,btn)=>{
  const input=btn.previousElementSibling;
  if(!input.value) return;
  push(ref(db,path+"/"+key+"/comments"),{from:currentUser,text:input.value});
  input.value='';
};
</script>
</body>
</html>
