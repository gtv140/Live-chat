<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<title>Live Chat</title>

<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

<style>
:root{
--bg:#efeae2;--panel:#fff;--text:#111;
--me:#dcf8c6;--other:#fff;--accent:#008069
}
body.dark{
--bg:#0b141a;--panel:#111b21;--text:#e9edef;
--me:#005c4b;--other:#1f2c34
}
*{box-sizing:border-box;font-family:system-ui}
html,body{height:100%;margin:0;background:var(--bg);color:var(--text)}

.app{height:100%;display:flex;flex-direction:column}

/* HEADER */
.top{
height:56px;background:var(--panel);
display:flex;align-items:center;
justify-content:space-between;
padding:0 14px;border-bottom:1px solid #ccc
}
.title{font-weight:600}
body.dark .top{border-color:#222}

/* USERS */
.users{
display:flex;overflow-x:auto;
background:var(--panel);
border-bottom:1px solid #ccc
}
.user{
padding:10px 14px;cursor:pointer;
display:flex;flex-direction:column;
font-size:13px;min-width:110px
}
.user.active{background:#e9edef}
body.dark .user.active{background:#1f2c34}
.dot{width:8px;height:8px;border-radius:50%;background:#22c55e;margin-bottom:4px}

/* CHAT */
.chat{
flex:1;overflow-y:auto;
padding:12px;display:flex;
flex-direction:column;gap:6px
}
.msg{
max-width:78%;padding:8px 12px;
border-radius:8px;font-size:14px;
animation:fade .2s
}
@keyframes fade{from{opacity:0;transform:translateY(4px)}}
.me{align-self:flex-end;background:var(--me)}
.other{align-self:flex-start;background:var(--other)}
.time{font-size:10px;opacity:.6;margin-top:2px}

/* INPUT */
.input{
display:flex;gap:8px;
padding:8px;background:var(--panel);
padding-bottom:calc(8px + env(safe-area-inset-bottom))
}
.input input{
flex:1;padding:10px 14px;
border-radius:20px;border:1px solid #aaa;
background:transparent;color:var(--text)
}
.input button{
width:44px;border:none;
border-radius:50%;
background:var(--accent);
color:#fff;font-size:18px
}

.typing{font-size:12px;padding:0 12px;opacity:.6}
</style>
</head>

<body>
<div class="app">
  <div class="top">
    <div class="title" id="title">Live Chat</div>
    <button onclick="toggle()">🌙</button>
  </div>

  <div class="users" id="users"></div>

  <div class="typing" id="typing"></div>

  <div class="chat" id="chat"></div>

  <div class="input">
    <input id="text" placeholder="Type message…" oninput="typingOn()">
    <button onclick="send()">➤</button>
  </div>
</div>

<script>
firebase.initializeApp({
apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com"
});
const db=firebase.database();

let me=prompt("Enter username");
if(!me) location.reload();

let current="global";
db.ref("users/"+me).set({online:true});
window.onbeforeunload=()=>db.ref("users/"+me).remove();

/* USERS */
db.ref("users").on("value",s=>{
users.innerHTML="";
Object.keys(s.val()||{}).forEach(u=>{
let d=document.createElement("div");
d.className="user"+(u==me?" active":"");
d.innerHTML=`<div class="dot"></div>${u}`;
d.onclick=()=>openChat(u);
users.appendChild(d);
});
});

/* CHAT */
function openChat(u){
current=[me,u].sort().join("_");
title.innerText="Chat with "+u;
chat.innerHTML="";
db.ref("chats/"+current).off();
db.ref("chats/"+current).on("child_added",s=>{
let m=s.val();
let d=document.createElement("div");
d.className="msg "+(m.from==me?"me":"other");
d.innerHTML=`${m.text}<div class="time">${new Date(m.time).toLocaleTimeString()}</div>`;
chat.appendChild(d);
chat.scrollTop=chat.scrollHeight;
});
}

/* SEND */
function send(){
if(!text.value.trim())return;
db.ref("chats/"+current).push({
from:me,text:text.value,time:Date.now()
});
text.value="";
typing.innerText="";
}

/* TYPING */
let t;
function typingOn(){
db.ref("typing/"+current+"/"+me).set(true);
clearTimeout(t);
t=setTimeout(()=>db.ref("typing/"+current+"/"+me).remove(),800);
}
db.ref("typing").on("value",s=>{
let v=s.val()||{};
typing.innerText=Object.keys(v[current]||{})
.filter(u=>u!==me).length?"Typing…":"";
});

/* THEME */
function toggle(){
document.body.classList.toggle("dark");
}
</script>
</body>
</html>
