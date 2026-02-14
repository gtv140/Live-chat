<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
/* Root colors & neon theme */
:root{
  --bg:#0a0a0a; --card:#111; --text:#fff; --muted:#aaa; 
  --primary-gradient:linear-gradient(45deg,#ff00ff,#00ffff,#ff007f);
  --button-gradient:linear-gradient(45deg,#ff3cac,#784ba0,#2b86c5);
}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
.app{max-width:480px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:#111;box-shadow:0 0 10px #00ffff;}
header h1{margin:0;font-size:20px;color:#ff3cac;text-shadow:0 0 5px #ff3cac,0 0 10px #00ffff;}
header button{background:none;border:none;font-size:18px;color:var(--text);}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}

/* Hero / Dashboard */
.hero{background:var(--primary-gradient);padding:24px;border-radius:18px;color:#fff;text-align:center;text-shadow:0 0 5px #fff,0 0 10px #ff3cac;}
.hero h2{margin:0;font-size:22px;}
.hero p{margin:4px 0;opacity:.9;font-size:14px;}

/* Cards for stats */
.stats{display:flex;justify-content:space-around;margin-top:16px;gap:8px;flex-wrap:wrap;}
.card{background:#111;border:1px solid #222;border-radius:12px;padding:12px;flex:1;min-width:100px;text-align:center;color:#fff;box-shadow:0 0 10px #ff3cac;}
.card h3{margin:0 0 4px;font-size:16px;text-shadow:0 0 3px #00ffff;}
.card p{margin:0;font-size:14px;opacity:.8;}

/* Chat styles */
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:40vh;overflow-y:auto;margin-bottom:10px;}
.msg{background:#222;color:#fff;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;position:relative;box-shadow:0 0 5px #ff3cac;}
.msg .actions{position:absolute;top:4px;right:6px;display:flex;gap:4px;font-size:12px;cursor:pointer;}
.msg .comments{margin-top:6px;padding-left:12px;border-left:2px solid #444;}
.input-row{display:flex;gap:8px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #333;background:#111;color:#fff;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:var(--button-gradient);color:#fff;cursor:pointer;box-shadow:0 0 5px #ff3cac,0 0 10px #00ffff;}

/* Users / Groups */
.user,.group{display:flex;align-items:center;gap:8px;margin-bottom:10px;cursor:pointer;padding:6px;border-radius:10px;}
.user:hover,.group:hover{background:linear-gradient(45deg,#ff3cac,#00ffff);color:#000;}
.dot{width:10px;height:10px;background:#22c55e;border-radius:50%;}

/* Navigation */
nav{display:flex;justify-content:space-around;background:#111;padding:10px 0;box-shadow:0 -2px 10px #00ffff;}
nav button{background:none;border:none;font-size:20px;color:var(--muted);cursor:pointer;}
nav button.active{color:#ff3cac;}

/* Login card */
.login-card{background:#111;padding:16px;border-radius:16px;box-shadow:0 0 10px #ff3cac;}
.login-card input{width:100%;padding:12px;border-radius:12px;margin-bottom:8px;border:1px solid #333;background:#111;color:#fff;}
.login-card button{width:100%;padding:12px;border:none;border-radius:12px;background:var(--button-gradient);color:#fff;cursor:pointer;box-shadow:0 0 5px #ff3cac;}

/* Comment input */
.comment-input{margin-top:4px;display:flex;gap:4px;}
.comment-input input{flex:1;padding:6px;border-radius:8px;border:1px solid #333;background:#111;color:#fff;font-size:12px;}
.comment-input button{padding:6px 8px;border:none;border-radius:8px;background:var(--button-gradient);color:#fff;font-size:12px;cursor:pointer;box-shadow:0 0 5px #ff3cac,0 0 10px #00ffff;}

/* About / Contact */
h3{text-shadow:0 0 5px #ff3cac;}
a{color:#00ffff;text-decoration:none;}
a:hover{color:#ff3cac;}
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

<!-- DASHBOARD / HOME PAGE -->
<div id="home" class="page">
<div class="hero">
<h2>Welcome, <span id="currentUserDisplay">User</span> 🚀</h2>
<p>Fast • Secure • Neon Chat Platform</p>
</div>
<div class="stats">
<div class="card"><h3 id="onlineCount">0</h3><p>Online Users</p></div>
<div class="card"><h3 id="groupCount">0</h3><p>Groups</p></div>
<div class="card"><h3 id="msgCount">0</h3><p>Messages Sent</p></div>
</div>
<p style="margin-top:16px;color:#aaa;">Connect instantly, chat privately or in groups, like & comment messages in real-time.</p>
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
<p><b>Live Connect</b> is a premium neon chat platform designed for fast, secure, and real-time communication.</p>
<ul>
<li>Founded: 2026</li>
<li>Mission: Connect people globally instantly</li>
<li>Vision: Modern neon chat experience</li>
<li>Features: Private chat, group chat, likes & comments, dark/neon mode</li>
</ul>
</div>

<!-- CONTACT PAGE -->
<div id="contact" class="page">
<h3>Contact & Support</h3>
<form id="contactForm">
<input type="text" placeholder="Name" required style="width:100%;padding:8px;margin-bottom:6px;border-radius:8px;border:1px solid #333;background:#111;color:#fff;">
<input type="email" placeholder="Email" required style="width:100%;padding:8px;margin-bottom:6px;border-radius:8px;border:1px solid #333;background:#111;color:#fff;">
<textarea placeholder="Message" required style="width:100%;padding:8px;margin-bottom:6px;border-radius:8px;border:1px solid #333;background:#111;color:#fff;"></textarea>
<button type="submit" style="padding:10px 16px;border:none;border-radius:12px;background:linear-gradient(45deg,#ff3cac,#00ffff,#ff007f);color:#fff;cursor:pointer;box-shadow:0 0 5px #ff3cac,0 0 10px #00ffff;">Send Message</button>
</form>
<p style="margin-top:8px;"><i class="fa-solid fa-envelope"></i> webhub262@gmail.com</p>
<p>
<a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank"><i class="fab fa-facebook"></i> Facebook</a> | 
<a href="https://www.instagram.com/mr_nazim073" target="_blank"><i class="fab fa-instagram"></i> Instagram</a> | 
<a href="https://youtube.com/@crazykhantv" target="_blank"><i class="fab fa-youtube"></i> YouTube</a>
</p>
</div>

<!-- NAVIGATION -->
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
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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
const onlineCountDisplay=document.getElementById("onlineCount");
const groupCountDisplay=document.getElementById("groupCount");
const msgCountDisplay=document.getElementById("msgCount");
const currentUserDisplay=document.getElementById("currentUserDisplay");
let msgCounter=0;

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser=uname;
  currentUserDisplay.textContent=currentUser;
  set(ref(db,"users/"+uname),{name:uname,online:true});
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
  updateStats();
};

window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

// Online users (only currently logged-in)
onValue(ref(db,"users"),snap=>{
  userList.innerHTML="";
  let onlineCount=0;
  snap.forEach(u=>{
    if(u.val().online){
      onlineCount++;
      if(u.key!==currentUser) {
        const d=document.createElement("div");
        d.className="user";
        d.innerHTML=`<div class="dot"></div>${u.val().name}`;
        d.onclick=()=>{curChat=u.key; isGroup=false; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'));};
        userList.appendChild(d);
      }
    }
  });
  onlineCountDisplay.textContent=onlineCount;
});

// Groups
onValue(ref(db,"groups"),snap=>{
  groupList.innerHTML="";
  let gCount=0;
  snap.forEach(g=>{
    gCount++;
    const d=document.createElement("div");
    d.className="group";
    d.textContent=g.key;
    d.onclick=()=>{curChat=g.key; isGroup=true; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'));};
    groupList.appendChild(d);
  });
  groupCountDisplay.textContent=gCount;
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
  msgCounter++;
  msgCountDisplay.textContent=msgCounter;
  input.value='';
};

window.deleteMsg=(path,key)=>{remove(ref(db,path+"/"+key));};

window.addComment=(path,key,btn)=>{
  const input=btn.previousElementSibling;
  if(!input.value) return;
  push(ref(db,path+"/"+key+"/comments"),{from:currentUser,text:input.value});
  input.value='';
};

function updateStats(){
  // This can include more detailed stats in future
}
</script>
</body>
</html>
