<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{--bg:#f0f2f5;--card:#fff;--me:#dcf8c6;--other:#fff;--pri:#25D366;--text:#000}
body.dark{--bg:#121212;--card:#1e1e1e;--me:#065f46;--other:#2a2a2a;--pri:#10b981;--text:#fff}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text);scroll-behavior:smooth}
header{position:sticky;top:0;height:60px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 16px;font-weight:700;z-index:1000}
header nav a{color:#fff;margin:0 10px;text-decoration:none;font-weight:500;font-size:14px}
header nav a:hover{text-decoration:underline}
.hero{height:220px;background-size:cover;color:#fff;display:flex;align-items:center;justify-content:center;font-size:24px;text-shadow:0 2px 6px #000;border-radius:8px;margin:8px}
section{padding:12px 16px;margin-bottom:12px}
.card{background:var(--card);padding:12px;border-radius:12px;box-shadow:0 2px 6px rgba(0,0,0,0.1);margin-bottom:12px}
button{cursor:pointer}
.users{display:flex;gap:8px;padding:8px;overflow-x:auto;background:var(--card);border-radius:8px;margin-bottom:8px}
.user{padding:6px 12px;border-radius:20px;background:#00000015;font-size:14px;cursor:pointer;white-space:nowrap;display:flex;align-items:center;gap:4px}
.user.online::before{content:"●";color:#25D366;margin-right:4px}
.user.offline::before{content:"●";color:#ccc;margin-right:4px}
.user.group{background:#00000030}
.chat{display:flex;flex-direction:column;min-height:300px}
.chat-head{padding:10px;background:var(--card);display:flex;justify-content:space-between;font-size:14px;border-radius:8px;margin-bottom:4px;box-shadow:0 2px 4px rgba(0,0,0,0.1)}
.messages{flex:1;overflow-y:auto;padding:8px;background:var(--card);border-radius:8px;box-shadow:inset 0 2px 4px rgba(0,0,0,0.05);margin-bottom:4px}
.msg{max-width:75%;margin-bottom:6px;padding:8px;border-radius:12px;font-size:13px;position:relative;word-wrap:break-word}
.me{background:var(--me);margin-left:auto}
.other{background:var(--other)}
.msg img{max-width:180px;border-radius:8px}
.msg button{position:absolute;top:4px;right:4px;background:red;border:none;color:#fff;border-radius:50%;width:20px;height:20px;cursor:pointer}
.input{display:flex;gap:6px;padding:6px;background:var(--card);border-radius:8px;margin-top:4px}
.input input{flex:1;padding:10px;border-radius:20px;border:1px solid #ccc;font-size:14px}
.input button{width:38px;height:38px;border-radius:50%;border:none;background:var(--pri);color:#fff;font-size:16px;cursor:pointer}
.fab{position:fixed;bottom:20px;right:20px;background:var(--pri);width:50px;height:50px;border-radius:50%;display:flex;align-items:center;justify-content:center;color:#fff;font-size:22px;cursor:pointer;box-shadow:0 4px 10px rgba(0,0,0,0.3)}
.fab:hover{background:#10b981}
.typing{font-size:12px;color:#555;margin-left:4px}
</style>
</head>

<body>

<header>
<div>Live Connect 🚀</div>
<nav>
<a href="#home">Home</a>
<a href="#chat">Chat</a>
<a href="#groups">Groups</a>
<a href="#profile">Profile</a>
<a href="#about">About</a>
<a href="#contact">Contact</a>
<button onclick="document.body.classList.toggle('dark')"><i class="fa fa-moon"></i></button>
</nav>
</header>

<section id="home">
<div class="hero" id="hero">Welcome to Live Connect</div>
<div class="card">
<h3>Features</h3>
<ul>
<li>1:1 Chat & Group Chat</li>
<li>Send Text, Images & Reactions</li>
<li>Dark/Light Mode</li>
<li>Mobile & Desktop Friendly</li>
<li>Notifications & Premium Feel</li>
</ul>
</div>
</section>

<section id="chat">
<h3>Chat</h3>
<div class="users" id="users"></div>
<div class="chat">
  <div class="chat-head">
    <span id="chatWith">Select user</span>
    <span>
      <button onclick="clearChat()">Clear</button>
      <button onclick="delGroup()">Delete Group</button>
    </span>
  </div>
  <div class="messages" id="msgs"></div>
  <div class="typing" id="typing"></div>
  <div class="input">
    <input id="msg" placeholder="Type a message...">
    <input type="file" id="img" accept="image/*" hidden>
    <button onclick="send()">➤</button>
    <button onclick="img.click()">🖼️</button>
    <button onclick="showStickers()">😊</button>
  </div>
</div>
<div class="fab" onclick="createGroup()">👥</div>
</section>

<section id="groups">
<h3>Groups</h3>
<div class="card">
<button onclick="createGroup()">Create Group</button>
</div>
</section>

<section id="profile">
<h3>Profile</h3>
<div class="card">
<p>Name: <span id="profileName">Not Logged In</span></p>
<p>Status: Online</p>
</div>
</section>

<section id="about">
<h3>About</h3>
<div class="card">
<p>Live Connect is a modern chat platform combining WhatsApp & Instagram features in a simple, attractive interface. Stay connected with friends and groups.</p>
</div>
</section>

<section id="contact">
<h3>Contact</h3>
<div class="card">
<p>Email: support@liveconnect.com</p>
<p>Phone: +92 300 0000000</p>
</div>
</section>

<div class="login" id="login">
  <div>
    <h3>Enter your name</h3>
    <input id="name" placeholder="Full Name">
    <button onclick="start()">Continue</button>
  </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";
import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-storage.js";

const firebaseConfig = {
apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
authDomain:"live-chat-b810c.firebaseapp.com",
databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
projectId:"live-chat-b810c",
storageBucket:"live-chat-b810c.appspot.com"
};

const app = initializeApp(firebaseConfig),
db = getDatabase(app),
st = getStorage(app);

let me="", cur="", isGroup=false, creator={};

window.start = () => {
me = document.getElementById("name").value.trim();
if(!me){ alert("Enter name"); return;}
document.getElementById("login").style.display="none";
document.getElementById("profileName").textContent=me;
set(ref(db,"users/"+me),{online:true});
onDisconnect(ref(db,"users/"+me)).set({online:false});
loadUsersAndGroups();
};

function loadUsersAndGroups(){
onValue(ref(db,"users"),snapshot=>{
document.getElementById("users").innerHTML="";
snapshot.forEach(u=>{
if(u.key!==me){
let status = u.val().online ? "online" : "offline";
let d=document.createElement("div");
d.className="user "+status;
d.textContent=u.key;
d.onclick=()=>open(u.key,false);
document.getElementById("users").appendChild(d);
}
});
});
onValue(ref(db,"groups"),snapshot=>{
snapshot.forEach(g=>{
creator[g.key]=g.val().by;
let d=document.createElement("div");
d.className="user group";
d.textContent=g.key;
d.onclick=()=>open(g.key,true);
document.getElementById("users").appendChild(d);
});
});
}

window.open=(u,g)=>{
cur=u; isGroup=g;
document.getElementById("chatWith").textContent=u;
let p = g ? "groupChats/"+u : "chats/"+[me,u].sort().join("_");
onValue(ref(db,p),snapshot=>{
const msgs=document.getElementById("msgs");
msgs.innerHTML="";
snapshot.forEach(m=>{
let d=document.createElement("div");
d.className="msg "+(m.val().from===me?"me":"other");
d.innerHTML=m.val().img?`<img src="${m.val().img}">`:m.val().text||"";
if(m.val().from===me){
let b=document.createElement("button");
b.textContent="🗑️";
b.onclick=()=>remove(ref(db,p+"/"+m.key));
d.appendChild(b);
}
msgs.appendChild(d);
});
msgs.scrollTop=msgs.scrollHeight;
});
};

window.send=()=>{
if(!cur)return;
if(msg.value.trim()!="")
push(ref(db,isGroup?"groupChats/"+cur:"chats/"+[me,cur].sort().join("_")),{from:me,text:msg.value});
msg.value="";
};

img.onchange=async()=>{
let f=img.files[0];
let r=sRef(st,"imgs/"+Date.now()+f.name);
await uploadBytes(r,f);
let u=await getDownloadURL(r);
push(ref(db,isGroup?"groupChats/"+cur:"chats/"+[me,cur].sort().join("_")),{from:me,img:u});
};

window.clearChat=()=>remove(ref(db,isGroup?"groupChats/"+cur:"chats/"+[me,cur].sort().join("_")));
window.delGroup=()=>{if(isGroup && creator[cur]===me)remove(ref(db,"groups/"+cur))};
window.createGroup=()=>{
let g=prompt("Enter group name:");
if(!g)return;
set(ref(db,"groups/"+g),{by:me});
alert("Group created!");
};
window.showStickers=()=>alert("Sticker/GIF panel coming soon!");

const imgs=[
"https://images.unsplash.com/photo-1525182008055-f88b95ff7980",
"https://images.unsplash.com/photo-1515378791036-0648a3ef77b2",
"https://images.unsplash.com/photo-1492724441997-5dc865305da7"
];
let i=0;
setInterval(()=>document.getElementById("hero").style.backgroundImage=`url(${imgs[i++%imgs.length]})`,3000);
</script>
</body>
</html>
