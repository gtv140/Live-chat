<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0b0c10; --card:#1f1f1f; --text:#e5e7eb; --muted:#aaa; --primary:#0ff; --shadow:0 0 15px rgba(0,255,255,0.2);
}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
.app{max-width:420px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:var(--shadow);}
header h1{margin:0;font-size:18px;color:var(--primary);text-shadow:0 0 8px var(--primary);}
header button{background:none;border:none;font-size:18px;color:var(--primary);text-shadow:0 0 6px var(--primary);}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,#0ff,#00aaff);color:#fff;padding:24px;border-radius:18px;box-shadow:var(--shadow);}
.hero h2{margin:0 0 8px;text-shadow:0 0 8px #0ff;}
.hero p{margin:0;opacity:.9;}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:40vh;overflow-y:auto;margin-bottom:10px;box-shadow:var(--shadow);}
.msg{background:#111;padding:10px 14px;border-radius:14px;margin-bottom:8px;font-size:14px;position:relative;box-shadow:0 0 6px #0ff55;}
.msg .actions{position:absolute;top:4px;right:6px;display:flex;gap:4px;font-size:12px;cursor:pointer;}
.msg .comments{margin-top:6px;padding-left:12px;border-left:2px solid #0ff;}
.input-row{display:flex;gap:8px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #0ff;background:#111;color:#fff;outline:none;box-shadow:0 0 8px #0ff inset;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:var(--primary);color:#000;font-weight:bold;box-shadow:var(--shadow);transition:0.3s;}
.input-row button:hover{box-shadow:0 0 15px var(--primary);transform:scale(1.05);}
.user,.group{display:flex;align-items:center;gap:8px;margin-bottom:10px;cursor:pointer;padding:6px 10px;border-radius:12px;transition:0.3s;box-shadow:0 0 5px #0ff1;}
.user:hover,.group:hover{background:rgba(0,255,255,0.1);box-shadow:0 0 12px #0ff;}
.dot{width:10px;height:10px;background:#0f0;border-radius:50%;box-shadow:0 0 6px #0f0;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:var(--shadow);}
nav button{background:none;border:none;font-size:20px;color:var(--muted);}
nav button.active{color:var(--primary);text-shadow:0 0 6px var(--primary);}
.login-card{background:var(--card);padding:16px;border-radius:16px;box-shadow:var(--shadow);}
.login-card input{width:100%;padding:12px;border-radius:12px;margin-bottom:8px;border:1px solid #0ff;background:#111;color:#fff;outline:none;box-shadow:0 0 8px #0ff inset;}
.login-card button{width:100%;padding:12px;border:none;border-radius:12px;background:var(--primary);color:#000;font-weight:bold;box-shadow:var(--shadow);transition:0.3s;}
.login-card button:hover{box-shadow:0 0 15px var(--primary);transform:scale(1.05);}
.comment-input{margin-top:4px;display:flex;gap:4px;}
.comment-input input{flex:1;padding:6px;border-radius:8px;border:1px solid #0ff;background:#111;color:#fff;outline:none;box-shadow:0 0 6px #0ff inset;}
.comment-input button{padding:6px 8px;border:none;border-radius:8px;background:var(--primary);color:#000;font-size:12px;box-shadow:var(--shadow);}
.comment-input button:hover{box-shadow:0 0 12px var(--primary);transform:scale(1.05);}
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
<div class="hero">
<h2>Real-Time Chat</h2>
<p>Fast • Secure • Mobile Friendly</p>
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
<p>Live Connect is a modern chat platform for mobile and desktop. You can:</p>
<ul>
<li>Chat privately or in groups</li>
<li>See who’s online</li>
<li>Like & comment messages</li>
<li>Use dark/light mode</li>
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

// LOGIN
window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser=uname;
  const userRef = ref(db,"users/"+uname);
  set(userRef,{name:uname,online:true});
  // ensure offline on disconnect
  onDisconnect(userRef).update({online:false});
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

// PAGE NAV
window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

// ONLINE USERS
onValue(ref(db,"users"),snap=>{
  userList.innerHTML="";
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

// CHAT LOADING
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
