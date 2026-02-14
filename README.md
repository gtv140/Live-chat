<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0a0a0a;
  --card:#111;
  --text:#e0e0ff;
  --muted:#aaa;
  --primary:#ff49f5;
  --secondary:#00cfff;
  --accent:#ff3c3c;
}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
body.dark{--bg:#0a0a0a;}
.app{max-width:480px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 2px 20px rgba(255,255,255,.1);}
header h1{margin:0;font-size:20px;color:var(--primary);text-shadow:0 0 8px var(--primary);}
header .logout-btn{font-size:18px;color:var(--accent);cursor:pointer;transition:.3s;}
header .logout-btn:hover{color:#ff77aa;}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,var(--primary),var(--secondary));color:#fff;padding:24px;border-radius:20px;box-shadow:0 0 20px var(--primary);}
.hero h2{margin:0 0 8px;text-shadow:0 0 12px #fff;}
.hero p{margin:0;opacity:.9;text-shadow:0 0 6px #fff;}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:35vh;overflow-y:auto;margin-bottom:10px;box-shadow:0 0 10px var(--secondary);}
.msg{background:#222;color:#fff;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;position:relative;box-shadow:0 0 8px var(--primary);}
.msg .actions{position:absolute;top:4px;right:6px;display:flex;gap:4px;font-size:12px;cursor:pointer;}
.msg .comments{margin-top:6px;padding-left:12px;border-left:2px solid #444;}
.input-row{display:flex;gap:8px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #444;background:#111;color:#fff;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:linear-gradient(45deg,var(--primary),var(--secondary));color:#fff;cursor:pointer;font-weight:bold;box-shadow:0 0 10px var(--primary);}
.user{display:flex;align-items:center;gap:8px;margin-bottom:10px;cursor:pointer;}
.dot{width:10px;height:10px;background:#22c55e;border-radius:50%;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:0 -2px 20px rgba(255,255,255,.1);}
nav button{background:none;border:none;font-size:22px;color:var(--muted);cursor:pointer;transition:0.3s;}
nav button.active{color:var(--primary);}
nav button:hover{color:var(--secondary);}
.login-card{background:var(--card);padding:20px;border-radius:16px;box-shadow:0 0 20px var(--primary);}
.login-card input{width:100%;padding:12px;border-radius:12px;margin-bottom:12px;border:1px solid #444;background:#111;color:#fff;}
.login-card button{width:100%;padding:12px;border:none;border-radius:12px;background:linear-gradient(45deg,var(--primary),var(--secondary));color:#fff;font-weight:bold;cursor:pointer;box-shadow:0 0 12px var(--primary);}
.group{background:#222;padding:8px;margin-bottom:6px;border-radius:12px;cursor:pointer;box-shadow:0 0 8px var(--secondary);}
.group:hover{background:linear-gradient(90deg,var(--primary),var(--secondary));}
.comment-input{margin-top:4px;display:flex;gap:4px;}
.comment-input input{flex:1;padding:6px;border-radius:8px;border:1px solid #444;background:#111;color:#fff;font-size:12px;}
.comment-input button{padding:6px 8px;border:none;border-radius:8px;background:linear-gradient(45deg,var(--primary),var(--secondary));color:#fff;font-size:12px;cursor:pointer;}
.about-card,.contact-card{background:#111;padding:16px;border-radius:16px;box-shadow:0 0 20px var(--primary);}
.social-icons a{margin-right:10px;font-size:20px;color:var(--primary);text-decoration:none;transition:0.3s;}
.social-icons a:hover{color:var(--secondary);text-shadow:0 0 10px var(--secondary);}
</style>
</head>
<body>
<div class="app">
<header>
<h1>Live Connect</h1>
<i class="fa-solid fa-right-from-bracket logout-btn" onclick="logout()" title="Logout"></i>
</header>

<!-- LOGIN PAGE -->
<div id="loginPage" class="page active">
<div class="login-card">
<h3>Enter Username</h3>
<input type="text" id="usernameInput" placeholder="Username">
<button onclick="login()">Continue</button>
</div>
</div>

<!-- HOME / DASHBOARD -->
<div id="home" class="page">
<div class="hero">
<h2>Welcome <span id="dashUser"></span> 🚀</h2>
<p>Fast • Secure • Modern Chat Platform</p>
</div>
<div class="about-card" style="margin-top:16px;">
<h3>Active Users</h3>
<div id="userList"></div>
<h3 style="margin-top:16px;">Groups</h3>
<div id="groupList"></div>
<div class="input-row" style="margin-top:6px;">
<input id="groupInput" placeholder="Create new group"/>
<button onclick="createGroup()">➕</button>
</div>
</div>
</div>

<!-- CHAT PAGE -->
<div id="chat" class="page">
<div class="chat-box" id="chatBox"></div>
<div class="input-row">
<input id="msgInput" placeholder="Type message…" />
<button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
</div>
</div>

<!-- ABOUT PAGE -->
<div id="about" class="page">
<div class="about-card">
<h3>About Live Connect</h3>
<p>Live Connect is a modern, fast, and secure chat platform for web and mobile. You can chat privately or in groups, see who's online, like and comment messages in real-time.</p>
<p>Our mission: Provide smooth and reliable communication experience with neon-glow interactive UI.</p>
<div class="social-icons" style="margin-top:12px;">
<a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank"><i class="fab fa-facebook"></i></a>
<a href="https://www.instagram.com/mr_nazim073" target="_blank"><i class="fab fa-instagram"></i></a>
<a href="https://youtube.com/@crazykhantv" target="_blank"><i class="fab fa-youtube"></i></a>
<a href="mailto:webhub262@gmail.com"><i class="fa-solid fa-envelope"></i></a>
</div>
</div>
</div>

<!-- CONTACT PAGE -->
<div id="contact" class="page">
<div class="contact-card">
<h3>Contact Us</h3>
<p>For any queries or support, reach us through:</p>
<div class="social-icons">
<a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank"><i class="fab fa-facebook"></i></a>
<a href="https://www.instagram.com/mr_nazim073" target="_blank"><i class="fab fa-instagram"></i></a>
<a href="https://youtube.com/@crazykhantv" target="_blank"><i class="fab fa-youtube"></i></a>
<a href="mailto:webhub262@gmail.com"><i class="fa-solid fa-envelope"></i></a>
</div>
</div>
</div>

<nav>
<button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
<button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
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
const dashUser=document.getElementById("dashUser");

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser=uname;
  dashUser.textContent=uname;
  const userRef=ref(db,"users/"+uname);
  set(userRef,{name:uname,online:true});
  onDisconnect(userRef).update({online:false});
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

// Logout
window.logout=()=>{
  if(!currentUser) return;
  const userRef=ref(db,"users/"+currentUser);
  set(userRef,{name:currentUser,online:false});
  currentUser=null;
  document.getElementById("home").classList.remove("active");
  document.getElementById("loginPage").classList.add("active");
};

// Page switch
window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

// Online users
onValue(ref(db,"users"),snap=>{
  userList.innerHTML="";
  groupList.innerHTML="";
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

// Groups
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

window.createGroup=()=>{
  const g=document.getElementById("groupInput").value.trim();
  if(!g){alert("Enter group name");return;}
  set(ref(db,"groups/"+g),{createdBy:currentUser});
  document.getElementById("groupInput").value='';
};

// Chat load
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

window.sendMsg=()=>{
  if(!curChat) return alert("Select a user/group first");
  const input=document.getElementById('msgInput');
  if(!input.value) return;
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  push(ref(db,path),{from:currentUser,text:input.value});
  input.value='';
};

window.deleteMsg=(path,key)=>{remove(ref(db,path+"/"+key));};

window.addComment=(path,key,btn)=>{
  const input=btn.previousElementSibling;
  if(!input.value) return;
  push(ref(db,path+"/"+key+"/comments"),{from:currentUser,text:input.value});
  input.value='';
};
</script>
</body>
</html>
