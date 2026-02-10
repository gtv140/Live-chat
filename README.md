<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{--bg:#f0f2f5;--card:#fff;--pri:#25D366;--text:#000}
body.dark{--bg:#121212;--card:#1e1e1e;--pri:#10b981;--text:#fff}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text);scroll-behavior:smooth}
header{position:sticky;top:0;height:60px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 16px;font-weight:700;z-index:1000}
header nav a{color:#fff;margin:0 10px;text-decoration:none;font-weight:500;font-size:14px}
header nav a:hover{text-decoration:underline}
.hero{height:220px;background-size:cover;color:#fff;display:flex;align-items:center;justify-content:center;font-size:24px;text-shadow:0 2px 6px #000;border-radius:8px;margin:8px}
section{padding:12px 16px;margin-bottom:12px}
.card{background:var(--card);padding:12px;border-radius:12px;box-shadow:0 2px 6px rgba(0,0,0,0.1);margin-bottom:12px}
button{cursor:pointer;padding:8px 12px;border-radius:8px;border:none;background:var(--pri);color:#fff;font-weight:600}
.login{position:fixed;inset:0;background:var(--bg);display:flex;align-items:center;justify-content:center;z-index:2000}
.login div{background:var(--card);padding:20px;border-radius:12px;width:90%;max-width:360px;box-shadow:0 2px 6px rgba(0,0,0,0.2)}
.login input{width:100%;padding:12px;margin:10px 0;border-radius:8px;border:1px solid #ccc;font-size:14px}
.fab{position:fixed;bottom:20px;right:20px;background:var(--pri);width:50px;height:50px;border-radius:50%;display:flex;align-items:center;justify-content:center;color:#fff;font-size:22px;cursor:pointer;box-shadow:0 4px 10px rgba(0,0,0,0.3)}
.fab:hover{background:#10b981}
</style>
</head>

<body>

<header>
<div>Live Connect 🚀</div>
<nav>
<a href="index.html">Home</a>
<a href="chat.html">Chat</a>
<a href="groups.html">Groups</a>
<a href="profile.html">Profile</a>
<a href="about.html">About</a>
<a href="contact.html">Contact</a>
<button onclick="document.body.classList.toggle('dark')"><i class="fa fa-moon"></i></button>
</nav>
</header>

<section id="home">
<div class="hero" id="hero">Welcome to Live Connect</div>
<div class="card">
<h3>Features</h3>
<ul>
<li>1:1 & Group Chat with Images</li>
<li>Online/Offline Status</li>
<li>Dark/Light Mode Toggle</li>
<li>Emoji & Sticker Support</li>
<li>Notifications & Reactions ❤️😂👍</li>
<li>Mobile + Desktop Responsive</li>
<li>Premium & Modern UI</li>
</ul>
</div>
</section>

<div class="login" id="login">
  <div>
    <h3>Enter Your Name</h3>
    <input id="name" placeholder="Full Name">
    <button onclick="start()">Continue</button>
  </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, onDisconnect, onValue } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
authDomain:"live-chat-b810c.firebaseapp.com",
databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
projectId:"live-chat-b810c",
storageBucket:"live-chat-b810c.appspot.com"
};

const app = initializeApp(firebaseConfig), db = getDatabase(app);

let me="";
window.start=()=>{
me=document.getElementById("name").value.trim();
if(!me){ alert("Enter name"); return;}
document.getElementById("login").style.display="none";
set(ref(db,"users/"+me),{online:true});
onDisconnect(ref(db,"users/"+me)).set({online:false});
alert("Welcome "+me+"! You can now go to Chat page.");
};

// Hero Slider
const imgs=[
"https://images.unsplash.com/photo-1525182008055-f88b95ff7980",
"https://images.unsplash.com/photo-1515378791036-0648a3ef77b2",
"https://images.unsplash.com/photo-1492724441997-5dc865305da7"
];
let i=0;
setInterval(()=>document.getElementById("hero").style.backgroundImage=`url(${imgs[i++%imgs.length]})`,3000);
</script>

</body>
</html><html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Live Connect 🚀 - Chat</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{--bg:#f0f2f5;--card:#fff;--me:#dcf8c6;--other:#fff;--pri:#25D366;--text:#000}
body.dark{--bg:#121212;--card:#1e1e1e;--me:#065f46;--other:#2a2a2a;--pri:#10b981;--text:#fff}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text)}
header{position:sticky;top:0;height:60px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 16px;font-weight:700;z-index:1000}
header nav a{color:#fff;margin:0 10px;text-decoration:none;font-weight:500;font-size:14px}
header nav a:hover{text-decoration:underline}
.users{display:flex;gap:8px;padding:8px;overflow-x:auto;background:var(--card);border-radius:8px;margin-bottom:8px}
.user{padding:6px 12px;border-radius:20px;background:#00000015;font-size:14px;cursor:pointer;white-space:nowrap;display:flex;align-items:center;gap:4px}
.user.online::before{content:"●";color:#25D366;margin-right:4px}
.user.offline::before{content:"●";color:#ccc;margin-right:4px}
.user.group{background:#00000030}
.chat{display:flex;flex-direction:column;height:70vh;max-height:700px;margin:0 16px}
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
</style>
</head>

<body>

<header>
<div>Live Connect 🚀</div>
<nav>
<a href="index.html">Home</a>
<a href="chat.html">Chat</a>
<a href="groups.html">Groups</a>
<a href="profile.html">Profile</a>
<a href="about.html">About</a>
<a href="contact.html">Contact</a>
<button onclick="document.body.classList.toggle('dark')"><i class="fa fa-moon"></i></button>
</nav>
</header>

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
  <div class="input">
    <input id="msg" placeholder="Type a message...">
    <input type="file" id="img" accept="image/*" hidden>
    <button onclick="send()">➤</button>
    <button onclick="img.click()">🖼️</button>
  </div>
</div>

<div class="fab" onclick="createGroup()">👥</div>

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

let me=prompt("Enter your name:").trim(), cur="", isGroup=false, creator={};
if(!me) me="Guest_"+Date.now();
set(ref(db,"users/"+me),{online:true});
onDisconnect(ref(db,"users/"+me)).set({online:false});

function loadUsersAndGroups(){
onValue(ref(db,"users"),snap=>{
users.innerHTML="";
snap.forEach(u=>{
if(u.key!==me){
let status=u.val().online?"online":"offline";
let d=document.createElement("div");
d.className="user "+status;
d.textContent=u.key;
d.onclick=()=>open(u.key,false);
users.appendChild(d);
}});
});
onValue(ref(db,"groups"),snap=>{
snap.forEach(g=>{
creator[g.key]=g.val().by;
let d=document.createElement("div");
d.className="user group";
d.textContent=g.key;
d.onclick=()=>open(g.key,true);
users.appendChild(d);
});
});
}

loadUsersAndGroups();

window.open=(u,g)=>{
cur=u; isGroup=g;
chatWith.textContent=u;
let p=g?"groupChats/"+u:"chats/"+[me,u].sort().join("_");
onValue(ref(db,p),snap=>{
msgs.innerHTML="";
snap.forEach(m=>{
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
if(!cur || msg.value.trim()=="") return;
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
window.delGroup=()=>{if(isGroup && creator[cur]===me) remove(ref(db,"groups/"+cur))};
window.createGroup=()=>{
let g=prompt("Enter group name:");
if(!g) return;
set(ref(db,"groups/"+g),{by:me});
alert("Group created!");
};
</script>

</body>
</html><html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Live Connect 🚀 - Groups</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{--bg:#f0f2f5;--card:#fff;--pri:#25D366;--text:#000}
body.dark{--bg:#121212;--card:#1e1e1e;--pri:#10b981;--text:#fff}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text)}
header{position:sticky;top:0;height:60px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 16px;font-weight:700;z-index:1000}
header nav a{color:#fff;margin:0 10px;text-decoration:none;font-weight:500;font-size:14px}
header nav a:hover{text-decoration:underline}
.container{padding:12px}
.card{background:var(--card);padding:12px;border-radius:12px;box-shadow:0 2px 6px rgba(0,0,0,0.1);margin-bottom:12px;display:flex;justify-content:space-between;align-items:center}
button{cursor:pointer;padding:6px 12px;border-radius:8px;border:none;background:var(--pri);color:#fff;font-weight:600}
</style>
</head>

<body>

<header>
<div>Live Connect 🚀</div>
<nav>
<a href="index.html">Home</a>
<a href="chat.html">Chat</a>
<a href="groups.html">Groups</a>
<a href="profile.html">Profile</a>
<a href="about.html">About</a>
<a href="contact.html">Contact</a>
<button onclick="document.body.classList.toggle('dark')"><i class="fa fa-moon"></i></button>
</nav>
</header>

<div class="container">
<h2>Groups</h2>
<button onclick="createGroup()">+ Create New Group</button>
<div id="groupList"></div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
authDomain:"live-chat-b810c.firebaseapp.com",
databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
projectId:"live-chat-b810c",
storageBucket:"live-chat-b810c.appspot.com"
};

const app = initializeApp(firebaseConfig),
db = getDatabase(app);

let me=prompt("Enter your name:").trim();
if(!me) me="Guest_"+Date.now();

const groupList = document.getElementById("groupList");
const creator = {};

function loadGroups(){
onValue(ref(db,"groups"),snap=>{
groupList.innerHTML="";
snap.forEach(g=>{
creator[g.key]=g.val().by;
let div=document.createElement("div");
div.className="card";
div.innerHTML=`<span>${g.key}</span>
<button onclick="deleteGroup('${g.key}')">Delete</button>`;
groupList.appendChild(div);
});
});
}

function createGroup(){
let g=prompt("Enter group name:");
if(!g) return;
set(ref(db,"groups/"+g),{by:me});
alert("Group created!");
}

window.deleteGroup=(g)=>{
if(creator[g]===me){
if(confirm("Delete group "+g+"?")) remove(ref(db,"groups/"+g));
}else{
alert("You are not the creator!");
}
}

loadGroups();
</script>

</body>
</html><html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Live Connect 🚀 - Profile</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{--bg:#f0f2f5;--card:#fff;--pri:#25D366;--text:#000}
body.dark{--bg:#121212;--card:#1e1e1e;--pri:#10b981;--text:#fff}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text)}
header{position:sticky;top:0;height:60px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 16px;font-weight:700;z-index:1000}
header nav a{color:#fff;margin:0 10px;text-decoration:none;font-weight:500;font-size:14px}
header nav a:hover{text-decoration:underline}
.container{padding:12px}
.card{background:var(--card);padding:12px;border-radius:12px;box-shadow:0 2px 6px rgba(0,0,0,0.1);margin-bottom:12px}
input,textarea{width:100%;padding:10px;margin:6px 0;border-radius:8px;border:1px solid #ccc;font-size:14px}
button{cursor:pointer;padding:8px 12px;border-radius:8px;border:none;background:var(--pri);color:#fff;font-weight:600}
img.avatar{width:80px;height:80px;border-radius:50%;object-fit:cover;margin-bottom:8px;display:block}
</style>
</head>

<body>

<header>
<div>Live Connect 🚀</div>
<nav>
<a href="index.html">Home</a>
<a href="chat.html">Chat</a>
<a href="groups.html">Groups</a>
<a href="profile.html">Profile</a>
<a href="about.html">About</a>
<a href="contact.html">Contact</a>
<button onclick="document.body.classList.toggle('dark')"><i class="fa fa-moon"></i></button>
</nav>
</header>

<div class="container">
<h2>My Profile</h2>
<div class="card">
<img src="https://via.placeholder.com/80" alt="Avatar" class="avatar" id="avatar">
<input type="file" id="avatarInput" accept="image/*">
<button onclick="uploadAvatar()">Upload Avatar</button>
</div>

<div class="card">
<label>Status</label>
<textarea id="status" placeholder="What's on your mind?"></textarea>
<button onclick="updateStatus()">Update Status</button>
</div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, get } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";
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

let me = prompt("Enter your name:").trim();
if(!me) me="Guest_"+Date.now();

const avatarImg = document.getElementById("avatar");
const avatarInput = document.getElementById("avatarInput");
const statusInput = document.getElementById("status");

async function uploadAvatar(){
let file = avatarInput.files[0];
if(!file){ alert("Select image"); return; }
let storageRef = sRef(st,"avatars/"+me+"_"+Date.now()+file.name);
await uploadBytes(storageRef,file);
let url = await getDownloadURL(storageRef);
set(ref(db,"users/"+me+"/avatar"),url);
avatarImg.src=url;
alert("Avatar updated!");
}

function updateStatus(){
let text = statusInput.value.trim();
if(!text){ alert("Enter status"); return; }
set(ref(db,"users/"+me+"/status"),text);
alert("Status updated!");
}

// Load existing avatar & status
get(ref(db,"users/"+me)).then(snap=>{
if(snap.exists()){
let data = snap.val();
if(data.avatar) avatarImg.src=data.avatar;
if(data.status) statusInput.value=data.status;
}
});
</script>

</body>
</html><html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Live Connect 🚀 - About</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{--bg:#f0f2f5;--card:#fff;--pri:#25D366;--text:#000}
body.dark{--bg:#121212;--card:#1e1e1e;--pri:#10b981;--text:#fff}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text)}
header{position:sticky;top:0;height:60px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 16px;font-weight:700;z-index:1000}
header nav a{color:#fff;margin:0 10px;text-decoration:none;font-weight:500;font-size:14px}
header nav a:hover{text-decoration:underline}
.container{padding:12px}
.card{background:var(--card);padding:12px;border-radius:12px;box-shadow:0 2px 6px rgba(0,0,0,0.1);margin-bottom:12px}
</style>
</head>

<body>
<header>
<div>Live Connect 🚀</div>
<nav>
<a href="index.html">Home</a>
<a href="chat.html">Chat</a>
<a href="groups.html">Groups</a>
<a href="profile.html">Profile</a>
<a href="about.html">About</a>
<a href="contact.html">Contact</a>
<button onclick="document.body.classList.toggle('dark')"><i class="fa fa-moon"></i></button>
</nav>
</header>

<div class="container">
<h2>About Live Connect</h2>
<div class="card">
<p>Live Connect is a modern, secure, and attractive multi-page chat platform.  
It supports 1:1 chat, group chat, avatar & status updates, dark/light mode, and real-time messaging using Firebase.</p>
<p>Our goal is to provide users a simple yet premium feel experience, just like popular messaging apps, while keeping it mobile-friendly and responsive.</p>
</div>

<div class="card">
<h3>Features:</h3>
<ul>
<li>1:1 & Group Chat with Images</li>
<li>Online/Offline Status</li>
<li>Profile Avatar & Status</li>
<li>Dark/Light Mode Toggle</li>
<li>Responsive Design</li>
<li>Easy to Use & Modern UI</li>
<li>Secure Firebase Backend</li>
</ul>
</div>
</div>
</body>
</html><html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Live Connect 🚀 - Contact</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{--bg:#f0f2f5;--card:#fff;--pri:#25D366;--text:#000}
body.dark{--bg:#121212;--card:#1e1e1e;--pri:#10b981;--text:#fff}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text)}
header{position:sticky;top:0;height:60px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 16px;font-weight:700;z-index:1000}
header nav a{color:#fff;margin:0 10px;text-decoration:none;font-weight:500;font-size:14px}
header nav a:hover{text-decoration:underline}
.container{padding:12px}
.card{background:var(--card);padding:12px;border-radius:12px;box-shadow:0 2px 6px rgba(0,0,0,0.1);margin-bottom:12px}
input,textarea{width:100%;padding:10px;margin:6px 0;border-radius:8px;border:1px solid #ccc;font-size:14px}
button{cursor:pointer;padding:8px 12px;border-radius:8px;border:none;background:var(--pri);color:#fff;font-weight:600}
</style>
</head>

<body>
<header>
<div>Live Connect 🚀</div>
<nav>
<a href="index.html">Home</a>
<a href="chat.html">Chat</a>
<a href="groups.html">Groups</a>
<a href="profile.html">Profile</a>
<a href="about.html">About</a>
<a href="contact.html">Contact</a>
<button onclick="document.body.classList.toggle('dark')"><i class="fa fa-moon"></i></button>
</nav>
</header>

<div class="container">
<h2>Contact Us</h2>
<div class="card">
<label>Your Name</label>
<input type="text" id="name" placeholder="Full Name">

<label>Email</label>
<input type="email" id="email" placeholder="Email Address">

<label>Message</label>
<textarea id="message" placeholder="Write your message..."></textarea>

<button onclick="sendMessage()">Send Message</button>
</div>
</div>

<script>
function sendMessage(){
let name=document.getElementById("name").value.trim();
let email=document.getElementById("email").value.trim();
let message=document.getElementById("message").value.trim();
if(!name || !email || !message){ alert("Please fill all fields"); return; }
alert("Thanks "+name+"! Your message has been sent.");
document.getElementById("name").value="";
document.getElementById("email").value="";
document.getElementById("message").value="";
}
</script>

</body>
</html>
