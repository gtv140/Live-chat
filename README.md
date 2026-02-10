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
header{height:50px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 12px;font-weight:700;position:sticky;top:0;z-index:1000;font-size:18px;box-shadow:0 2px 6px rgba(0,0,0,0.2);}
.container{padding:8px;max-width:600px;margin:auto;}
.page{display:none;}
.page.active{display:block;}
.hero{position:relative;height:160px;border-radius:12px;display:flex;align-items:center;justify-content:center;color:var(--text);text-shadow:0 2px 6px rgba(0,0,0,0.7);margin-bottom:12px;overflow:hidden;}
.hero::after{content:"";position:absolute;top:0;left:0;width:100%;height:100%;background:linear-gradient(to bottom, rgba(0,0,0,0.3), rgba(0,0,0,0.3));z-index:1;}
.hero img{position:absolute;top:0;left:0;width:100%;height:100%;object-fit:cover;z-index:0;filter:brightness(0.7);}
.hero-content{position:relative;z-index:2;text-align:center;padding:12px;}
.card{background:var(--card);padding:12px;border-radius:12px;box-shadow:0 2px 6px rgba(0,0,0,0.1);margin-bottom:12px;}
ul{padding-left:0;list-style:none;} li{margin-bottom:4px;font-size:14px;}
button{cursor:pointer;padding:10px 12px;border:none;border-radius:8px;background:var(--pri);color:#fff;margin:2px;font-size:16px;}
button:hover{opacity:0.9;}
input,textarea{padding:10px;border-radius:8px;border:1px solid #ccc;margin-bottom:4px;width:100%;font-size:14px;}
img{max-width:100%;border-radius:8px;}
.msg{padding:8px;margin:4px 0;border-radius:12px;word-wrap:break-word;font-size:14px;}
.msg.me{background:#dcf8c6;text-align:right;}
.msg.other{background:#fff;text-align:left;}
.user{padding:8px 12px;border-radius:12px;background:#eee;cursor:pointer;white-space:nowrap;margin:0 4px;flex-shrink:0;display:flex;align-items:center;gap:6px;font-size:14px;}
.user.online::before{content:"🟢";}
.user.offline::before{content:"⚪";}
#users,#groupList,#currentUsers{display:flex;overflow-x:auto;padding:4px 0;}
.bottom-nav{position:fixed;bottom:0;left:0;width:100%;display:flex;justify-content:space-around;background:var(--pri);color:#fff;height:60px;align-items:center;z-index:1000;border-top-left-radius:12px;border-top-right-radius:12px;}
.bottom-nav i{font-size:24px;cursor:pointer;}
@media(max-width:480px){.hero{height:120px;font-size:14px;} input,textarea,button{font-size:14px;padding:8px;} .card{padding:8px;} .msg{font-size:13px;}}
</style>
</head>
<body>

<header>
<div>Live Connect 🚀</div>
<button onclick="document.body.classList.toggle('dark')"><i class="fa fa-moon"></i></button>
</header>

<div class="container">

<!-- Login -->
<div id="loginPage" class="page active">
<div class="card">
<h3>Enter Username</h3>
<input type="text" id="usernameInput" placeholder="Username">
<button onclick="login()">Enter</button>
</div>
</div>

<!-- Home Page -->
<div id="home" class="page">

<!-- Hero Section -->
<div class="hero">
  <img src="https://images.unsplash.com/photo-1525182008055-f88b95ff7980?auto=format&fit=crop&w=800&q=60" alt="Live Connect Hero">
  <div class="hero-content">
    <h1 style="font-size:26px;font-weight:700;margin-bottom:6px;">Live Connect 🚀</h1>
    <p style="font-size:15px;margin-bottom:12px;">Secure, Modern & Mobile-Friendly Real-Time Chat</p>
    <div style="margin-top:12px;">
      <button onclick="showPage('chat')" style="margin-right:6px;">💬 Start Chatting</button>
      <button onclick="showPage('profile')">👤 Create Profile</button>
    </div>
  </div>
</div>

<!-- About + Support + Social -->
<div class="card" style="text-align:center;">
  <p style="margin:4px;">About: Live Connect — Modern chat platform</p>
  <p style="margin:4px;">Support: <a href="mailto:webhub262@gmail.com" style="color:#0d6efd;">webhub262@gmail.com</a></p>
  <div style="display:flex;gap:12px;margin-top:8px;justify-content:center;">
    <a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank" style="font-size:20px;color:#3b5998;"><i class="fab fa-facebook-square"></i></a>
    <a href="https://www.instagram.com/mr_nazim073?igsh=MXd4d2hmcWNvNjVsdQ==" target="_blank" style="font-size:20px;color:#e1306c;"><i class="fab fa-instagram"></i></a>
    <a href="https://youtube.com/@crazykhantv?si=KxpJG79rAEBUQuZn" target="_blank" style="font-size:20px;color:#ff0000;"><i class="fab fa-youtube"></i></a>
  </div>
</div>

<!-- Key Features Section -->
<div class="card">
<h3 style="text-align:center;font-size:18px;margin-bottom:8px;">✨ Key Features</h3>
<div style="display:flex;flex-wrap:wrap;gap:8px;justify-content:center;">
  <div class="card" style="flex:1 1 120px;min-width:120px;text-align:center;">
    <i class="fa fa-comment fa-2x" style="color:#25D366;"></i>
    <p>Real-Time Chat</p>
  </div>
  <div class="card" style="flex:1 1 120px;min-width:120px;text-align:center;">
    <i class="fa fa-users fa-2x" style="color:#10b981;"></i>
    <p>Online Users</p>
  </div>
  <div class="card" style="flex:1 1 120px;min-width:120px;text-align:center;">
    <i class="fa fa-user fa-2x" style="color:#3b82f6;"></i>
    <p>Profile & Status</p>
  </div>
  <div class="card" style="flex:1 1 120px;min-width:120px;text-align:center;">
    <i class="fa fa-moon fa-2x" style="color:#facc15;"></i>
    <p>Dark/Light Mode</p>
  </div>
</div>
</div>

</div>

<!-- Chat -->
<div id="chat" class="page">
<div class="card">
<h3>Chat</h3>
<div id="users"></div>
<div id="msgs" style="height:250px;overflow-y:auto;border:1px solid #ccc;padding:6px;border-radius:8px;margin-bottom:8px;"></div>
<input type="text" id="msg" placeholder="Type message...">
<input type="file" id="img" accept="image/*" hidden>
<button onclick="sendMsg()">➤</button>
<button onclick="img.click()">🖼️</button>
<button onclick="clearChat()">Clear Chat</button>
</div>
</div>

<!-- Groups -->
<div id="groups" class="page">
<div class="card">
<h3>Groups</h3>
<button onclick="createGroup()">+ Create Group</button>
<div id="groupList"></div>
</div>
</div>

<!-- Profile -->
<div id="profile" class="page">
<div class="card">
<h3>Profile</h3>
<img src="https://via.placeholder.com/80" id="avatar" style="border-radius:50%;width:80px;height:80px;margin-bottom:6px;">
<input type="file" id="avatarInput" accept="image/*">
<button onclick="uploadAvatar()">Upload Avatar</button>
<textarea id="status" placeholder="Status..."></textarea>
<button onclick="updateStatus()">Update Status</button>
</div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";
import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-storage.js";

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
const st = getStorage(app);

let currentUser=null, curChat="", isGroup=false;
const msgsDiv=document.getElementById("msgs");

function showPage(p){
  document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active'));
  document.getElementById(p).classList.add('active');
}
window.showPage = showPage;

const currentUsersDiv=document.getElementById("currentUsers");
onValue(ref(db,"users"), snap=>{
  if(currentUsersDiv) currentUsersDiv.innerHTML="";
  const usersDiv=document.getElementById("users"); if(usersDiv) usersDiv.innerHTML="";
  snap.forEach(u=>{
    if(u.key!==currentUser){
      if(usersDiv){
        const d=document.createElement("div");
        d.className="user "+(u.val().online?"online":"offline");
        d.textContent=u.val().name;
        d.onclick=()=>openChat(u.key,false);
        usersDiv.appendChild(d);
      }
    }
    if(currentUsersDiv){
      const d=document.createElement("div");
      d.className="user "+(u.val().online?"online":"offline");
      d.style.padding="6px 10px"; d.style.background="#fff"; d.style.borderRadius="12px"; d.style.fontSize="12px"; d.style.color="#000";
      d.textContent=u.val().name + (u.val().status?" — "+u.val().status:"");
      currentUsersDiv.appendChild(d);
    }
  });
});

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username"); return;}
  currentUser=uname;
  set(ref(db,"users/"+uname),{name:uname,online:true,avatar:"https://via.placeholder.com/80"});
  showPage('home');
};

window.openChat=(u,g)=>{
  curChat=u; isGroup=g;
  const path=g?"groupChats/"+u:"chats/"+[currentUser,u].sort().join("_");
  onValue(ref(db,path), snap=>{
    msgsDiv.innerHTML="";
    snap.forEach(m=>{
      const div=document.createElement("div");
      div.className="msg "+(m.val().from===currentUser?"me":"other");
      div.innerHTML=m.val().img?`<img src="${m.val().img}">`:m.val().text;
      if(m.val().from===currentUser){const b=document.createElement("button");b.textContent="🗑️";b.onclick=()=>remove(ref(db,path+"/"+m.key)); div.appendChild(b);}
      msgsDiv.appendChild(div);
      msgsDiv.scrollTop=msgsDiv.scrollHeight;
    });
  });
};

window.sendMsg=()=>{
  if(!curChat) return;
  const text=document.getElementById("msg").value.trim(); if(!text) return;
  push(ref(db,isGroup?"groupChats/"+curChat:"chats/"+[currentUser,curChat].sort().join("_")),{from:currentUser,text});
  document.getElementById("msg").value="";
};

document.getElementById("img").onchange=async()=>{
  const file=document.getElementById("img").files[0]; if(!file||!curChat) return;
  const r=sRef(st,"imgs/"+Date.now()+file.name);
  await uploadBytes(r,file); const url=await getDownloadURL(r);
  push(ref(db,isGroup?"groupChats/"+curChat:"chats/"+[currentUser,curChat].sort().join("_")),{from:currentUser,img:url});
};

window.createGroup=()=>{
  const g=prompt("Enter group name:"); if(!g) return;
  set(ref(db,"groups/"+g),{by:currentUser,members:{[currentUser]:true}}); alert("Group created!");
};

const avatarImg=document.getElementById("avatar");
const avatarInput=document.getElementById("avatarInput");
const statusInput=document.getElementById("status");

window.uploadAvatar=async()=>{
  const f=avatarInput.files[0]; if(!f) return alert("Select image");
  const r=sRef(st,"avatars/"+currentUser+"_"+Date.now()+f.name);
  await uploadBytes(r,f); const u=await getDownloadURL(r);
  set(ref(db,"users/"+currentUser+"/avatar"),u); avatarImg.src=u; alert("Avatar updated!");
};

window.updateStatus=()=>{set(ref(db,"users/"+currentUser+"/status"),statusInput.value.trim()); alert("Status updated!");};

window.clearChat=()=>{if(!curChat) return; remove(ref(db,isGroup?"groupChats/"+curChat:"chats/"+[currentUser,curChat].sort().join("_")));}
</script>
</body>
</html>
