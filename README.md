<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0a0a0f;
  --card:#11121b;
  --text:#e0e0f0;
  --muted:#8888aa;
  --primary:#8e44ad;
  --gradient: linear-gradient(135deg,#ff6ec4,#7873f5,#00d2ff);
  --glow: 0 0 10px #ff6ec4,0 0 20px #7873f5,0 0 30px #00d2ff;
}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
body.dark{--bg:#0a0a0f;--card:#11121b;--text:#e0e0f0;}
.app{max-width:480px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 2px 10px rgba(0,0,0,.5);}
header h1{margin:0;font-size:20px;color:#fff;text-shadow:var(--glow);}
header button{background:none;border:none;font-size:18px;color:var(--text);}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:var(--card);border-radius:18px;padding:24px;text-align:center;box-shadow:0 0 20px rgba(0,255,255,.2);}
.hero h2{margin:0 0 8px;color:#fff;text-shadow:var(--glow);}
.hero p{margin:0;color:var(--muted);}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:40vh;overflow-y:auto;margin-bottom:10px;box-shadow:0 0 20px rgba(0,255,255,.1);}
.msg{background:#1a1a2e;color:#fff;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;position:relative;box-shadow:0 0 5px rgba(0,255,255,.2);}
.msg .actions{position:absolute;top:4px;right:6px;display:flex;gap:4px;font-size:12px;cursor:pointer;}
.msg .comments{margin-top:6px;padding-left:12px;border-left:2px solid #333;}
.input-row{display:flex;gap:8px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #555;background:#0a0a0f;color:#fff;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:var(--gradient);color:#fff;font-weight:bold;text-shadow:var(--glow);}
.user{display:flex;align-items:center;gap:8px;margin-bottom:10px;cursor:pointer;}
.dot{width:10px;height:10px;background:#22c55e;border-radius:50%;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:0 -2px 10px rgba(0,0,0,.5);}
nav button{background:none;border:none;font-size:20px;color:var(--muted);}
nav button.active{color:#00ffff;text-shadow:var(--glow);}
.login-card{background:var(--card);padding:16px;border-radius:16px;box-shadow:0 0 20px rgba(0,255,255,.2);}
.login-card input{width:100%;padding:12px;border-radius:12px;margin-bottom:8px;border:1px solid #555;background:#0a0a0f;color:#fff;}
.login-card button{width:100%;padding:12px;border:none;border-radius:12px;background:var(--gradient);color:#fff;font-weight:bold;text-shadow:var(--glow);}
.group{background:#1a1a2e;padding:8px;margin-bottom:6px;border-radius:12px;cursor:pointer;box-shadow:0 0 10px rgba(255,0,255,.1);}
.group:hover{background:#2a2a3e;}
.comment-input{margin-top:4px;display:flex;gap:4px;}
.comment-input input{flex:1;padding:6px;border-radius:8px;border:1px solid #555;font-size:12px;background:#0a0a0f;color:#fff;}
.comment-input button{padding:6px 8px;border:none;border-radius:8px;background:var(--gradient);color:#fff;font-size:12px;text-shadow:var(--glow);}
.social a{margin-right:12px;color:#fff;text-decoration:none;font-size:20px;text-shadow:var(--glow);}
.social a:hover{color:#ff6ec4;}
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

<!-- HOME -->
<div id="home" class="page">
<div class="hero">
<h2>Real-Time Chat 🚀</h2>
<p>Fast • Secure • Mobile Friendly</p>
</div>
<p style="margin-top:16px;color:var(--muted)">Connect instantly, see who’s online, chat privately or in groups, like and comment messages in real-time.</p>
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
<p>Live Connect is a premium real-time chat platform for teams and communities.</p>
<ul>
<li>Private & group chats</li>
<li>Real-time online users</li>
<li>Comments & reactions on messages</li>
<li>Dark/Light mode support</li>
<li>Secure & modern interface</li>
</ul>
<div class="social">
<a href="https://www.facebook.com/yourprofile" target="_blank"><i class="fab fa-facebook"></i></a>
<a href="https://www.instagram.com/yourprofile" target="_blank"><i class="fab fa-instagram"></i></a>
<a href="https://youtube.com/@yourchannel" target="_blank"><i class="fab fa-youtube"></i></a>
<a href="mailto:webhub262@gmail.com"><i class="fa-solid fa-envelope"></i></a>
</div>
</div>

<!-- CONTACT -->
<div id="contact" class="page">
<h3>Contact & Support</h3>
<p>For any queries, reach out to us:</p>
<ul>
<li>Email: <a href="mailto:webhub262@gmail.com">webhub262@gmail.com</a></li>
<li>Facebook: <a href="https://www.facebook.com/yourprofile" target="_blank">Facebook</a></li>
<li>Instagram: <a href="https://www.instagram.com/yourprofile" target="_blank">Instagram</a></li>
<li>YouTube: <a href="https://youtube.com/@yourchannel" target="_blank">YouTube</a></li>
</ul>
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
import { getDatabase, ref, set, push, onValue, remove, onDisconnect, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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
  // Auto set offline if disconnect
  onDisconnect(userRef).update({online:false});
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

// LOGOUT (optional)
window.logout=()=>{
  if(currentUser){
    set(ref(db,"users/"+currentUser),{name:currentUser,online:false});
    currentUser=null;
    document.getElementById("home").classList.remove("active");
    document.getElementById("loginPage").classList.add("active");
  }
};

// PAGE SWITCH
window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

// Online users
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

// Send message
window.sendMsg=()=>{
  if(!curChat) return alert("Select a user/group first");
  const input=document.getElementById('msgInput');
  if(!input.value) return;
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  push(ref(db,path),{from:currentUser,text:input.value});
  input.value='';
};

// Delete message
window.deleteMsg=(path,key)=>{remove(ref(db,path+"/"+key));};

// Add comment
window.addComment=(path,key,btn)=>{
  const input=btn.previousElementSibling;
  if(!input.value) return;
  push(ref(db,path+"/"+key+"/comments"),{from:currentUser,text:input.value});
  input.value='';
};
</script>
</body>
</html>
