<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{--bg:#f0f2f5;--card:#fff;--pri:#25D366;--text:#000;}
body.dark{--bg:#121212;--card:#1e1e1e;--pri:#10b981;--text:#fff;}
body{margin:0;font-family:system-ui,sans-serif;background:var(--bg);color:var(--text);}
header{position:sticky;top:0;height:60px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 12px;font-weight:700;z-index:1000;}
header nav a{color:#fff;margin:0 8px;text-decoration:none;font-weight:500;font-size:14px;cursor:pointer;}
header nav a:hover{text-decoration:underline;}
header button{background:none;border:none;color:#fff;cursor:pointer;font-size:16px;}
.container{padding:12px;max-width:960px;margin:auto;}
.hero{height:200px;border-radius:12px;background-size:cover;background-position:center;display:flex;align-items:center;justify-content:center;color:#fff;font-size:22px;text-shadow:0 2px 6px #000;margin-bottom:12px;}
.card{background:var(--card);padding:16px;border-radius:12px;box-shadow:0 2px 6px rgba(0,0,0,0.1);margin-bottom:12px;}
ul{padding-left:20px;} li{margin-bottom:6px;}
button{cursor:pointer;padding:6px 12px;border:none;border-radius:6px;background:var(--pri);color:#fff;margin:2px;}
button:hover{opacity:0.9;}
input,textarea{padding:8px;border-radius:6px;border:1px solid #ccc;margin-bottom:4px;width:calc(100% - 16px);}
img{max-width:100%;border-radius:6px;}
.msg{padding:6px;margin:4px 0;border-radius:6px;}
.msg.me{background:#dcf8c6;text-align:right;}
.msg.other{background:#fff;text-align:left;}
@media(max-width:600px){header nav{display:flex;flex-wrap:wrap;gap:6px;} .hero{font-size:18px;height:150px;}}
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
<div id="home" class="page">
<div class="hero" id="hero">Welcome to Live Connect 🚀</div>
<div class="card">
<h3>Features</h3>
<ul>
<li>1:1 & Group Chat with Images</li>
<li>Online/Offline Status</li>
<li>Profile Avatar & Status</li>
<li>Dark/Light Mode Toggle</li>
<li>Responsive Design for Mobile & Desktop</li>
<li>Modern & Premium UI</li>
<li>Easy-to-Use Multi-page Layout</li>
</ul>
</div>
</div>

<!-- Chat Page -->
<div id="chat" class="page" style="display:none;">
<div class="card">
<h3>Chat with Users</h3>
<div id="users" style="display:flex;gap:6px;overflow-x:auto;margin-bottom:8px;"></div>
<div id="msgs" style="height:300px;overflow-y:auto;border:1px solid #ccc;padding:8px;border-radius:8px;margin-bottom:8px;"></div>
<input type="text" id="msg" placeholder="Type a message...">
<input type="file" id="img" accept="image/*" hidden>
<button onclick="sendMsg()">➤</button>
<button onclick="img.click()">🖼️</button>
<button onclick="clearChat()">Clear Chat</button>
</div>
</div>

<!-- Groups Page -->
<div id="groups" class="page" style="display:none;">
<div class="card">
<h3>Groups</h3>
<button onclick="createGroup()">+ Create Group</button>
<div id="groupList"></div>
</div>
</div>

<!-- Profile Page -->
<div id="profile" class="page" style="display:none;">
<div class="card">
<h3>My Profile</h3>
<img src="https://via.placeholder.com/80" id="avatar" style="border-radius:50%;width:80px;height:80px;">
<input type="file" id="avatarInput" accept="image/*">
<button onclick="uploadAvatar()">Upload Avatar</button>
<textarea id="status" placeholder="Status..."></textarea>
<button onclick="updateStatus()">Update Status</button>
</div>
</div>

<!-- About Page -->
<div id="about" class="page" style="display:none;">
<div class="card">
<h3>About Live Connect</h3>
<p>Live Connect is a modern, secure, mobile-friendly multi-page chat platform with 1:1 & Group chat, images, profile avatars, dark/light mode, and responsive design.</p>
</div>
</div>

<!-- Contact Page -->
<div id="contact" class="page" style="display:none;">
<div class="card">
<h3>Contact Us</h3>
<input type="text" id="cname" placeholder="Name">
<input type="email" id="cemail" placeholder="Email">
<textarea id="cmessage" placeholder="Message"></textarea>
<button onclick="sendContact()">Send</button>
</div>
</div>

</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";
import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-storage.js";

// Firebase config
const firebaseConfig = {
apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
authDomain:"live-chat-b810c.firebaseapp.com",
databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
projectId:"live-chat-b810c",
storageBucket:"live-chat-b810c.appspot.com"
};
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
const st = getStorage(app);

// Multi-page function
function showPage(p){document.querySelectorAll('.page').forEach(pg=>pg.style.display='none');document.getElementById(p).style.display='block';}
window.showPage=showPage;

// Hero slider
const heroImgs=["https://images.unsplash.com/photo-1525182008055-f88b95ff7980","https://images.unsplash.com/photo-1515378791036-0648a3ef77b2","https://images.unsplash.com/photo-1492724441997-5dc865305da7"];
let i=0;
setInterval(()=>{const h=document.getElementById("hero");if(h)h.style.backgroundImage=`url(${heroImgs[i++%heroImgs.length]})`;},3000);

// User login
let me = prompt("Enter your name:").trim(); if(!me) me = "Guest_"+Date.now();
set(ref(db,"users/"+me),{online:true});
onValue(ref(db,"users"),snap=>{
const usersDiv=document.getElementById("users"); usersDiv.innerHTML="";
snap.forEach(u=>{
if(u.key!==me){const d=document.createElement("div");d.className="user "+(u.val().online?"online":"offline");d.textContent=u.key;d.onclick=()=>openChat(u.key,false);usersDiv.appendChild(d);}}});

// Chat
let cur="", isGroup=false;
const msgsDiv = document.getElementById("msgs");
window.openChat=(u,g)=>{cur=u;isGroup=g;let path = g?"groupChats/"+u:"chats/"+[me,u].sort().join("_");onValue(ref(db,path),snap=>{msgsDiv.innerHTML="";snap.forEach(m=>{const div=document.createElement("div");div.className="msg "+(m.val().from===me?"me":"other");div.innerHTML=m.val().img?`<img src="${m.val().img}">`:m.val().text;if(m.val().from===me){const b=document.createElement("button");b.textContent="🗑️";b.onclick=()=>remove(ref(db,path+"/"+m.key));div.appendChild(b);}msgsDiv.appendChild(div);msgsDiv.scrollTop=msgsDiv.scrollHeight;});});};

window.sendMsg=()=>{if(!cur)return;const text=document.getElementById("msg").value.trim();if(!text)return;push(ref(db,isGroup?"groupChats/"+cur:"chats/"+[me,cur].sort().join("_")),{from:me,text});document.getElementById("msg").value="";};
document.getElementById("img").onchange=async()=>{const file=document.getElementById("img").files[0];if(!file)return;const r=sRef(st,"imgs/"+Date.now()+file.name);await uploadBytes(r,file);const url=await getDownloadURL(r);push(ref(db,isGroup?"groupChats/"+cur:"chats/"+[me,cur].sort().join("_")),{from:me,img:url});};

// Groups
window.createGroup=()=>{const g=prompt("Enter group name:");if(!g)return;set(ref(db,"groups/"+g),{by:me});alert("Group created!");};

// Profile
const avatarImg=document.getElementById("avatar");
const avatarInput=document.getElementById("avatarInput");
const statusInput=document.getElementById("status");
window.uploadAvatar=async()=>{const f=avatarInput.files[0];if(!f){alert("Select image");return;}const r=sRef(st,"avatars/"+me+"_"+Date.now()+f.name);await uploadBytes(r,f);const u=await getDownloadURL(r);set(ref(db,"users/"+me+"/avatar"),u);avatarImg.src=u;alert("Avatar updated!");};
window.updateStatus=()=>{set(ref(db,"users/"+me+"/status"),statusInput.value.trim());alert("Status updated!");};

// Contact
window.sendContact=()=>{alert("Thanks! Your message has been sent."); document.getElementById("cname").value=""; document.getElementById("cemail").value=""; document.getElementById("cmessage").value="";}
</script>
