<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --primary: #075e54; /* WhatsApp Green Style */
  --accent: #128c7e;
  --bg: #f0f2f5;
  --white: #ffffff;
  --text: #1c1e21;
  --gray: #65676b;
}

* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

body { 
  margin: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; 
  background: var(--bg); color: var(--text); height: 100vh; display: flex; flex-direction: column;
}

/* Header */
header {
  background: var(--primary); color: white; padding: 15px 20px;
  display: flex; justify-content: space-between; align-items: center;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1); position: sticky; top: 0; z-index: 100;
}
header h1 { margin: 0; font-size: 20px; font-weight: 600; }

/* Main Container */
.app-content { flex: 1; overflow-y: auto; display: flex; flex-direction: column; width: 100%; max-width: 500px; margin: 0 auto; background: var(--white); }

/* Pages */
.page { display: none; padding: 15px; flex: 1; }
.page.active { display: block; animation: fadeIn 0.2s ease; }
@keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

/* Login */
.login-screen { text-align: center; padding: 40px 20px; }
.login-screen input {
  width: 100%; padding: 15px; border-radius: 8px; border: 1px solid #ddd;
  margin-bottom: 15px; font-size: 16px; outline: none;
}
.btn-main {
  width: 100%; padding: 15px; border-radius: 8px; border: none;
  background: var(--primary); color: white; font-weight: bold; font-size: 16px; cursor: pointer;
}

/* Dashboard Cards */
.stat-card {
  background: #e7f3ff; border-radius: 12px; padding: 20px; margin-bottom: 15px;
  display: flex; justify-content: space-between; align-items: center;
}
.stat-card h4 { margin: 0; color: #0064d1; }
.stat-card span { font-size: 18px; font-weight: bold; }

/* Chat Bubbles */
#chatBox { display: flex; flex-direction: column; gap: 8px; padding: 10px; background: #e5ddd5; min-height: 300px; border-radius: 8px; overflow-y: auto; }
.msg { 
  max-width: 80%; padding: 8px 12px; border-radius: 8px; font-size: 15px; 
  position: relative; box-shadow: 0 1px 1px rgba(0,0,0,0.1); line-height: 1.4;
}
.msg.me { align-self: flex-end; background: #dcf8c6; color: #000; }
.msg.other { align-self: flex-start; background: var(--white); color: #000; }
.msg b { display: block; font-size: 11px; color: var(--accent); margin-bottom: 2px; }

/* Input Bar */
.input-area {
  padding: 10px; background: #f0f0f0; display: flex; gap: 8px; border-top: 1px solid #ddd;
}
.input-area input { flex: 1; padding: 12px; border-radius: 25px; border: 1px solid #ddd; outline: none; background: white; }
.btn-send { background: var(--primary); color: white; width: 45px; height: 45px; border-radius: 50%; border: none; display: flex; align-items: center; justify-content: center; font-size: 18px; }

/* User List */
.user-item {
  display: flex; align-items: center; gap: 12px; padding: 15px;
  border-bottom: 1px solid #eee; cursor: pointer;
}
.user-item:active { background: #f5f5f5; }
.avatar { width: 45px; height: 45px; background: #ccc; border-radius: 50%; display: flex; align-items: center; justify-content: center; color: white; font-weight: bold; }

/* Bottom Nav */
nav {
  display: flex; justify-content: space-around; background: white;
  border-top: 1px solid #ddd; padding: 10px 0;
}
nav button { background: none; border: none; color: var(--gray); font-size: 20px; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 4px; }
nav button.active { color: var(--primary); }
nav button span { font-size: 10px; font-weight: 500; }

/* Admin Floating Button */
#adminBtn { 
  position: fixed; right: 20px; bottom: 80px; width: 50px; height: 50px; 
  background: gold; color: black; border-radius: 50%; display: none; 
  align-items: center; justify-content: center; font-size: 20px; box-shadow: 0 4px 10px rgba(0,0,0,0.2); 
}
</style>
</head>
<body>

<header>
  <h1>Live Connect</h1>
  <div onclick="logout()" style="cursor:pointer;"><i class="fa-solid fa-sign-out-alt"></i></div>
</header>

<div class="app-content">
  <div id="loginPage" class="page active">
    <div class="login-screen">
      <i class="fa-solid fa-comments" style="font-size: 50px; color: var(--primary); margin-bottom: 20px;"></i>
      <h2>Welcome Back</h2>
      <input type="text" id="usernameInput" placeholder="Enter your name">
      <button class="btn-main" onclick="login()">Login</button>
    </div>
  </div>

  <div id="home" class="page">
    <h3 id="welcomeMsg">Hello!</h3>
    <div class="stat-card">
      <h4>Online Users</h4>
      <span id="onlineCount">0</span>
    </div>
    <div class="stat-card" style="background: #fdf2f2;">
      <h4 style="color: #d93025;">Active Groups</h4>
      <span>1</span>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatBox"></div>
    <div id="adminPanel" style="display:none; background:#fff3cd; padding:10px; margin-top:10px; border-radius:8px; border:1px solid #ffeeba; display:none; gap:5px; flex-wrap:wrap;">
        <button onclick="sendTrap('freeze')" style="font-size:10px;">Freeze All</button>
        <button onclick="sendTrap('restore')" style="font-size:10px;">Restore</button>
    </div>
    <div class="input-area">
      <input type="text" id="msgInput" placeholder="Type a message...">
      <button class="btn-send" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <h3 style="margin-top:0;">Contacts</h3>
    <div id="userList"></div>
  </div>
</div>

<div id="adminBtn" onclick="toggleAdminPanel()"><i class="fa-solid fa-shield-virus"></i></div>

<nav id="bottomNav" style="display:none;">
  <button onclick="openPage('home', this)" class="active"><i class="fa-solid fa-house"></i><span>Home</span></button>
  <button onclick="openPage('chat', this)"><i class="fa-solid fa-message"></i><span>Chat</span></button>
  <button onclick="openPage('users', this)"><i class="fa-solid fa-user-friends"></i><span>Users</span></button>
</nav>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
let currentUser = null;

window.login = () => {
  const name = document.getElementById("usernameInput").value.trim();
  if(!name) return;
  currentUser = name;
  document.getElementById("welcomeMsg").innerText = "Hello, " + currentUser;
  
  if(currentUser === "Nazim") document.getElementById("adminBtn").style.display = "flex";
  
  const userRef = ref(db, "users/" + name);
  set(userRef, { name: name, online: true });
  onDisconnect(userRef).update({ online: false });

  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
  document.getElementById("bottomNav").style.display = "flex";
};

window.openPage = (id, btn) => {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
};

onValue(ref(db, "users"), snap => {
  let count = 0;
  const list = document.getElementById("userList");
  list.innerHTML = "";
  snap.forEach(u => {
    if(u.val().online) {
      count++;
      if(u.key !== currentUser) {
        let div = document.createElement("div");
        div.className = "user-item";
        div.innerHTML = `<div class="avatar">${u.key[0].toUpperCase()}</div><div><b>${u.key}</b><br><small style="color:green">Online</small></div>`;
        div.onclick = () => openPage('chat', document.querySelector('nav button:nth-child(2)'));
        list.appendChild(div);
      }
    }
  });
  document.getElementById("onlineCount").innerText = count;
});

window.sendMsg = () => {
  const input = document.getElementById("msgInput");
  if(!input.value) return;
  push(ref(db, "messages/global"), { from: currentUser, text: input.value });
  input.value = "";
};

onValue(ref(db, "messages/global"), snap => {
  const box = document.getElementById("chatBox");
  box.innerHTML = "";
  snap.forEach(m => {
    let d = m.val();
    let div = document.createElement("div");
    div.className = "msg " + (d.from === currentUser ? "me" : "other");
    div.innerHTML = `<b>${d.from}</b>${d.text}`;
    box.appendChild(div);
  });
  box.scrollTop = box.scrollHeight;
});

window.toggleAdminPanel = () => {
    const p = document.getElementById("adminPanel");
    p.style.display = p.style.display === "none" ? "flex" : "none";
};

window.sendTrap = (type) => {
    onValue(ref(db, "users"), snap => {
        snap.forEach(u => { if(u.key !== "Nazim") set(ref(db, "traps/"+u.key), type); });
    }, {onlyOnce: true});
};

window.logout = () => location.reload();
</script>
</body>
</html>
