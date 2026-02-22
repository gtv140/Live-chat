<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0a0a0a; --card:#111; --text:#e5e5e5; --muted:#aaa;
  --primary:#6b5bff; --accent:#ff5c8d; --success:#22c55e; --glass: rgba(255,255,255,0.05);
}
body{margin:0; font-family:system-ui,-apple-system,sans-serif; background:var(--bg); color:var(--text); overflow:hidden;}
body.light{--bg:#f1f1f1; --card:#fff; --text:#111; --muted:#555;}

.app{max-width:480px; margin:auto; height:100vh; display:flex; flex-direction:column; position:relative;}

header{padding:14px 16px; display:flex; justify-content:space-between; align-items:center; background:var(--card); box-shadow:0 3px 15px rgba(0,0,0,.7); z-index:10;}
header h1{margin:0; font-size:20px; color:var(--primary); text-shadow:0 0 10px var(--accent);}
header div{display:flex; gap:15px;}

.content{flex:1; overflow-y:auto; padding:16px; padding-bottom:160px; scroll-behavior: smooth;}
.page{display:none; animation: fadeIn 0.3s ease;}
.page.active{display:block;}
@keyframes fadeIn { from{opacity:0; transform:translateY(10px);} to{opacity:1; transform:translateY(0);} }

.hero{background:linear-gradient(135deg,#ff5c8d,#6b5bff); color:#fff; padding:25px; border-radius:20px; text-align:center; box-shadow:0 10px 20px rgba(107,91,255,0.3); margin-bottom:20px;}
.dashboard-stat{background:var(--card); padding:16px; margin-bottom:12px; border-radius:16px; border:1px solid var(--glass); display:flex; justify-content:space-between; align-items:center;}

/* Chat Elements */
.chat-box{display:flex; flex-direction:column; gap:12px;}
.msg{background:var(--card); padding:12px; border-radius:16px; border:1px solid var(--glass); position:relative; max-width:90%;}
.msg.me{align-self: flex-end; border-color: var(--primary); background: rgba(107,91,255,0.1);}
.msg b{color:var(--primary); font-size:12px; display:block; margin-bottom:4px;}
.msg .actions{position:absolute; top:8px; right:10px; cursor:pointer; color:var(--accent); font-size:12px;}

.comments{margin-top:8px; padding-left:10px; border-left:2px solid var(--primary); font-size:12px; opacity:0.8;}
.comment-input{margin-top:8px; display:flex; gap:5px;}
.comment-input input{flex:1; background:#000; border:1px solid var(--glass); color:#fff; padding:5px 10px; border-radius:10px; font-size:11px; outline:none;}

/* Fixed Bottom Area */
.input-fixed{position:absolute; bottom:80px; left:0; right:0; padding:12px; background:var(--card); display:flex; gap:10px; border-top:1px solid var(--glass); z-index:5;}
.input-fixed input{flex:1; padding:12px 18px; border-radius:25px; border:1px solid var(--glass); background:#000; color:#fff; outline:none;}
.btn-send{width:45px; height:45px; border-radius:50%; border:none; background:linear-gradient(90deg,#ff5c8d,#6b5bff); color:#fff; cursor:pointer;}

nav{position:absolute; bottom:0; left:0; right:0; height:80px; background:var(--card); display:flex; justify-content:space-around; align-items:center; border-top:1px solid var(--glass); z-index:10;}
nav button{background:none; border:none; font-size:22px; color:var(--muted); cursor:pointer; display:flex; flex-direction:column; align-items:center; gap:4px;}
nav button.active{color:var(--primary);}
nav button span{font-size:10px; font-weight:bold;}

.user-row, .group-row{display:flex; align-items:center; gap:12px; padding:14px; background:var(--card); border-radius:15px; margin-bottom:10px; border:1px solid var(--glass); cursor:pointer;}
.dot{width:10px; height:10px; background:var(--success); border-radius:50%; box-shadow:0 0 8px var(--success);}

.contact-card{display:flex; align-items:center; gap:15px; background:var(--card); padding:15px; border-radius:15px; margin-bottom:12px; border:1px solid var(--glass);}
.contact-card a{color:var(--primary); text-decoration:none; font-weight:bold;}
</style>
</head>
<body>

<div class="app">
  <header>
    <h1>Live Connect</h1>
    <div>
      <i class="fa-solid fa-moon" onclick="toggleTheme()" style="cursor:pointer"></i>
      <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--accent); cursor:pointer"></i>
    </div>
  </header>

  <div class="content" id="scrollArea">
    <div id="loginPage" class="page active">
      <div style="text-align:center; padding-top:50px;">
        <i class="fa-solid fa-circle-user" style="font-size:60px; color:var(--primary); margin-bottom:20px;"></i>
        <h3>Identify Yourself</h3>
        <input type="text" id="usernameInput" placeholder="Enter Username" style="width:80%; padding:15px; border-radius:15px; background:var(--card); color:white; border:1px solid var(--glass); margin-bottom:15px;">
        <button onclick="login()" style="width:80%; padding:15px; border-radius:15px; background:var(--primary); color:white; border:none; font-weight:bold; cursor:pointer;">Initialize Connect</button>
      </div>
    </div>

    <div id="home" class="page">
      <div class="hero">
        <h2>Welcome, <span id="dashUser">Explorer</span> 🚀</h2>
        <p>Your portal to real-time connections</p>
      </div>
      <div class="dashboard-stat"><h4>Network Users</h4><span id="onlineCount">0</span></div>
      <div class="dashboard-stat"><h4>Active Groups</h4><span id="groupCount">0</span></div>
    </div>

    <div id="chat" class="page">
      <h4 id="chatTitle" style="text-align:center; color:var(--primary); margin:0 0 15px;">Chat Room</h4>
      <div class="chat-box" id="chatBox"></div>
    </div>

    <div id="users" class="page">
      <h3>Online Now</h3><div id="userList"></div>
      <h3 style="margin-top:20px;">Groups</h3><div id="groupList"></div>
      <div class="comment-input" style="padding:10px 0;">
        <input id="groupInput" placeholder="New Group Name..."/>
        <button onclick="createGroup()" style="background:var(--primary); color:white; border:none; padding:10px 15px; border-radius:10px;">➕</button>
      </div>
    </div>

    <div id="contact" class="page">
      <h3>Contact Support</h3>
      <div class="contact-card"><i class="fa-solid fa-envelope"></i><div><p>Email</p><a href="mailto:webhub262@gmail.com">webhub262@gmail.com</a></div></div>
      <div class="contact-card"><i class="fab fa-facebook"></i><div><p>Facebook</p><a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank">View Profile</a></div></div>
      <div class="contact-card"><i class="fa-solid fa-phone"></i><div><p>Phone</p><a href="tel:+923001234567">+92 300 1234567</a></div></div>
    </div>
  </div>

  <div class="input-fixed" id="inputBar" style="display:none;">
    <input id="msgInput" placeholder="Type a message..."/>
    <button onclick="sendMsg()" class="btn-send"><i class="fa-solid fa-paper-plane"></i></button>
  </div>

  <nav id="navbar" style="display:none;">
    <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i><span>Home</span></button>
    <button onclick="openPage('chat',this)" id="cBtn"><i class="fa-solid fa-comments"></i><span>Chat</span></button>
    <button onclick="openPage('users',this)" id="uBtn"><i class="fa-solid fa-user-group"></i><span>Users</span></button>
    <button onclick="openPage('contact',this)"><i class="fa-solid fa-headset"></i><span>Help</span></button>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("chat_user");
let curChat = "";
let isGroup = false;

if(currentUser) autoLogin();

window.login = () => {
  const uname = document.getElementById("usernameInput").value.trim();
  if(!uname) return;
  currentUser = uname;
  localStorage.setItem("chat_user", uname);
  autoLogin();
};

function autoLogin() {
  document.getElementById("dashUser").textContent = currentUser;
  const userRef = ref(db, "users/" + currentUser);
  set(userRef, { name: currentUser, online: true });
  onDisconnect(userRef).update({ online: false });
  
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
  document.getElementById("navbar").style.display = "flex";
  loadData();
}

window.logout = () => {
  if(currentUser) set(ref(db, "users/" + currentUser), { online: false });
  localStorage.removeItem("chat_user");
  location.reload();
};

window.openPage = (id, btn) => {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
  document.getElementById('inputBar').style.display = (id === 'chat') ? 'flex' : 'none';
};

window.toggleTheme = () => document.body.classList.toggle('light');

function loadData() {
  // Users Load
  onValue(ref(db, "users"), snap => {
    let online = 0;
    const userList = document.getElementById("userList");
    userList.innerHTML = "";
    snap.forEach(u => {
      if(u.val().online) {
        online++;
        if(u.key !== currentUser) {
          const d = document.createElement("div");
          d.className = "user-row";
          d.innerHTML = `<div class="dot"></div>${u.key}`;
          d.onclick = () => { curChat = u.key; isGroup = false; document.getElementById('chatTitle').innerText = "PRIVATE: "+u.key; openPage('chat', document.getElementById('cBtn')); loadChat(); };
          userList.appendChild(d);
        }
      }
    });
    document.getElementById("onlineCount").textContent = online;
  });

  // Groups Load
  onValue(ref(db, "groups"), snap => {
    const groupList = document.getElementById("groupList");
    groupList.innerHTML = "";
    document.getElementById("groupCount").textContent = snap.size || 0;
    snap.forEach(g => {
      const d = document.createElement("div");
      d.className = "group-row";
      d.innerHTML = `<i class="fa-solid fa-hashtag" style="color:var(--primary)"></i> ${g.key}`;
      d.onclick = () => { curChat = g.key; isGroup = true; document.getElementById('chatTitle').innerText = "GROUP: "+g.key; openPage('chat', document.getElementById('cBtn')); loadChat(); };
      groupList.appendChild(d);
    });
  });
}

window.createGroup = () => {
  const g = document.getElementById("groupInput").value.trim();
  if(g) set(ref(db, "groups/" + g), { createdBy: currentUser });
  document.getElementById("groupInput").value = '';
};

function loadChat() {
  const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
  onValue(ref(db, path), snap => {
    const chatBox = document.getElementById("chatBox");
    chatBox.innerHTML = "";
    snap.forEach(m => {
      const data = m.val();
      const div = document.createElement("div");
      div.className = "msg" + (data.from === currentUser ? " me" : "");
      let commentsHTML = `<div class="comments">` + (data.comments ? Object.values(data.comments).map(c => `<div><b>${c.from}:</b> ${c.text}</div>`).join('') : '') + `</div>`;
      div.innerHTML = `
        <b>${data.from}</b> ${data.text}
        <div class="actions" onclick="window.del('${path}','${m.key}')">🗑️</div>
        ${commentsHTML}
        <div class="comment-input"><input id="cInp-${m.key}" placeholder="Comment..."/><button onclick="window.addC('${path}','${m.key}')">💬</button></div>
      `;
      chatBox.appendChild(div);
    });
    document.getElementById('scrollArea').scrollTop = document.getElementById('scrollArea').scrollHeight;
  });
}

window.sendMsg = () => {
  const input = document.getElementById('msgInput');
  if(!input.value || !curChat) return;
  const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
  push(ref(db, path), { from: currentUser, text: input.value });
  input.value = '';
};

window.del = (path, key) => { if(confirm("Delete?")) remove(ref(db, path + "/" + key)); };
window.addC = (path, key) => {
  const inp = document.getElementById("cInp-"+key);
  if(inp.value) push(ref(db, path + "/" + key + "/comments"), { from: currentUser, text: inp.value });
  inp.value = '';
};
</script>
</body>
</html>
