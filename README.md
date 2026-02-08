<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Live Chat</title>

<!-- Firebase -->
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let username = localStorage.getItem("username");
let currentChat = "global";
let unread = 0;

/* ---------- LOGIN ---------- */
window.login = () => {
  const name = document.getElementById("username").value.trim();
  if(!name) return alert("Enter username");
  username = name;
  localStorage.setItem("username", name);

  set(ref(db, "users/"+name), {
    online: true,
    lastSeen: Date.now()
  });

  onDisconnect(ref(db,"users/"+name)).update({
    online:false,
    lastSeen:Date.now()
  });

  document.getElementById("login").style.display="none";
  document.getElementById("app").style.display="flex";
};

/* ---------- USERS ---------- */
onValue(ref(db,"users"), snap=>{
  const list = document.getElementById("users");
  list.innerHTML="";
  snap.forEach(u=>{
    const data=u.val();
    const div=document.createElement("div");
    div.className="user";
    div.innerHTML=`
      <span class="avatar">${u.key[0].toUpperCase()}</span>
      <div>
        <b>${u.key}</b>
        <small>${data.online?"Online":"Last seen"}</small>
      </div>`;
    list.appendChild(div);
  });
});

/* ---------- MESSAGES ---------- */
window.sendMessage=()=>{
  const text=document.getElementById("msg").value.trim();
  if(!text) return;
  push(ref(db,"chats/"+currentChat),{
    user:username,
    text,
    time:Date.now()
  });
  document.getElementById("msg").value="";
};

onValue(ref(db,"chats/"+currentChat),snap=>{
  const box=document.getElementById("messages");
  box.innerHTML="";
  snap.forEach(m=>{
    const msg=m.val();
    const div=document.createElement("div");
    div.className= msg.user===username?"me":"other";
    div.innerHTML=`<b>${msg.user}</b><p>${msg.text}</p>`;
    box.appendChild(div);
  });
  box.scrollTop=box.scrollHeight;
});

/* ---------- THEME ---------- */
window.toggleTheme=()=>{
  document.body.classList.toggle("dark");
};
</script>

<style>
:root{
  --bg:#f5f5f5;
  --card:#fff;
  --text:#111;
  --me:#dcf8c6;
  --other:#fff;
}
body.dark{
  --bg:#0f172a;
  --card:#020617;
  --text:#f8fafc;
  --me:#14532d;
  --other:#1e293b;
}
body{
  margin:0;
  font-family:system-ui;
  background:var(--bg);
  color:var(--text);
}
#login{
  height:100vh;
  display:flex;
  align-items:center;
  justify-content:center;
}
.login-box{
  background:var(--card);
  padding:20px;
  width:90%;
  max-width:320px;
  border-radius:12px;
}
#app{
  display:none;
  height:100vh;
}
.sidebar{
  width:30%;
  background:var(--card);
  overflow:auto;
}
.chat{
  flex:1;
  display:flex;
  flex-direction:column;
}
header{
  padding:10px;
  background:var(--card);
  display:flex;
  justify-content:space-between;
}
#messages{
  flex:1;
  overflow:auto;
  padding:10px;
}
.me, .other{
  max-width:75%;
  margin-bottom:10px;
  padding:8px;
  border-radius:10px;
}
.me{background:var(--me);margin-left:auto}
.other{background:var(--other)}
.input{
  display:flex;
  padding:10px;
  background:var(--card);
}
input{
  flex:1;
  padding:10px;
  border-radius:20px;
  border:1px solid #ccc;
}
button{
  margin-left:5px;
  padding:10px 15px;
  border-radius:50%;
  border:none;
  background:#22c55e;
  color:#fff;
}
.user{
  display:flex;
  gap:10px;
  padding:10px;
  border-bottom:1px solid #ddd;
}
.avatar{
  background:#22c55e;
  color:#fff;
  width:35px;
  height:35px;
  border-radius:50%;
  display:flex;
  align-items:center;
  justify-content:center;
}
@media(max-width:768px){
  .sidebar{width:40%}
}
</style>
</head>

<body>
<div id="login">
  <div class="login-box">
    <h3>Live Chat</h3>
    <input id="username" placeholder="Enter username"/>
    <button onclick="login()">Enter</button>
  </div>
</div>

<div id="app">
  <div class="sidebar">
    <h4 style="padding:10px">Active Users</h4>
    <div id="users"></div>
  </div>

  <div class="chat">
    <header>
      <b>Global Chat</b>
      <button onclick="toggleTheme()">🌓</button>
    </header>
    <div id="messages"></div>
    <div class="input">
      <input id="msg" placeholder="Type message"/>
      <button onclick="sendMessage()">➤</button>
    </div>
  </div>
</div>
</body>
</html>
