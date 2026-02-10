<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css"/>
<style>
:root{
  --bg:#f6f7fb; --card:#fff; --text:#111827; --muted:#6b7280; --primary:#2563eb;
}
body.dark{
  --bg:#0f172a; --card:#111827; --text:#e5e7eb; --muted:#9ca3af; --primary:#3b82f6;
}
*{box-sizing:border-box}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
.app{max-width:420px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 2px 10px rgba(0,0,0,.06);}
header h1{font-size:18px;margin:0;}
header button{background:none;border:none;font-size:18px;color:var(--text);}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}

/* Login Attractive */
#login{display:flex;flex-direction:column;align-items:center;justify-content:center;}
#login h2{margin-bottom:16px;color:var(--primary);}
#login input{padding:12px;border-radius:12px;border:1px solid #ccc;width:80%;margin-bottom:12px;font-size:16px;}
#login button{padding:12px 20px;border:none;border-radius:12px;background:var(--primary);color:#fff;font-size:16px;cursor:pointer;transition:0.3s;}
#login button:hover{opacity:0.9;transform:scale(1.02);}

/* Home */
.hero{background:linear-gradient(135deg,var(--primary),#60a5fa);color:#fff;padding:24px;border-radius:18px;text-align:center;margin-bottom:12px;}
.hero h2{margin:0 0 8px;}
.hero p{margin:0;opacity:.9;}

/* Chat */
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:50vh;overflow-y:auto;margin-bottom:10px;}
.msg{background:#e5e7eb;color:#000;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;}
body.dark .msg{background:#1f2937;color:#e5e7eb;}
.input-row{display:flex;gap:8px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #ccc;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:var(--primary);color:#fff;}

/* Users */
.user{display:flex;align-items:center;gap:8px;margin-bottom:10px;cursor:pointer;}
.dot{width:10px;height:10px;background:#22c55e;border-radius:50%;}

/* Bottom Nav */
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:0 -2px 10px rgba(0,0,0,.06);}
nav button{background:none;border:none;font-size:20px;color:#6b7280;}
nav button.active{color:var(--primary);}
</style>
</head>
<body>

<div class="app">

<header>
  <h1>Live Connect</h1>
  <button onclick="toggleDark()"><i class="fa-solid fa-moon"></i></button>
</header>

<!-- LOGIN -->
<div id="login" class="page active">
  <h2>Welcome to Live Connect 🚀</h2>
  <input type="text" id="usernameInput" placeholder="Enter your username" />
  <button onclick="login()">Continue</button>
</div>

<!-- HOME -->
<div id="home" class="page">
  <div class="hero">
    <h2>Real-Time Live Chat</h2>
    <p>Fast • Secure • Mobile Friendly</p>
  </div>
  <p style="color:var(--muted)">Connect instantly, see who’s online, chat in private or groups, like messages, and manage your profile easily.</p>
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
  <div id="currentUsers"></div>
</div>

<nav>
  <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
  <button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
  <button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
</nav>

</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, onValue, push } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let currentUser=null, curChat=null;

// Login
window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username"); return;}
  currentUser=uname;
  set(ref(db,"users/"+uname),{name:uname,online:true});
  document.getElementById("login").classList.remove("active");
  document.getElementById("home").classList.add("active");
  listenUsers();
};

// Page Nav
window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

// Dark Mode
window.toggleDark=()=>{document.body.classList.toggle('dark');};

// Chat
window.sendMsg=()=>{
  if(!curChat) return alert("Select user to chat");
  const input=document.getElementById('msgInput');
  if(!input.value) return;
  const path="chats/"+[currentUser,curChat].sort().join("_");
  push(ref(db,path),{from:currentUser,text:input.value});
  input.value='';
};

// Online Users
function listenUsers(){
  const usersDiv=document.getElementById("currentUsers");
  onValue(ref(db,"users"),snap=>{
    usersDiv.innerHTML="";
    snap.forEach(u=>{
      if(u.val().online && u.key!==currentUser){
        const div=document.createElement("div");
        div.className="user";
        div.innerHTML=`<div class="dot"></div>${u.val().name}`;
        div.onclick=()=>{
          curChat=u.key;
          openPage('chat',document.querySelector('nav button:nth-child(2)'));
          listenChat(curChat);
        };
        usersDiv.appendChild(div);
      }
    });
  });
}

function listenChat(user){
  const chatBox=document.getElementById("chatBox");
  const path="chats/"+[currentUser,user].sort().join("_");
  onValue(ref(db,path),snap=>{
    chatBox.innerHTML="";
    snap.forEach(m=>{
      const div=document.createElement("div");
      div.className="msg";
      div.innerHTML=`<b>${m.val().from}:</b> ${m.val().text}`;
      chatBox.appendChild(div);
      chatBox.scrollTop=chatBox.scrollHeight;
    });
  });
}
</script>
</body>
</html>
