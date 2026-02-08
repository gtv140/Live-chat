<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Chat</title>

<!-- Firebase compat (GitHub Pages SAFE) -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-auth-compat.js"></script>

<style>
*{margin:0;padding:0;box-sizing:border-box;font-family:Arial,sans-serif}
body{background:#f0f2f5;height:100vh}
body.dark{background:#0f172a;color:#e5e7eb}

.container{display:flex;height:100vh}
.sidebar{
  width:260px;background:#fff;border-right:1px solid #ddd;
  display:flex;flex-direction:column
}
body.dark .sidebar{background:#020617;border-color:#1e293b}

.sidebar-header{
  padding:15px;font-weight:bold;display:flex;
  justify-content:space-between;align-items:center
}

.users{flex:1;overflow-y:auto}
.user{
  padding:12px;border-bottom:1px solid #eee;
  cursor:pointer
}
body.dark .user{border-color:#1e293b}
.user:hover{background:#e5e7eb}
body.dark .user:hover{background:#1e293b}

.chat{
  flex:1;display:flex;flex-direction:column
}
.chat-header{
  padding:15px;background:#fff;border-bottom:1px solid #ddd
}
body.dark .chat-header{background:#020617;border-color:#1e293b}

.messages{
  flex:1;padding:15px;overflow-y:auto;background:#e5ddd5
}
body.dark .messages{background:#020617}

.msg{
  max-width:70%;margin-bottom:10px;padding:8px 12px;
  border-radius:8px;clear:both;font-size:14px
}
.me{background:#2563eb;color:#fff;float:right}
.other{background:#fff;float:left}
body.dark .other{background:#1e293b;color:#f8fafc}

.input{
  display:flex;padding:10px;background:#fff
}
body.dark .input{background:#020617}
.input input{
  flex:1;padding:10px;border-radius:20px;
  border:1px solid #ccc
}
.input button{
  margin-left:10px;padding:10px 16px;
  border:none;border-radius:50%;
  background:#2563eb;color:#fff;cursor:pointer
}

/* MOBILE FIX */
@media(max-width:768px){
  .container{flex-direction:column}
  .sidebar{
    width:100%;height:60px;
    flex-direction:row;overflow-x:auto
  }
  .users{display:flex}
  .user{min-width:100px;text-align:center}
}
</style>
</head>

<body>
<div class="container">
  <div class="sidebar">
    <div class="sidebar-header">
      <span>Active (<span id="count">0</span>)</span>
      <button onclick="toggleTheme()">🌙</button>
    </div>
    <div class="users" id="users"></div>
  </div>

  <div class="chat">
    <div class="chat-header" id="chatWith">Welcome</div>
    <div class="messages" id="messages"></div>
    <div class="input">
      <input id="msg" placeholder="Type message">
      <button onclick="send()">➤</button>
    </div>
  </div>
</div>

<script>
/* FIREBASE CONFIG */
firebase.initializeApp({
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c"
});

const db=firebase.database();
let me=prompt("Enter username");
if(!me) location.reload();

firebase.auth().signInAnonymously();
db.ref("users/"+me).set(true);
window.onbeforeunload=()=>db.ref("users/"+me).remove();

/* USERS */
db.ref("users").on("value",s=>{
  let u=s.val()||{};
  users.innerHTML="";
  count.innerText=Object.keys(u).length;
  Object.keys(u).forEach(n=>{
    let d=document.createElement("div");
    d.className="user";
    d.innerText=n;
    d.onclick=()=>openChat(n);
    users.appendChild(d);
  });
});

let current="global";

/* CHAT */
function openChat(u){
  current=[me,u].sort().join("_");
  chatWith.innerText="Chat with "+u;
  messages.innerHTML="";
  db.ref("chats/"+current).off();
  db.ref("chats/"+current).on("child_added",s=>{
    let m=s.val();
    let d=document.createElement("div");
    d.className="msg "+(m.from==me?"me":"other");
    d.innerText=m.text;
    messages.appendChild(d);
    messages.scrollTop=messages.scrollHeight;
  });
}

function send(){
  let t=msg.value.trim();
  if(!t)return;
  db.ref("chats/"+current).push({from:me,text:t});
  msg.value="";
}

/* THEME */
function toggleTheme(){
  document.body.classList.toggle("dark");
}
</script>
</body>
</html>
