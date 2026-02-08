<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Chat</title>

<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">

<style>
:root{
  --bg:#f0f2f5;
  --card:#ffffff;
  --primary:#25D366;
  --text:#111;
  --muted:#667781;
}
body.dark{
  --bg:#0b141a;
  --card:#111b21;
  --primary:#25D366;
  --text:#e9edef;
  --muted:#8696a0;
}
*{box-sizing:border-box;margin:0;padding:0;font-family:system-ui}
body{background:var(--bg);color:var(--text);height:100vh}

.app{display:flex;height:100vh}

/* ===== SIDEBAR ===== */
.sidebar{
  width:320px;
  background:var(--card);
  border-right:1px solid #ddd;
  display:flex;
  flex-direction:column;
}
.sidebar header{
  padding:12px;
  display:flex;
  justify-content:space-between;
  align-items:center;
}
.user-list{flex:1;overflow:auto}
.user{
  padding:12px;
  display:flex;
  gap:10px;
  cursor:pointer;
}
.user:hover{background:#00000010}
.avatar{
  width:40px;height:40px;border-radius:50%;
  background:var(--primary);
  color:white;
  display:flex;align-items:center;justify-content:center;
  font-weight:bold;
}
.user-info{flex:1}
.user-info small{color:var(--muted)}
.badge{
  background:red;color:white;
  font-size:11px;padding:2px 6px;border-radius:10px;
}

/* ===== CHAT ===== */
.chat{flex:1;display:flex;flex-direction:column}
.chat-header{
  padding:12px;
  background:var(--card);
  display:flex;
  justify-content:space-between;
  align-items:center;
}
.messages{
  flex:1;
  padding:15px;
  overflow:auto;
  background:var(--bg);
}
.msg{
  max-width:70%;
  margin-bottom:8px;
  padding:8px 10px;
  border-radius:10px;
  font-size:14px;
}
.me{background:#dcf8c6;margin-left:auto}
.other{background:var(--card)}
.msg small{font-size:11px;color:var(--muted)}
.typing{font-size:12px;color:var(--muted);margin:5px}

/* ===== INPUT ===== */
.input{
  display:flex;
  padding:10px;
  background:var(--card);
}
.input input{
  flex:1;
  padding:10px;
  border-radius:20px;
  border:1px solid #ccc;
}
.input button{
  margin-left:8px;
  background:var(--primary);
  border:none;
  color:white;
  padding:10px 16px;
  border-radius:50%;
}

/* ===== MOBILE ===== */
@media(max-width:768px){
  .sidebar{width:100px}
  .user-info, .badge{display:none}
}

/* ===== LOGIN ===== */
.login{
  position:fixed;
  inset:0;
  background:var(--bg);
  display:flex;
  align-items:center;
  justify-content:center;
}
.login-box{
  background:var(--card);
  padding:30px;
  border-radius:10px;
  width:300px;
}
.login-box input{
  width:100%;
  padding:10px;
  margin-top:10px;
}
</style>
</head>

<body>
<div class="login" id="login">
  <div class="login-box">
    <h3>Enter Username</h3>
    <input id="username" placeholder="Your name">
    <button onclick="login()">Start</button>
  </div>
</div>

<div class="app" id="app" style="display:none">
  <div class="sidebar">
    <header>
      <b>Chats</b>
      <i class="fa fa-moon" onclick="toggleTheme()"></i>
    </header>
    <div class="user-list" id="users"></div>
  </div>

  <div class="chat">
    <div class="chat-header">
      <div id="chatUser">Select user</div>
      <small id="status"></small>
    </div>
    <div class="messages" id="messages"></div>
    <div class="typing" id="typing"></div>
    <div class="input">
      <input id="msg" placeholder="Message">
      <button onclick="sendMsg()"><i class="fa fa-paper-plane"></i></button>
    </div>
  </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, push, onValue, set, onDisconnect } 
from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let me="", current="";

window.login=()=>{
  me=username.value.trim();
  if(!me) return;
  login.style.display="none";
  appDiv.style.display="flex";
  set(ref(db,"users/"+me),{online:true});
  onDisconnect(ref(db,"users/"+me)).set({online:false});
};

const appDiv=document.getElementById("app");

onValue(ref(db,"users"),snap=>{
  users.innerHTML="";
  snap.forEach(u=>{
    if(u.key!==me){
      users.innerHTML+=`
      <div class="user" onclick="openChat('${u.key}')">
        <div class="avatar">${u.key[0]}</div>
        <div class="user-info">
          <b>${u.key}</b><br>
          <small>${u.val().online?"online":"offline"}</small>
        </div>
      </div>`;
    }
  });
});

window.openChat=(u)=>{
  current=u;
  chatUser.innerText=u;
  onValue(ref(db,"chats/"+me+"_"+u),snap=>{
    messages.innerHTML="";
    snap.forEach(m=>{
      let d=m.val();
      messages.innerHTML+=`
      <div class="msg ${d.from===me?"me":"other"}">
        ${d.text}<br><small>${d.time}</small>
      </div>`;
    });
    messages.scrollTop=messages.scrollHeight;
  });
};

window.sendMsg=()=>{
  if(!msg.value||!current) return;
  const data={
    from:me,
    text:msg.value,
    time:new Date().toLocaleTimeString()
  };
  push(ref(db,"chats/"+me+"_"+current),data);
  push(ref(db,"chats/"+current+"_"+me),data);
  msg.value="";
};

window.toggleTheme=()=>{
  document.body.classList.toggle("dark");
};
</script>
</body>
</html>
