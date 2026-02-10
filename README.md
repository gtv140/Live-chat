<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Live Connect 🚀</title>

<!-- Icons -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css"/>

<style>
:root{
  --bg:#f6f7fb;
  --card:#fff;
  --text:#111827;
  --muted:#6b7280;
  --primary:#2563eb;
}
body.dark{
  --bg:#0f172a;
  --card:#111827;
  --text:#e5e7eb;
  --muted:#9ca3af;
  --primary:#3b82f6;
}
*{box-sizing:border-box}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
.app{max-width:420px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 2px 10px rgba(0,0,0,.06);}
header h1{font-size:18px;margin:0;}
header button{background:none;border:none;font-size:18px;color:var(--text);}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,var(--primary),#60a5fa);color:#fff;padding:24px;border-radius:18px;text-align:center;margin-bottom:12px;}
.hero h2{margin:0 0 8px}
.hero p{margin:0;opacity:.9;}
.login-card{background:var(--card);padding:20px;border-radius:16px;box-shadow:0 2px 10px rgba(0,0,0,.1);text-align:center;margin-top:40px;}
.login-card input{width:80%;padding:12px;margin:8px 0;border-radius:12px;border:1px solid #ccc;font-size:16px;}
.login-card button{padding:12px 16px;border:none;border-radius:12px;background:var(--primary);color:#fff;font-size:16px;cursor:pointer;}
.login-card button:hover{opacity:.9;}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:40vh;overflow-y:auto;margin-bottom:10px;}
.msg{background:#e5e7eb;color:#000;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;}
body.dark .msg{background:#1f2937;color:#e5e7eb;}
.input-row{display:flex;gap:8px;margin-bottom:12px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #ccc;font-size:14px;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:var(--primary);color:#fff;font-size:16px;}
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
  <h1>Live Connect 🚀</h1>
  <button onclick="toggleDark()"><i class="fa-solid fa-moon"></i></button>
</header>

<!-- LOGIN -->
<div id="login" class="page active">
  <div class="login-card">
    <h2>Welcome to Live Connect</h2>
    <input type="text" id="usernameInput" placeholder="Enter username"/>
    <button onclick="login()">Enter Chat</button>
  </div>
</div>

<!-- HOME -->
<div id="home" class="page">
  <div class="hero">
    <h2>Real-Time Chat</h2>
    <p>Fast • Secure • Mobile Friendly</p>
  </div>
</div>

<!-- CHAT -->
<div id="chat" class="page">
  <div class="chat-box" id="chatBox"></div>
  <div class="input-row">
    <input id="msgInput" placeholder="Type message…"/>
    <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
  </div>
</div>

<!-- USERS -->
<div id="users" class="page">
  <h3>Online Users</h3>
  <div id="onlineUsers"></div>
</div>

<!-- ABOUT -->
<div id="about" class="page">
  <h3>About</h3>
  <p>
    Live Connect is a modern real-time chat platform designed for fast and secure communication. You can chat privately, join groups, and see who’s online instantly.
  </p>
</div>

<!-- CONTACT -->
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
import { getDatabase, ref, set, push, onValue } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

// Login
window.login = ()=>{
  const uname = document.getElementById('usernameInput').value.trim();
  if(!uname){alert("Enter username"); return;}
  currentUser = uname;
  set(ref(db,'users/'+uname),{name:uname,online:true});
  showPage('home');
  listenUsers();
  listenMessages();
};

// Dark Mode
window.toggleDark = ()=>{document.body.classList.toggle('dark');};

// Navigation
window.openPage = (id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

// Real-time chat
function sendMsg(){
  const input = document.getElementById('msgInput');
  if(!input.value || !currentUser) return;
  push(ref(db,'messages'),{from:currentUser,text:input.value});
  input.value='';
}

function listenMessages(){
  const chatBox = document.getElementById('chatBox');
  onValue(ref(db,'messages'), snap=>{
    chatBox.innerHTML='';
    snap.forEach(m=>{
      const div=document.createElement('div');
      div.className='msg';
      div.innerHTML=`<b>${m.val().from}:</b> ${m.val().text}`;
      chatBox.appendChild(div);
      chatBox.scrollTop=chatBox.scrollHeight;
    });
  });
}

// Online users
function listenUsers(){
  const onlineDiv = document.getElementById('onlineUsers');
  onValue(ref(db,'users'), snap=>{
    onlineDiv.innerHTML='';
    snap.forEach(u=>{
      if(u.val().online){
        const d=document.createElement('div');
        d.className='user';
        d.innerHTML=`<div class="dot"></div>${u.val().name}`;
        onlineDiv.appendChild(d);
      }
    });
  });
}
</script>

</body>
</html>
