<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Live Connect 🚀</title>
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
.hero{background:linear-gradient(135deg,var(--primary),#60a5fa);color:#fff;padding:24px;border-radius:18px;}
.hero h2{margin:0 0 8px;}
.hero p{margin:0;opacity:.9;}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:50vh;overflow-y:auto;margin-bottom:10px;}
.msg{background:#e5e7eb;color:#000;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;}
body.dark .msg{background:#1f2937;color:#e5e7eb;}
.input-row{display:flex;gap:8px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #ccc;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:var(--primary);color:#fff;}
.user{display:flex;align-items:center;gap:8px;margin-bottom:10px;}
.dot{width:10px;height:10px;background:#22c55e;border-radius:50%;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:0 -2px 10px rgba(0,0,0,.06);}
nav button{background:none;border:none;font-size:20px;color:var(--muted);}
nav button.active{color:var(--primary);}
.actions span{cursor:pointer;margin-left:6px;}
.comments{margin-left:16px;font-size:13px;color:var(--muted);}
.comment-input{display:flex;margin-top:4px;gap:4px;}
.comment-input input{flex:1;padding:6px;border-radius:6px;border:1px solid #ccc;font-size:12px;}
.comment-input button{padding:6px 8px;border:none;border-radius:6px;background:var(--primary);color:#fff;font-size:12px;}
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
  <h3>Enter Username</h3>
  <input type="text" id="usernameInput" placeholder="Username">
  <button onclick="login()">Continue</button>
</div>

<!-- HOME -->
<div id="home" class="page">
  <div class="hero">
    <h2>Real-Time Live Chat</h2>
    <p>Fast • Secure • Mobile Friendly</p>
  </div>
  <p style="margin-top:16px;color:var(--muted)">
    Connect instantly, see who’s online, and chat smoothly on mobile.
  </p>
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

<!-- ABOUT -->
<div id="about" class="page">
  <h3>About</h3>
  <p>
    Live Connect is a modern real-time chat platform designed specially for mobile users. You can chat in groups, see who is online, comment, like, and manage your profile easily.
  </p>
</div>

<!-- CONTACT -->
<div id="contact" class="page">
  <h3>Contact & Support</h3>
  <p><i class="fa-solid fa-envelope"></i> webhub262@gmail.com</p>
  <p>
    <a href="https://www.facebook.com/profile.php?id=100084218946114">Facebook</a> |
    <a href="https://www.instagram.com/mr_nazim073">Instagram</a> |
    <a href="https://youtube.com/@crazykhantv">YouTube</a>
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
import { getDatabase, ref, set, push, onValue, remove, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let currentUser=null, curChat="", isGroup=false;

const currentUsersDiv=document.getElementById("currentUsers");
const chatBox=document.getElementById("chatBox");

function login(){
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username"); return;}
  currentUser=uname;
  set(ref(db,"users/"+uname),{name:uname,online:true});
  document.getElementById("login").classList.remove("active");
  document.getElementById("home").classList.add("active");
  listenUsers();
  listenChats();
}

function openPage(id,btn){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
}

function toggleDark(){document.body.classList.toggle('dark');}

// Online Users
function listenUsers(){
  onValue(ref(db,"users"), snap=>{
    currentUsersDiv.innerHTML="";
    snap.forEach(u=>{
      if(u.val().online && u.key!==currentUser){
        const div=document.createElement("div");
        div.className="user";
        div.innerHTML=`<div class="dot"></div>${u.val().name}`;
        div.onclick=()=>{curChat=u.key; isGroup=false; openPage('chat',document.querySelector('nav button:nth-child(2)'))};
        currentUsersDiv.appendChild(div);
      }
    });
  });
}

// Send Message
function sendMsg(){
  if(!curChat) return alert("Select a user/group first");
  const input=document.getElementById('msgInput');
  if(!input.value) return;
  const path = isGroup ? "groupChats/"+curChat : "chats/"+[currentUser,curChat].sort().join("_");
  push(ref(db,path),{from:currentUser,text:input.value,likes:0,comments:{}});
  input.value='';
}

// Listen Chats
function listenChats(){
  const chatsRef=ref(db);
  onValue(chatsRef, snap=>{
    chatBox.innerHTML="";
    if(!curChat) return;
    const path = isGroup ? "groupChats/"+curChat : "chats/"+[currentUser,curChat].sort().join("_");
    onValue(ref(db,path), chatSnap=>{
      chatBox.innerHTML="";
      chatSnap.forEach(m=>{
        const div=document.createElement("div");
        div.className="msg";
        div.innerHTML=`<b>${m.val().from}:</b> ${m.val().text} <span class="actions"><span onclick="likeMsg('${path}','${m.key}')">👍</span><span onclick="deleteMsg('${path}','${m.key}')">🗑️</span></span>`;
        chatBox.appendChild(div);
        chatBox.scrollTop=chatBox.scrollHeight;
      });
    });
  });
}

// Like Message
window.likeMsg=(path,key)=>{
  const msgRef=ref(db,path+"/"+key+"/likes");
  onValue(msgRef,snap=>{
    update(ref(db,path+"/"+key),{likes:(snap.val()||0)+1});
  },{onlyOnce:true});
}

// Delete Message
window.deleteMsg=(path,key)=>{
  remove(ref(db,path+"/"+key));
}
</script>
</body>
</html>
