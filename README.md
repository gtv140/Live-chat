<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#f6f7fb; --card:#fff; --text:#111827; --muted:#6b7280; --primary:#2563eb; --success:#22c55e; --danger:#ef4444;
}
body.dark{
  --bg:#0f172a; --card:#111827; --text:#e5e7eb; --muted:#9ca3af; --primary:#3b82f6; --success:#22c55e; --danger:#f87171;
}
*{box-sizing:border-box;margin:0;padding:0;}
body{font-family:system-ui,sans-serif;background:var(--bg);color:var(--text);min-height:100vh;display:flex;flex-direction:column;}
header{display:flex;justify-content:space-between;align-items:center;padding:14px 16px;background:var(--card);box-shadow:0 2px 10px rgba(0,0,0,0.06);}
header h1{font-size:18px;}
header button{background:none;border:none;font-size:18px;color:var(--text);cursor:pointer;}
.app{flex:1;max-width:500px;margin:auto;width:100%;display:flex;flex-direction:column;}
.page{display:none;padding:16px;flex:1;overflow-y:auto;}
.page.active{display:block;}
input,textarea,button{font-family:inherit;font-size:14px;}
input,textarea{width:100%;padding:10px;border-radius:10px;border:1px solid #ccc;margin-bottom:8px;}
button{cursor:pointer;border:none;border-radius:10px;background:var(--primary);color:#fff;}
.hero{background:linear-gradient(135deg,var(--primary),#60a5fa);padding:20px;border-radius:16px;color:#fff;margin-bottom:12px;text-align:center;}
.hero h2{margin-bottom:6px;}
.chat-box{background:var(--card);padding:12px;border-radius:16px;height:35vh;overflow-y:auto;margin-bottom:10px;}
.msg{padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;display:flex;justify-content:space-between;align-items:center;}
.msg.me{background:#dcf8c6;}
.msg.other{background:#fff;}
.input-row{display:flex;gap:6px;}
.input-row input{flex:1;}
.user{display:flex;align-items:center;gap:6px;margin-bottom:8px;}
.dot{width:10px;height:10px;border-radius:50%;background:var(--success);}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:0 -2px 10px rgba(0,0,0,0.06);}
nav button{background:none;border:none;font-size:20px;color:var(--muted);}
nav button.active{color:var(--primary);}
.like-btn{background:none;border:none;color:var(--primary);cursor:pointer;font-size:14px;margin-left:6px;}
</style>
</head>
<body>
<div class="app">

<header>
<h1>Live Connect 🚀</h1>
<button onclick="document.body.classList.toggle('dark')"><i class="fa-solid fa-moon"></i></button>
</header>

<!-- LOGIN PAGE -->
<div id="loginPage" class="page active">
<h3>Enter Username</h3>
<input type="text" id="usernameInput" placeholder="Username">
<button onclick="login()">Continue</button>
</div>

<!-- HOME PAGE -->
<div id="home" class="page">
<div class="hero">
<h2>Welcome, <span id="welcomeUser"></span>!</h2>
<p>Fast • Secure • Real-Time Chat</p>
</div>
<p style="margin-top:8px;color:var(--muted)">Connect instantly, see who’s online, and chat smoothly.</p>
</div>

<!-- CHAT PAGE -->
<div id="chat" class="page">
<div class="chat-box" id="chatBox"></div>
<div class="input-row">
<input id="msgInput" placeholder="Type message...">
<button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
</div>
</div>

<!-- ONLINE USERS -->
<div id="users" class="page">
<h3>Online Users</h3>
<div id="onlineUsers"></div>
</div>

<!-- GROUPS -->
<div id="groups" class="page">
<h3>Groups</h3>
<button onclick="createGroup()">+ Create Group</button>
<div id="groupList"></div>
</div>

<!-- ABOUT -->
<div id="about" class="page">
<h3>About Live Connect 🚀</h3>
<p>Modern real-time chat platform. Mobile-friendly, secure, with group chat, status, like & delete features.</p>
</div>

<!-- CONTACT -->
<div id="contact" class="page">
<h3>Contact & Support</h3>
<p><i class="fa-solid fa-envelope"></i> webhub262@gmail.com</p>
<p>
<a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank">Facebook</a> |
<a href="https://www.instagram.com/mr_nazim073" target="_blank">Instagram</a> |
<a href="https://youtube.com/@crazykhantv" target="_blank">YouTube</a>
</p>
</div>

<nav>
<button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
<button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
<button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
<button onclick="openPage('groups',this)"><i class="fa-solid fa-users"></i></button>
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
  storageBucket: "live-chat-b810c.firebasestorage.app",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = null;
let currentGroup = null;

const chatBox = document.getElementById("chatBox");
const onlineUsersDiv = document.getElementById("onlineUsers");
const welcomeUserSpan = document.getElementById("welcomeUser");
const groupListDiv = document.getElementById("groupList");

function login(){
  const uname = document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser = uname;
  welcomeUserSpan.textContent = currentUser;
  set(ref(db,"users/"+currentUser),{online:true});
  openPage('home', document.querySelector('nav button:first-child'));
  document.getElementById("loginPage").classList.remove('active');
}

function openPage(id,btn){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  if(btn) btn.classList.add('active');
}

// LISTEN ONLINE USERS
onValue(ref(db,"users"),snap=>{
  onlineUsersDiv.innerHTML="";
  snap.forEach(u=>{
    if(u.val().online){
      const div = document.createElement("div");
      div.className="user";
      div.innerHTML=`<div class="dot"></div>${u.key}`;
      onlineUsersDiv.appendChild(div);
    }
  });
});

// GROUPS
onValue(ref(db,"groups"),snap=>{
  groupListDiv.innerHTML="";
  snap.forEach(g=>{
    const div = document.createElement("div");
    div.className="user";
    div.textContent = g.key;
    div.onclick = ()=>openGroup(g.key);
    groupListDiv.appendChild(div);
  });
});

function createGroup(){
  const name = prompt("Enter group name:");
  if(!name) return;
  set(ref(db,"groups/"+name),{owner:currentUser});
}

function openGroup(name){
  currentGroup = name;
  const chatRef = ref(db,"groupChats/"+name);
  onValue(chatRef,snap=>{
    chatBox.innerHTML="";
    snap.forEach(m=>{
      const div = document.createElement("div");
      div.className = m.val().from===currentUser?"msg me":"msg other";
      div.innerHTML = `<b>${m.val().from}:</b> ${m.val().text} 
      ${m.val().from===currentUser?'<button onclick="deleteMsg(\''+m.key+'\',\''+name+'\')" style="background:none;border:none;color:red;cursor:pointer;">🗑️</button>':''}
      <button class="like-btn" onclick="likeMsg('${m.key}','${name}')">❤️</button>`;
      chatBox.appendChild(div);
      chatBox.scrollTop = chatBox.scrollHeight;
    });
  });
}

function sendMsg(){
  const input = document.getElementById("msgInput");
  if(!input.value) return;
  if(currentGroup){
    push(ref(db,"groupChats/"+currentGroup),{from:currentUser,text:input.value,likes:0});
    input.value="";
  }
}

function deleteMsg(key,group){
  remove(ref(db,"groupChats/"+group+"/"+key));
}

function likeMsg(key,group){
  // This is just a placeholder; likes can be implemented with proper update
  alert("You liked this message!");
}
</script>
</body>
</html>
