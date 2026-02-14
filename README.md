<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0a0a0a; --card:#111; --text:#fff; --muted:#aaa; --primary:#ff6ec7; --secondary:#6a5cff; --accent:#00f0ff;
}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
body.dark{--bg:#0a0a0a;--card:#111;--text:#fff;}
.app{max-width:480px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 0 20px rgba(0,0,0,.5);}
header h1{margin:0;font-size:20px;color:var(--accent); text-shadow: 0 0 10px var(--accent);}
header button{background:none;border:none;font-size:20px;color:var(--text); cursor:pointer;}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,var(--primary),var(--secondary));color:#fff;padding:24px;border-radius:18px;box-shadow:0 0 20px rgba(255,110,199,.4);}
.hero h2{margin:0 0 8px;text-shadow:0 0 8px #fff;}
.hero p{margin:0;opacity:.9;}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:40vh;overflow-y:auto;margin-bottom:10px;box-shadow:0 0 20px rgba(0,255,255,.2);}
.msg{background:#222;color:#fff;padding:10px 14px;border-radius:14px;margin-bottom:8px;font-size:14px;position:relative;box-shadow:0 0 5px rgba(0,255,255,.2);}
.msg .actions{position:absolute;top:4px;right:6px;display:flex;gap:6px;font-size:14px;cursor:pointer;}
.msg .comments{margin-top:6px;padding-left:12px;border-left:2px solid #333;}
.input-row{display:flex;gap:8px;margin-top:6px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #333;background:#111;color:#fff;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:linear-gradient(45deg,var(--primary),var(--secondary));color:#fff;cursor:pointer;box-shadow:0 0 10px var(--primary);}
.user{display:flex;align-items:center;gap:8px;margin-bottom:10px;cursor:pointer;}
.dot{width:10px;height:10px;background:#0f0;border-radius:50%;box-shadow:0 0 8px #0f0;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:0 -2px 10px rgba(0,0,0,.5);}
nav button{background:none;border:none;font-size:22px;color:var(--muted);}
nav button.active{color:var(--accent);}
.login-card{background:var(--card);padding:20px;border-radius:16px;box-shadow:0 0 20px rgba(255,255,255,.1);}
.login-card input{width:100%;padding:12px;border-radius:12px;margin-bottom:12px;border:1px solid #333;background:#111;color:#fff;}
.login-card button{width:100%;padding:12px;border:none;border-radius:12px;background:linear-gradient(45deg,var(--primary),var(--secondary));color:#fff;cursor:pointer;box-shadow:0 0 10px var(--primary);}
.group{background:#222;padding:10px;margin-bottom:6px;border-radius:12px;cursor:pointer;box-shadow:0 0 5px rgba(255,110,199,.2);}
.group:hover{background:linear-gradient(90deg,var(--primary),var(--secondary));}
.comment-input{margin-top:4px;display:flex;gap:4px;}
.comment-input input{flex:1;padding:6px;border-radius:8px;border:1px solid #333;font-size:12px;background:#111;color:#fff;}
.comment-input button{padding:6px 8px;border:none;border-radius:8px;background:var(--accent);color:#111;font-size:12px;cursor:pointer;}
.dashboard-card{background:#111;padding:16px;border-radius:14px;margin-bottom:12px;text-align:center;box-shadow:0 0 15px rgba(255,110,199,.2);}
.dashboard-card h3{margin:0;color:var(--accent);}
.dashboard-card p{margin:4px 0;color:#fff;font-weight:bold;}
.social-icons a{margin-right:12px;color:var(--accent);text-decoration:none;font-size:20px;}
.social-icons a:hover{color:var(--primary);}
.contact-card{background:#111;padding:16px;border-radius:14px;box-shadow:0 0 15px rgba(0,255,255,.2);}
</style>
</head>
<body>
<div class="app">
<header>
<h1>Live Connect</h1>
<button onclick="logout()"><i class="fa-solid fa-right-from-bracket"></i></button>
</header>

<!-- LOGIN -->
<div id="loginPage" class="page active">
<div class="login-card">
<h3>Enter Username</h3>
<input type="text" id="usernameInput" placeholder="Username">
<button onclick="login()">Continue</button>
</div>
</div>

<!-- DASHBOARD -->
<div id="home" class="page">
<div class="hero">
<h2>Dashboard</h2>
<p>Real-Time Active Users & Groups</p>
</div>
<div class="dashboard-card">
<h3>Active Users</h3>
<p id="activeUsersCount">0</p>
</div>
<div class="dashboard-card">
<h3>Total Groups</h3>
<p id="totalGroupsCount">0</p>
</div>
<div class="dashboard-card">
<h3>Welcome, <span id="currentUserName">User</span></h3>
<p>Enjoy chatting in real-time!</p>
</div>
</div>

<!-- CHAT -->
<div id="chat" class="page">
<div class="chat-box" id="chatBox"></div>
<div class="input-row">
<input id="msgInput" placeholder="Type message…" />
<button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
</div>
</div>

<!-- USERS & GROUPS -->
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

<!-- ABOUT -->
<div id="about" class="page">
<h3>About Live Connect 🚀</h3>
<p>Live Connect is a modern, secure, real-time chat platform designed for seamless communication. Features include:</p>
<ul>
<li>Private & group chats</li>
<li>Real-time active users</li>
<li>Glowing neon UI</li>
<li>Dark mode support</li>
</ul>
<div class="social-icons">
<a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank"><i class="fab fa-facebook"></i></a>
<a href="https://www.instagram.com/mr_nazim073" target="_blank"><i class="fab fa-instagram"></i></a>
<a href="https://youtube.com/@crazykhantv" target="_blank"><i class="fab fa-youtube"></i></a>
<a href="mailto:webhub262@gmail.com"><i class="fa-solid fa-envelope"></i></a>
</div>
</div>

<!-- CONTACT -->
<div id="contact" class="page">
<h3>Contact & Support</h3>
<div class="contact-card">
<p>Have questions? Reach us via:</p>
<p><i class="fa-solid fa-envelope"></i> webhub262@gmail.com</p>
<p><a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank"><i class="fab fa-facebook"></i> Facebook</a></p>
<p><a href="https://www.instagram.com/mr_nazim073" target="_blank"><i class="fab fa-instagram"></i> Instagram</a></p>
<p><a href="https://youtube.com/@crazykhantv" target="_blank"><i class="fab fa-youtube"></i> YouTube</a></p>
</div>
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
const currentUserName=document.getElementById("currentUserName");
const activeUsersCount=document.getElementById("activeUsersCount");
const totalGroupsCount=document.getElementById("totalGroupsCount");

// LOGIN
window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser=uname;
  currentUserName.textContent=uname;
  const userRef = ref(db,"users/"+uname);
  set(userRef,{name:uname,online:true});
  onDisconnect(userRef).update({online:false}); // auto offline on disconnect
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

// LOGOUT
window.logout=()=>{
  if(!currentUser) return;
  set(ref(db,"users/"+currentUser),{name:currentUser,online:false});
  currentUser=null;
  document.getElementById("home").classList.remove("active");
  document.getElementById("loginPage").classList.add("active");
};

// PAGE SWITCH
window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

// REAL-TIME USERS
onValue(ref(db,"users"),snap=>{
  userList.innerHTML="";
  let count=0;
  snap.forEach(u=>{
    if(u.val().online){
      count++;
      if(u.key!==currentUser){
        const d=document.createElement("div");
        d.className="user";
        d.innerHTML=`<div class="dot"></div>${u.val().name}`;
        d.onclick=()=>{curChat=u.key; isGroup=false; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'))};
        userList.appendChild(d);
      }
    }
  });
  activeUsersCount.textContent=count;
});

// REAL-TIME GROUPS
onValue(ref(db,"groups"),snap=>{
  groupList.innerHTML="";
  totalGroupsCount.textContent = snap.size;
  snap.forEach(g=>{
    const d=document.createElement("div");
    d.className="group";
    d.textContent=g.key;
    d.onclick=()=>{curChat=g.key; isGroup=true; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'))};
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

// LOAD CHAT
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
