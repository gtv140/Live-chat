<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0a0a0a;
  --card:#111;
  --text:#e5e5e5;
  --muted:#aaa;
  --primary:#6b5bff;
  --accent:#ff5c8d;
  --success:#22c55e;
}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);transition:.3s;}
body.light{--bg:#f1f1f1;--card:#fff;--text:#111;--muted:#555;}
.app{max-width:480px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 3px 15px rgba(0,0,0,.7);}
header h1{margin:0;font-size:22px;color:var(--primary);text-shadow:0 0 12px var(--accent);}
header button{background:none;border:none;font-size:20px;color:var(--text);cursor:pointer;transition:.3s;}
header button:hover{color:var(--accent);}
.page{display:none;padding:16px;flex:1;transition:0.3s;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,#ff5c8d,#6b5bff);color:#fff;padding:28px;border-radius:20px;text-align:center;text-shadow:0 0 12px #fff;box-shadow:0 0 15px #6b5bff;transition:.3s;}
.hero h2{margin:0 0 10px;font-size:22px;}
.hero p{margin:0;font-size:14px;opacity:.9;}
.dashboard-stat{background:var(--card);padding:16px;margin-bottom:12px;border-radius:16px;box-shadow:0 0 12px var(--primary);display:flex;justify-content:space-between;align-items:center;transition:.3s;}
.dashboard-stat:hover{box-shadow:0 0 15px var(--accent);}
.dashboard-stat h4{margin:0;color:var(--accent);}
.dashboard-stat span{font-weight:bold;}
.chat-box{background:var(--card);border-radius:18px;padding:14px;height:45vh;overflow-y:auto;margin-bottom:12px;box-shadow:0 0 12px var(--primary);}
.msg{background:#222;color:#fff;padding:10px 14px;border-radius:16px;margin-bottom:10px;font-size:14px;position:relative;box-shadow:0 0 6px var(--primary);transition:.3s;}
.msg:hover{box-shadow:0 0 12px var(--accent);}
.msg .actions{position:absolute;top:6px;right:8px;display:flex;gap:6px;font-size:13px;cursor:pointer;}
.input-row{display:flex;gap:10px;}
.input-row input{flex:1;padding:14px;border-radius:14px;border:1px solid #444;background:#111;color:#fff;outline:none;}
.input-row button{padding:14px 18px;border:none;border-radius:14px;background:linear-gradient(90deg,#ff5c8d,#6b5bff);color:#fff;cursor:pointer;box-shadow:0 0 10px #ff5c8d;}
.user,.group{display:flex;align-items:center;gap:10px;margin-bottom:12px;cursor:pointer;padding:12px;background:var(--card);border-radius:14px;box-shadow:0 0 8px var(--primary);transition:.3s;}
.dot{width:12px;height:12px;background:var(--success);border-radius:50%;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:12px 0;box-shadow:0 -3px 12px rgba(0,0,0,.7);}
nav button{background:none;border:none;font-size:22px;color:var(--muted);cursor:pointer;}
nav button.active{color:var(--primary);}
.contact-card{display:flex;align-items:center;gap:14px;background:var(--card);padding:14px 18px;border-radius:14px;margin-bottom:14px;box-shadow:0 0 12px var(--accent);}
</style>
</head>
<body>
<div class="app">
<header>
<h1 id="appTitle">Live Connect</h1>
<div style="display:flex;gap:10px;">
  <button onclick="toggleTheme()"><i class="fa-solid fa-moon"></i></button>
  <button onclick="logout()"><i class="fa-solid fa-right-from-bracket"></i></button>
</div>
</header>

<div id="loginPage" class="page active">
<div class="login-card" style="background:var(--card);padding:20px;border-radius:18px;box-shadow:0 0 15px var(--primary);">
<h3>Enter Username</h3>
<input type="text" id="usernameInput" placeholder="Username" style="width:100%;padding:14px;margin-bottom:12px;border-radius:14px;border:1px solid #444;background:#111;color:#fff;">
<button onclick="login()" style="width:100%;padding:14px;border-radius:14px;background:linear-gradient(90deg,#ff5c8d,#6b5bff);color:#fff;border:none;cursor:pointer;">Continue</button>
</div>
</div>

<div id="home" class="page">
<div class="hero"><h2>Welcome <span id="dashUser"></span> 🚀</h2><p>Real-Time Chat & Active Connections</p></div>
<div class="dashboard-stat"><h4>Total Users</h4><span id="onlineCount">0</span></div>
<div class="dashboard-stat"><h4>Total Groups</h4><span id="groupCount">0</span></div>
</div>

<div id="chat" class="page">
<h4 id="chattingWith" style="margin:0 0 10px; color:var(--accent);">Select a chat</h4>
<div class="chat-box" id="chatBox"></div>
<div class="input-row">
<input id="msgInput" placeholder="Type message…"/>
<button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
</div>
</div>

<div id="users" class="page">
<h3>Online Users</h3><div id="userList"></div>
<h3 style="margin-top:16px;">Groups</h3><div id="groupList"></div>
<div class="input-row" style="margin-top:6px;">
<input id="groupInput" placeholder="Create group"/>
<button onclick="createGroup()">➕</button>
</div>
</div>

<div id="about" class="page">
<h3>About Live Connect 🚀</h3>
<p>Developed by <b>WebHub Technologies</b>. Real-time private & group chats with professional dashboard.</p>
</div>

<div id="contact" class="page">
<h3>Contact Support</h3>
<div class="contact-card"><i class="fa-solid fa-envelope"></i><div><h4>Email</h4><p>webhub262@gmail.com</p></div></div>
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
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("chat_user") || null;
let curChat = "";
let isGroup = false;

if(currentUser) { 
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("home").classList.add("active");
    startApp(); 
}

window.login = () => {
  const uname = document.getElementById("usernameInput").value.trim();
  if(!uname) return;
  currentUser = uname;
  localStorage.setItem("chat_user", uname);
  startApp();
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

function startApp() {
  document.getElementById("dashUser").textContent = currentUser;
  const userRef = ref(db, "users/"+currentUser);
  set(userRef, {name: currentUser, online: true});
  onDisconnect(userRef).update({online: false});
}

window.logout = () => {
  if(currentUser) set(ref(db, "users/"+currentUser), {online: false});
  localStorage.removeItem("chat_user");
  location.reload();
};

window.openPage = (id, btn) => {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
};

window.toggleTheme = () => document.body.classList.toggle('light');

onValue(ref(db, "users"), snap => {
  let online = 0;
  document.getElementById("userList").innerHTML = "";
  snap.forEach(u => {
    if(u.val().online) {
      online++;
      if(u.key !== currentUser) {
        const d = document.createElement("div");
        d.className = "user";
        d.innerHTML = `<div class="dot"></div>${u.val().name}`;
        d.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("chattingWith").innerText="Chatting with: "+u.key; loadChat(); openPage('chat', document.querySelector('nav button:nth-child(2)')); };
        document.getElementById("userList").appendChild(d);
      }
    }
  });
  document.getElementById("onlineCount").textContent = online;
});

onValue(ref(db, "groups"), snap => {
  document.getElementById("groupList").innerHTML = "";
  document.getElementById("groupCount").textContent = snap.size || 0;
  snap.forEach(g => {
    const d = document.createElement("div");
    d.className = "group";
    d.textContent = g.key;
    d.onclick = () => { curChat=g.key; isGroup=true; document.getElementById("chattingWith").innerText="Group: "+g.key; loadChat(); openPage('chat', document.querySelector('nav button:nth-child(2)')); };
    document.getElementById("groupList").appendChild(d);
  });
});

window.createGroup = () => {
  const g = document.getElementById("groupInput").value.trim();
  if(g) { set(ref(db, "groups/"+g), {createdBy: currentUser}); document.getElementById("groupInput").value=''; }
};

function loadChat() {
  const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
  onValue(ref(db, path), snap => {
    const chatBox = document.getElementById("chatBox");
    chatBox.innerHTML = "";
    snap.forEach(m => {
      const div = document.createElement("div");
      div.className = "msg";
      div.innerHTML = `<b>${m.val().from}:</b> ${m.val().text} <div class="actions"><span onclick="deleteMsg('${path}','${m.key}')">🗑️</span></div>`;
      chatBox.appendChild(div);
    });
    chatBox.scrollTop = chatBox.scrollHeight;
  });
}

window.sendMsg = () => {
  const input = document.getElementById('msgInput');
  if(!input.value || !curChat) return;
  const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
  push(ref(db, path), {from: currentUser, text: input.value});
  input.value = '';
};

window.deleteMsg = (path, key) => { remove(ref(db, path+"/"+key)); };
</script>
</body>
</html>
