<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<!-- Font Awesome -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#f6f7fb;--card:#fff;--text:#111827;--muted:#6b7280;--primary:#2563eb;
}
body.dark{--bg:#0f172a;--card:#111827;--text:#e5e7eb;--muted:#9ca3af;--primary:#3b82f6;}
*{box-sizing:border-box;}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
.app{max-width:420px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 2px 10px rgba(0,0,0,.06);}
header h1{font-size:18px;margin:0;}
header button{background:none;border:none;font-size:18px;color:var(--text);cursor:pointer;}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,var(--primary),#60a5fa);color:#fff;padding:24px;border-radius:18px;text-align:center;margin-bottom:12px;}
.hero h2{margin:0 0 8px;font-size:22px;}
.hero p{margin:0;font-size:14px;opacity:.9;}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:40vh;overflow-y:auto;margin-bottom:10px;}
.msg{background:#e5e7eb;color:#000;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;position:relative;}
body.dark .msg{background:#1f2937;color:#e5e7eb;}
.msg button{position:absolute;top:4px;right:4px;background:none;border:none;color:red;cursor:pointer;}
.input-row{display:flex;gap:8px;margin-bottom:12px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #ccc;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:var(--primary);color:#fff;cursor:pointer;}
.user{display:flex;align-items:center;gap:8px;margin-bottom:10px;}
.dot{width:10px;height:10px;background:#22c55e;border-radius:50%;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:0 -2px 10px rgba(0,0,0,.06);}
nav button{background:none;border:none;font-size:20px;color:var(--muted);cursor:pointer;}
nav button.active{color:var(--primary);}
.login-card{background:var(--card);padding:20px;border-radius:16px;box-shadow:0 4px 12px rgba(0,0,0,.1);text-align:center;margin-top:50px;}
.login-card input{width:80%;padding:12px;margin:8px 0;border-radius:12px;border:1px solid #ccc;}
.login-card button{padding:12px 20px;border:none;border-radius:12px;background:var(--primary);color:#fff;cursor:pointer;font-size:16px;}
a{text-decoration:none;color:var(--primary);}
</style>
</head>
<body>

<div class="app">

<header>
<h1>Live Connect 🚀</h1>
<button onclick="toggleDark()"><i class="fa-solid fa-moon"></i></button>
</header>

<!-- LOGIN PAGE -->
<div id="login" class="page active">
  <div class="login-card">
    <h2>Welcome to Live Connect!</h2>
    <input type="text" id="usernameInput" placeholder="Enter your username"/>
    <button onclick="login()">Continue</button>
  </div>
</div>

<!-- HOME PAGE -->
<div id="home" class="page">
  <div class="hero">
    <h2 id="welcomeText">Real-Time Live Chat</h2>
    <p>Fast • Secure • Mobile Friendly</p>
  </div>
</div>

<!-- CHAT PAGE -->
<div id="chat" class="page">
  <div class="chat-box" id="chatBox"></div>
  <div class="input-row">
    <input id="msgInput" placeholder="Type message…"/>
    <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
  </div>
</div>

<!-- USERS PAGE -->
<div id="users" class="page">
  <h3>Online Users</h3>
  <div id="onlineUsers"></div>
</div>

<!-- ABOUT PAGE -->
<div id="about" class="page">
  <h3>About Live Connect 🚀</h3>
  <p>Live Connect is a modern, secure, and mobile-friendly real-time chat platform. Chat, create groups, like and delete messages, and see who's online instantly!</p>
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

let currentUser = null;
const chatBox = document.getElementById("chatBox");
const onlineUsersDiv = document.getElementById("onlineUsers");
const welcomeText = document.getElementById("welcomeText");

// Page navigation
function openPage(id, btn){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
}

function toggleDark(){document.body.classList.toggle('dark');}

// LOGIN FUNCTION
window.login = ()=>{
  const uname = document.getElementById('usernameInput').value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser = uname;
  set(ref(db,"users/"+uname), {name:uname, online:true});
  document.getElementById('login').classList.remove('active');
  document.getElementById('home').classList.add('active');
  welcomeText.textContent = "Welcome, "+uname+"!";
};

// REAL-TIME ONLINE USERS
onValue(ref(db,"users"), snap=>{
  onlineUsersDiv.innerHTML="";
  snap.forEach(u=>{
    if(u.val().online){
      const div = document.createElement("div");
      div.className="user";
      div.innerHTML=`<div class="dot"></div> ${u.val().name}`;
      onlineUsersDiv.appendChild(div);
    }
  });
});

// REAL-TIME CHAT
onValue(ref(db,"messages"), snap=>{
  chatBox.innerHTML="";
  snap.forEach(m=>{
    const div = document.createElement("div");
    div.className="msg";
    div.innerHTML=`<b>${m.val().from}:</b> ${m.val().text}`;
    if(m.val().from === currentUser){
      const btn = document.createElement("button");
      btn.textContent="🗑️";
      btn.onclick=()=>remove(ref(db,"messages/"+m.key));
      div.appendChild(btn);
    }
    chatBox.appendChild(div);
    chatBox.scrollTop=chatBox.scrollHeight;
  });
});

window.sendMsg = ()=>{
  const msgInput = document.getElementById('msgInput');
  if(!msgInput.value) return;
  push(ref(db,"messages"), {from:currentUser,text:msgInput.value});
  msgInput.value="";
};
</script>

</body>
</html>
