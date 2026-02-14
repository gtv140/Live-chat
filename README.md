<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0a0a1f; --card:#111; --text:#eee; --muted:#aaa;
  --neon1:#ff6ec7; --neon2:#6ec1ff; --neon3:#b366ff;
  --primary:#ff00ff;
}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
.app{max-width:480px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 0 10px rgba(255,255,255,.1);}
header h1{margin:0;font-size:22px;text-shadow:0 0 5px var(--neon1);}
header button{background:none;border:none;font-size:18px;color:var(--text);}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,var(--neon1),var(--neon2));color:#fff;padding:24px;border-radius:18px;text-align:center;box-shadow:0 0 15px rgba(255,110,199,.4);}
.hero h2{text-shadow:0 0 10px var(--neon3);}
.hero p{opacity:.9;}
.dashboard{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-top:16px;}
.card{background:#111;padding:14px;border-radius:16px;text-align:center;box-shadow:0 0 10px rgba(255,255,255,.1);transition:0.3s;}
.card:hover{box-shadow:0 0 20px var(--neon2);transform:translateY(-3px);}
.card h3{margin:0;color:var(--neon1);}
.card p{margin:4px 0;color:#eee;}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:40vh;overflow-y:auto;margin-bottom:10px;box-shadow:0 0 10px rgba(255,255,255,.05);}
.msg{background:#1a1a2e;color:#eee;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;position:relative;box-shadow:0 0 5px rgba(255,255,255,.1);}
.msg .actions{position:absolute;top:4px;right:6px;display:flex;gap:4px;font-size:12px;cursor:pointer;}
.msg .comments{margin-top:6px;padding-left:12px;border-left:2px solid #333;}
.input-row{display:flex;gap:8px;margin-top:6px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #444;background:#111;color:#eee;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:linear-gradient(45deg,var(--neon1),var(--neon2),var(--neon3));color:#fff;box-shadow:0 0 10px var(--neon1);}
.user{display:flex;align-items:center;gap:8px;margin-bottom:10px;cursor:pointer;}
.dot{width:10px;height:10px;background:#22c55e;border-radius:50%;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:0 -2px 10px rgba(255,255,255,.05);}
nav button{background:none;border:none;font-size:20px;color:var(--muted);}
nav button.active{color:var(--neon1);}
.login-card{background:var(--card);padding:16px;border-radius:16px;box-shadow:0 0 10px rgba(255,255,255,.05);}
.login-card input{width:100%;padding:12px;border-radius:12px;margin-bottom:8px;border:1px solid #444;background:#111;color:#eee;}
.login-card button{width:100%;padding:12px;border:none;border-radius:12px;background:linear-gradient(45deg,var(--neon1),var(--neon2),var(--neon3));color:#fff;box-shadow:0 0 10px var(--neon1);}
.group{background:#111;padding:8px;margin-bottom:6px;border-radius:12px;cursor:pointer;box-shadow:0 0 5px rgba(255,255,255,.05);}
.group:hover{background:linear-gradient(45deg,var(--neon1),var(--neon2),var(--neon3));color:#fff;}
.comment-input{margin-top:4px;display:flex;gap:4px;}
.comment-input input{flex:1;padding:6px;border-radius:8px;border:1px solid #444;background:#111;color:#eee;font-size:12px;}
.comment-input button{padding:6px 8px;border:none;border-radius:8px;background:linear-gradient(45deg,var(--neon1),var(--neon2),var(--neon3));color:#fff;font-size:12px;}
.contact-card{background:#111;padding:16px;border-radius:16px;box-shadow:0 0 10px rgba(255,255,255,.1);}
.contact-card input, .contact-card textarea{width:100%;padding:12px;border-radius:12px;margin-bottom:8px;border:1px solid #444;background:#111;color:#eee;}
.contact-card button{width:100%;padding:12px;border:none;border-radius:12px;background:linear-gradient(45deg,var(--neon1),var(--neon2),var(--neon3));color:#fff;box-shadow:0 0 10px var(--neon1);}
</style>
</head>
<body>
<div class="app">
<header>
<h1>Live Connect</h1>
<button onclick="document.body.classList.toggle('dark')"><i class="fa-solid fa-moon"></i></button>
</header>

<!-- LOGIN -->
<div id="loginPage" class="page active">
<div class="login-card">
<h3>Enter Username</h3>
<input type="text" id="usernameInput" placeholder="Username">
<button onclick="login()">Continue</button>
</div>
</div>

<!-- HOME DASHBOARD -->
<div id="home" class="page">
<div class="hero">
<h2>Welcome to Live Connect</h2>
<p id="welcomeUser">Loading...</p>
</div>
<div class="dashboard" id="dashboardCards">
<div class="card"><h3>Online Users</h3><p id="onlineCount">0</p></div>
<div class="card"><h3>Total Groups</h3><p id="groupCount">0</p></div>
<div class="card"><h3>Recent Messages</h3><p id="recentMsgs">0</p></div>
<div class="card"><h3>My Username</h3><p id="currentUserDisplay">---</p></div>
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

<!-- USERS -->
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
<p>Live Connect is a futuristic chat platform designed for fast, secure, and interactive communication. Our company is dedicated to delivering the most immersive real-time messaging experience.</p>
<p>Connect with us on social media:</p>
<p>
<a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank"><i class="fab fa-facebook"></i> Facebook</a> | 
<a href="https://www.instagram.com/mr_nazim073" target="_blank"><i class="fab fa-instagram"></i> Instagram</a> | 
<a href="https://youtube.com/@crazykhantv" target="_blank"><i class="fab fa-youtube"></i> YouTube</a> | 
<a href="mailto:webhub262@gmail.com"><i class="fa-solid fa-envelope"></i> Email</a>
</p>
</div>

<!-- CONTACT -->
<div id="contact" class="page">
<div class="contact-card">
<h3>Contact Us</h3>
<input type="text" placeholder="Your Name" id="contactName">
<input type="email" placeholder="Your Email" id="contactEmail">
<textarea placeholder="Message" id="contactMsg" rows="4"></textarea>
<button onclick="sendContact()">Send Message</button>
</div>
</div>

<!-- NAV -->
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
const welcomeUser=document.getElementById("welcomeUser");
const onlineCount=document.getElementById("onlineCount");
const groupCount=document.getElementById("groupCount");
const recentMsgs=document.getElementById("recentMsgs");
const currentUserDisplay=document.getElementById("currentUserDisplay");

// LOGIN
window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser=uname;
  set(ref(db,"users/"+uname),{name:uname,online:true});
  currentUserDisplay.textContent=uname;
  welcomeUser.textContent=`Hello, ${uname}!`;
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

// NAV
window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

// ONLINE USERS
onValue(ref(db,"users"),snap=>{
  userList.innerHTML=""; 
  let count=0;
  snap.forEach(u=>{
    if(u.val().online && u.key!==currentUser){
      count++;
      const d=document.createElement("div");
      d.className="user";
      d.innerHTML=`<div class="dot"></div>${u.val().name}`;
      d.onclick=()=>{curChat=u.key; isGroup=false; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'));};
      userList.appendChild(d);
    }
  });
  onlineCount.textContent=count;
});

// GROUPS
onValue(ref(db,"groups"),snap=>{
  groupList.innerHTML="";
  groupCount.textContent = snap.size;
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
    recentMsgs.textContent = snap.size;
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

// DELETE & COMMENT
window.deleteMsg=(path,key)=>{remove(ref(db,path+"/"+key));};
window.addComment=(path,key,btn)=>{
  const input=btn.previousElementSibling;
  if(!input.value) return;
  push(ref(db,path+"/"+key+"/comments"),{from:currentUser,text:input.value});
  input.value='';
};

// CONTACT FORM
window.sendContact=()=>{
  const n=document.getElementById("contactName").value.trim();
  const e=document.getElementById("contactEmail").value.trim();
  const m=document.getElementById("contactMsg").value.trim();
  if(!n || !e || !m){alert("Please fill all fields"); return;}
  push(ref(db,"contactMessages"),{name:n,email:e,message:m});
  alert("Message sent! We will contact you soon.");
  document.getElementById("contactName").value='';
  document.getElementById("contactEmail").value='';
  document.getElementById("contactMsg").value='';
};
</script>
</body>
</html>
