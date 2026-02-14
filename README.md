<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0c0c0c;--card:#111;--text:#fff;--muted:#aaa;--primary:#ff3c96;--secondary:#3c8eff;--accent:#b93eff;
}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
body.dark{--bg:#0b0b0b;--card:#111827;--text:#e5e7eb;}
.app{max-width:420px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 0 20px #000;}
header h1{margin:0;font-size:20px;text-shadow:0 0 8px var(--primary);}
header button{background:none;border:none;font-size:18px;color:var(--text);}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,var(--primary),var(--secondary),var(--accent));color:#fff;padding:24px;border-radius:18px;text-align:center;box-shadow:0 0 20px var(--accent);}
.hero h2{text-shadow:0 0 12px #fff;}
.hero p{opacity:.9;}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:35vh;overflow-y:auto;margin-bottom:10px;box-shadow:0 0 15px #000;}
.msg{background:#222;color:#fff;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;position:relative;box-shadow:0 0 10px #000;}
.msg .actions{position:absolute;top:4px;right:6px;display:flex;gap:4px;font-size:12px;cursor:pointer;}
.msg .comments{margin-top:6px;padding-left:12px;border-left:2px solid #444;}
.input-row{display:flex;gap:8px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #555;background:#111;color:#fff;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:linear-gradient(45deg,var(--primary),var(--secondary),var(--accent));color:#fff;text-shadow:0 0 6px #fff;cursor:pointer;transition:0.3s;}
.input-row button:hover{transform:scale(1.05);}
.user{display:flex;align-items:center;gap:8px;margin-bottom:10px;cursor:pointer;}
.dot{width:10px;height:10px;background:#22c55e;border-radius:50%;box-shadow:0 0 5px #22c55e;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:0 -2px 15px #000;}
nav button{background:none;border:none;font-size:20px;color:var(--muted);}
nav button.active{color:var(--primary);text-shadow:0 0 6px var(--primary);}
.login-card{background:var(--card);padding:16px;border-radius:16px;box-shadow:0 0 20px #000;}
.login-card input{width:100%;padding:12px;border-radius:12px;margin-bottom:8px;border:1px solid #555;background:#111;color:#fff;}
.login-card button{width:100%;padding:12px;border:none;border-radius:12px;background:linear-gradient(45deg,var(--primary),var(--secondary),var(--accent));color:#fff;text-shadow:0 0 6px #fff;cursor:pointer;}
.group{background:var(--card);padding:8px;margin-bottom:6px;border-radius:12px;cursor:pointer;box-shadow:0 0 5px #000;}
.group:hover{background:linear-gradient(45deg,var(--primary),var(--secondary));color:#fff;}
.comment-input{margin-top:4px;display:flex;gap:4px;}
.comment-input input{flex:1;padding:6px;border-radius:8px;border:1px solid #555;background:#111;color:#fff;font-size:12px;}
.comment-input button{padding:6px 8px;border:none;border-radius:8px;background:linear-gradient(45deg,var(--primary),var(--secondary),var(--accent));color:#fff;font-size:12px;cursor:pointer;}
.dashboard-card{background:#111;padding:12px;margin-bottom:10px;border-radius:14px;box-shadow:0 0 15px #000;text-align:center;}
.dashboard-card h4{text-shadow:0 0 8px var(--secondary);}
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

<!-- DASHBOARD -->
<div id="home" class="page">
<div class="hero">
<h2>Welcome, <span id="dashUser">User</span>!</h2>
<p>Real-Time Chat Dashboard</p>
</div>
<div class="dashboard-card">
<h4>Total Online Users: <span id="totalOnline">0</span></h4>
<h4>Total Groups: <span id="totalGroups">0</span></h4>
</div>
<div style="text-align:center;margin-top:10px;">
<button onclick="openPage('chat',document.querySelector('nav button:nth-child(2)'))">Go to Chat</button>
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
<p>Live Connect is a premium chat platform, built for speed, security and real-time interaction. Our mission is to connect people instantly and safely.</p>
<ul>
<li>Fast & Secure Chat</li>
<li>Private & Group Messaging</li>
<li>Dark Mode & Neon Interface</li>
<li>Real-time Online Users</li>
<li>Premium Neon Dashboard</li>
</ul>
</div>

<!-- CONTACT -->
<div id="contact" class="page">
<h3>Contact & Support</h3>
<form id="contactForm">
<input type="text" id="contactName" placeholder="Your Name" required style="width:100%;padding:10px;margin-bottom:6px;border-radius:8px;background:#111;border:1px solid #555;color:#fff;">
<input type="email" id="contactEmail" placeholder="Your Email" required style="width:100%;padding:10px;margin-bottom:6px;border-radius:8px;background:#111;border:1px solid #555;color:#fff;">
<textarea id="contactMsg" placeholder="Your Message" required style="width:100%;padding:10px;margin-bottom:6px;border-radius:8px;background:#111;border:1px solid #555;color:#fff;"></textarea>
<button type="submit" style="width:100%;padding:12px;border:none;border-radius:12px;background:linear-gradient(45deg,var(--primary),var(--secondary),var(--accent));color:#fff;text-shadow:0 0 6px #fff;cursor:pointer;">Send Message</button>
</form>
<p style="margin-top:10px;">Or reach out on social media:</p>
<p>
<a href="https://facebook.com" target="_blank">Facebook</a> | 
<a href="https://instagram.com" target="_blank">Instagram</a> | 
<a href="https://youtube.com" target="_blank">YouTube</a> | 
<a href="mailto:webhub262@gmail.com">Email</a>
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
const dashUser=document.getElementById("dashUser");
const totalOnline=document.getElementById("totalOnline");
const totalGroups=document.getElementById("totalGroups");

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser=uname;
  dashUser.textContent=uname;
  set(ref(db,"users/"+uname),{name:uname,online:true});
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
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
  let onlineCount=0;
  userList.innerHTML=""; groupList.innerHTML="";
  snap.forEach(u=>{
    if(u.val().online && u.key!==currentUser){
      onlineCount++;
      const d=document.createElement("div");
      d.className="user";
      d.innerHTML=`<div class="dot"></div>${u.val().name}`;
      d.onclick=()=>{curChat=u.key; isGroup=false; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'));};
      userList.appendChild(d);
    }
  });
  totalOnline.textContent=onlineCount+1; // including current user
});

// Groups
onValue(ref(db,"groups"),snap=>{
  groupList.innerHTML="";
  let groupCount=0;
  snap.forEach(g=>{
    groupCount++;
    const d=document.createElement("div");
    d.className="group";
    d.textContent=g.key;
    d.onclick=()=>{curChat=g.key; isGroup=true; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'));};
    groupList.appendChild(d);
  });
  totalGroups.textContent=groupCount;
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

// Contact form submit
document.getElementById('contactForm').addEventListener('submit',e=>{
  e.preventDefault();
  alert('Message sent! We will contact you soon.');
  e.target.reset();
});
</script>
</body>
</html>
