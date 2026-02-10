<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{--bg:#f0f2f5;--card:#fff;--me:#dcf8c6;--other:#fff;--pri:#25D366;--text:#000}
body.dark{--bg:#121212;--card:#1e1e1e;--me:#065f46;--other:#2a2a2a;--pri:#10b981;--text:#fff}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text)}
header{position:sticky;top:0;height:60px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 16px;font-weight:700;z-index:1000}
header nav a{color:#fff;margin:0 10px;text-decoration:none;font-weight:500;font-size:14px;cursor:pointer}
header nav a:hover{text-decoration:underline}
.container{padding:12px}
.page{display:none}
.page.active{display:block}
.hero{height:180px;background-size:cover;color:#fff;display:flex;align-items:center;justify-content:center;font-size:20px;text-shadow:0 2px 6px #000;border-radius:12px;margin-bottom:12px}
.users{display:flex;gap:8px;padding:8px;overflow-x:auto;background:var(--card);border-radius:8px;margin-bottom:8px}
.user{padding:6px 12px;border-radius:20px;background:#00000015;font-size:14px;cursor:pointer;white-space:nowrap;display:flex;align-items:center;gap:4px}
.user.online::before{content:"●";color:#25D366;margin-right:4px}
.user.offline::before{content:"●";color:#ccc;margin-right:4px}
.user.group{background:#00000030}
.chat{display:flex;flex-direction:column;height:70vh;max-height:700px;margin-bottom:12px}
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
button{cursor:pointer;padding:6px 12px;border-radius:8px;border:none;background:var(--pri);color:#fff;font-weight:600}
.card{background:var(--card);padding:12px;border-radius:12px;box-shadow:0 2px 6px rgba(0,0,0,0.1);margin-bottom:12px}
input,textarea{width:100%;padding:10px;margin:6px 0;border-radius:8px;border:1px solid #ccc;font-size:14px}
img.avatar{width:80px;height:80px;border-radius:50%;object-fit:cover;margin-bottom:8px;display:block}
</style>
</head>

<body>

<header>
<div>Live Connect 🚀</div>
<nav>
<a onclick="showPage('home')">Home</a>
<a onclick="showPage('chat')">Chat</a>
<a onclick="showPage('groups')">Groups</a>
<a onclick="showPage('profile')">Profile</a>
<a onclick="showPage('about')">About</a>
<a onclick="showPage('contact')">Contact</a>
<button onclick="document.body.classList.toggle('dark')"><i class="fa fa-moon"></i></button>
</nav>
</header>

<div class="container">

<!-- Home Page -->
<div id="home" class="page active">
<div class="hero" id="hero">Welcome to Live Connect 🚀</div>
<div class="card"><h3>Features</h3>
<ul>
<li>1:1 & Group Chat with Images</li>
<li>Online/Offline Status</li>
<li>Profile Avatar & Status</li>
<li>Dark/Light Mode Toggle</li>
<li>Responsive Design</li>
<li>Modern, Premium UI</li>
</ul></div>
</div>

<!-- Chat Page -->
<div id="chat" class="page">
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
</div><!-- Part 2 – Continue inside same HTML after Part 1 -->

<!-- Groups Page -->
<div id="groups" class="page">
<h2>Groups</h2>
<button onclick="createGroup()">+ Create New Group</button>
<div id="groupList"></div>
</div>

<!-- Profile Page -->
<div id="profile" class="page">
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

<!-- About Page -->
<div id="about" class="page">
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

<!-- Contact Page -->
<div id="contact" class="page">
<h2>Contact Us</h2>
<div class="card">
<label>Your Name</label>
<input type="text" id="cname" placeholder="Full Name">
<label>Email</label>
<input type="email" id="cemail" placeholder="Email Address">
<label>Message</label>
<textarea id="cmessage" placeholder="Write your message..."></textarea>
<button onclick="sendContact()">Send Message</button>
</div>
</div>

</div> <!-- End of container -->

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";
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
if(!me) me = "Guest_"+Date.now();

// Page Navigation
function showPage(pagestr){
document.querySelectorAll(".page").forEach(p=>p.classList.remove("active"));
document.getElementById(pagestr).classList.add("active");
}

// Hero Slider
const heroImgs=[
"https://images.unsplash.com/photo-1525182008055-f88b95ff7980",
"https://images.unsplash.com/photo-1515378791036-0648a3ef77b2",
"https://images.unsplash.com/photo-1492724441997-5dc865305da7"
];
let i=0;
setInterval(()=>{const h=document.getElementById("hero"); if(h) h.style.backgroundImage=`url(${heroImgs[i++%heroImgs.length]})`;},3000);

// Chat System
const usersDiv = document.getElementById("users");
const msgsDiv = document.getElementById("msgs");
let cur="", isGroup=false, creator={};

set(ref(db,"users/"+me),{online:true});
onValue(ref(db,"users"),snap=>{
usersDiv.innerHTML="";
snap.forEach(u=>{
if(u.key!==me){
let d=document.createElement("div");
d.className="user "+(u.val().online?"online":"offline");
d.textContent = u.key;
d.onclick = ()=>openChat(u.key,false);
usersDiv.appendChild(d);
}
});
});

onValue(ref(db,"groups"),snap=>{
snap.forEach(g=>{
creator[g.key]=g.val().by;
let d=document.createElement("div");
d.className="user group";
d.textContent=g.key;
d.onclick = ()=>openChat(g.key,true);
usersDiv.appendChild(d);
});
});

window.openChat=(u,g)=>{
cur=u; isGroup=g;
document.getElementById("chatWith").textContent=u;
let path = g?"groupChats/"+u:"chats/"+[me,u].sort().join("_");
onValue(ref(db,path),snap=>{
msgsDiv.innerHTML="";
snap.forEach(m=>{
let div=document.createElement("div");
div.className="msg "+(m.val().from===me?"me":"other");
div.innerHTML = m.val().img?`<img src="${m.val().img}">`:m.val().text;
if(m.val().from===me){
let b=document.createElement("button");
b.textContent="🗑️";
b.onclick=()=>remove(ref(db,path+"/"+m.key));
div.appendChild(b);
}
msgsDiv.appendChild(div);
msgsDiv.scrollTop=msgsDiv.scrollHeight;
});
});
};

window.send=()=>{
if(!cur) return;
let text = document.getElementById("msg").value.trim();
if(!text) return;
push(ref(db,isGroup?"groupChats/"+cur:"chats/"+[me,cur].sort().join("_")),{from:me,text});
document.getElementById("msg").value="";
};

document.getElementById("img").onchange = async()=>{
let file = document.getElementById("img").files[0];
if(!file) return;
let r = sRef(st,"imgs/"+Date.now()+file.name);
await uploadBytes(r,file);
let url = await getDownloadURL(r);
push(ref(db,isGroup?"groupChats/"+cur:"chats/"+[me,cur].sort().join("_")),{from:me,img:url});
};

// Chat Clear / Delete Group
window.clearChat=()=>{if(!cur) return; remove(ref(db,isGroup?"groupChats/"+cur:"chats/"+[me,cur].sort().join("_")));};
window.delGroup=()=>{if(isGroup && creator[cur]===me) remove(ref(db,"groups/"+cur));};
window.createGroup=()=>{let g=prompt("Enter group name:"); if(!g) return; set(ref(db,"groups/"+g),{by:me}); alert("Group created!");};

// Profile Page
const avatarImg = document.getElementById("avatar");
const avatarInput = document.getElementById("avatarInput");
const statusInput = document.getElementById("status");
get(ref(db,"users/"+me)).then(snap=>{if(snap.exists()){let d=snap.val(); if(d.avatar) avatarImg.src=d.avatar; if(d.status) statusInput.value=d.status;}});
window.uploadAvatar=async()=>{let f=avatarInput.files[0]; if(!f){alert("Select image");return;} let r=sRef(st,"avatars/"+me+"_"+Date.now()+f.name); await uploadBytes(r,f); let u=await getDownloadURL(r); set(ref(db,"users/"+me+"/avatar"),u); avatarImg.src=u; alert("Avatar updated!");}
window.updateStatus=()=>{let t=statusInput.value.trim(); if(!t){alert("Enter status"); return;} set(ref(db,"users/"+me+"/status"),t); alert("Status updated!");};

// Contact Page
window.sendContact=()=>{let n=document.getElementById("cname").value.trim(); let e=document.getElementById("cemail").value.trim(); let m=document.getElementById("cmessage").value.trim(); if(!n||!e||!m){alert("Please fill all fields");return;} alert("Thanks "+n+"! Your message has been sent."); document.getElementById("cname").value=""; document.getElementById("cemail").value=""; document.getElementById("cmessage").value="";};
</script>

</body>
</html>
