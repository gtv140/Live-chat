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
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
.app{max-width:420px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 2px 10px rgba(0,0,0,.06);}
header h1{margin:0;font-size:18px;}
header button{background:none;border:none;font-size:18px;color:var(--text);}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,var(--primary),#60a5fa);color:#fff;padding:24px;border-radius:18px;}
.hero h2{margin:0 0 8px;}
.hero p{margin:0;opacity:.9;}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:40vh;overflow-y:auto;margin-bottom:10px;}
.msg{background:#e5e7eb;color:#000;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;position:relative;}
body.dark .msg{background:#1f2937;color:#e5e7eb;}
.msg .actions{position:absolute;top:4px;right:6px;display:flex;gap:4px;font-size:12px;}
.input-row{display:flex;gap:8px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #ccc;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:var(--primary);color:#fff;}
.user{display:flex;align-items:center;gap:8px;margin-bottom:10px;cursor:pointer;}
.dot{width:10px;height:10px;background:#22c55e;border-radius:50%;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:0 -2px 10px rgba(0,0,0,.06);}
nav button{background:none;border:none;font-size:20px;color:var(--muted);}
nav button.active{color:var(--primary);}
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
<div style="background:var(--card);padding:16px;border-radius:16px;box-shadow:0 2px 10px rgba(0,0,0,.06);">
<h3>Enter Username</h3>
<input type="text" id="usernameInput" placeholder="Username" style="width:100%;padding:12px;border-radius:12px;margin-bottom:8px;border:1px solid #ccc;">
<button onclick="login()" style="width:100%;padding:12px;border:none;border-radius:12px;background:var(--primary);color:#fff;">Login</button>
</div>
</div>

<!-- HOME PAGE -->
<div id="home" class="page">
<div class="hero">
<h2>Real-Time Chat</h2>
<p>Fast • Secure • Mobile Friendly</p>
</div>
<p style="margin-top:16px;color:var(--muted)">
Connect instantly, see who’s online, chat in private or group, and react to messages.
</p>
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
</div>

<!-- ABOUT PAGE -->
<div id="about" class="page">
<h3>About Live Connect 🚀</h3>
<p>Live Connect is a modern chat platform with private & group chat, online status, likes, comments, and full mobile support.</p>
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

const chatBox=document.getElementById("chatBox");
const userList=document.getElementById("userList");

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser=uname;
  set(ref(db,"users/"+uname),{name:uname,online:true});
  openPage('home',document.querySelector('nav button'));
};

window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

// Online Users
onValue(ref(db,"users"),snap=>{
  userList.innerHTML="";
  snap.forEach(u=>{
    if(u.val().online && u.key!==currentUser){
      const d=document.createElement("div");
      d.className="user";
      d.innerHTML=`<div class="dot"></div>${u.val().name}`;
      d.onclick=()=>{curChat=u.key; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'))};
      userList.appendChild(d);
    }
  });
});

// Chat
function loadChat(){
  const path="chats/"+[currentUser,curChat].sort().join("_");
  onValue(ref(db,path),snap=>{
    chatBox.innerHTML="";
    snap.forEach(m=>{
      const div=document.createElement("div");
      div.className="msg";
      div.innerHTML=`<b>${m.val().from}:</b> ${m.val().text} <div class="actions">
        <span onclick="likeMsg('${path}','${m.key}')">👍</span>
        <span onclick="deleteMsg('${path}','${m.key}')">🗑️</span>
      </div>`;
      chatBox.appendChild(div);
      chatBox.scrollTop=chatBox.scrollHeight;
    });
  });
}

function sendMsg(){
  if(!curChat) return alert("Select user from Online Users");
  const input=document.getElementById('msgInput');
  if(!input.value) return;
  const path="chats/"+[currentUser,curChat].sort().join("_");
  push(ref(db,path),{from:currentUser,text:input.value});
  input.value='';
}

// Like & Delete
function likeMsg(path,key){
  // Here you can implement like functionality if needed
  alert("Liked message!");
}
function deleteMsg(path,key){
  remove(ref(db,path+"/"+key));
}
</script>
</body>
</html>
