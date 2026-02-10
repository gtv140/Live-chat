<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{--bg:#f0f2f5;--card:#fff;--pri:#25D366;--text:#000;--sec:#10b981;}
body.dark{--bg:#121212;--card:#1e1e1e;--pri:#10b981;--text:#fff;--sec:#3b82f6;}
body{margin:0;font-family:system-ui,sans-serif;background:var(--bg);color:var(--text);}
header{height:50px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 12px;font-weight:700;position:sticky;top:0;z-index:1000;font-size:18px;box-shadow:0 2px 6px rgba(0,0,0,0.2);}
.container{padding:10px;max-width:480px;margin:auto;}
.page{display:none;}
.page.active{display:block;}
.hero{position:relative;height:120px;border-radius:12px;display:flex;align-items:center;justify-content:center;color:var(--text);text-shadow:0 2px 6px rgba(0,0,0,0.7);margin-bottom:10px;overflow:hidden;}
.hero img{position:absolute;top:0;left:0;width:100%;height:100%;object-fit:cover;z-index:0;filter:brightness(0.7);}
.hero-content{position:relative;z-index:2;text-align:center;padding:8px;}
.card{background:var(--card);padding:8px;border-radius:10px;margin-bottom:10px;box-shadow:0 2px 6px rgba(0,0,0,0.15);}
button{cursor:pointer;padding:6px 8px;border:none;border-radius:6px;background:var(--pri);color:#fff;margin:2px;font-size:13px;}
input,textarea{padding:6px;border-radius:6px;border:1px solid #ccc;margin-bottom:4px;width:100%;font-size:13px;}
.msg{padding:6px;margin:4px 0;border-radius:10px;word-wrap:break-word;font-size:12px;display:flex;justify-content:space-between;align-items:center;}
.msg.me{background:#dcf8c6;text-align:right;}
.msg.other{background:#fff;text-align:left;}
.msg button{padding:2px 4px;font-size:10px;margin-left:4px;}
.user{padding:6px 10px;border-radius:10px;background:#eee;cursor:pointer;white-space:nowrap;margin:0 3px;flex-shrink:0;display:flex;align-items:center;gap:4px;font-size:12px;}
.user.online::before{content:"🟢";}
#users,#groupList,#currentUsers{display:flex;overflow-x:auto;padding:4px 0;}
.bottom-nav{position:fixed;bottom:0;left:0;width:100%;display:flex;justify-content:space-around;background:var(--pri);color:#fff;height:50px;align-items:center;z-index:1000;border-top-left-radius:10px;border-top-right-radius:10px;}
.bottom-nav i{font-size:22px;cursor:pointer;}
.active-nav{color:#fff;}
.social-icons a{margin:0 4px;font-size:18px;color:var(--sec);text-decoration:none;}
.social-icons a:hover{opacity:0.8;}
@media(max-width:480px){
  .hero{height:100px;} 
  input,textarea,button{font-size:12px;padding:5px;} 
  .msg{font-size:11px;}
  .bottom-nav i{font-size:20px;}
}
</style>
</head>
<body>

<header>
<div>Live Connect 🚀</div>
<button onclick="document.body.classList.toggle('dark')"><i class="fa fa-moon"></i></button>
</header>

<div class="container">

<!-- Login Page -->
<div id="loginPage" class="page active">
<div class="card">
<h3>Enter Username</h3>
<input type="text" id="usernameInput" placeholder="Username">
<button onclick="login()">Enter</button>
</div>
</div>

<!-- Home Page -->
<div id="home" class="page">
<div class="hero">
  <img src="https://images.unsplash.com/photo-1525182008055-f88b95ff7980?auto=format&fit=crop&w=800&q=60" alt="Hero">
  <div class="hero-content">
    <h1 style="font-size:20px;">Live Connect 🚀</h1>
    <p style="font-size:13px;">Chat with friends & groups in real-time. Secure & mobile-friendly.</p>
  </div>
</div>

<div class="card">
<h4 style="text-align:center;">Features ✨</h4>
<div style="display:flex;flex-wrap:wrap;gap:6px;justify-content:center;">
  <div class="card" style="flex:1 1 90px;text-align:center;"><i class="fa fa-comment fa-2x" style="color:#25D366;"></i><p style="font-size:11px;">Chat</p></div>
  <div class="card" style="flex:1 1 90px;text-align:center;"><i class="fa fa-users fa-2x" style="color:#10b981;"></i><p style="font-size:11px;">Online Users</p></div>
  <div class="card" style="flex:1 1 90px;text-align:center;"><i class="fa fa-user fa-2x" style="color:#3b82f6;"></i><p style="font-size:11px;">Profile & Status</p></div>
  <div class="card" style="flex:1 1 90px;text-align:center;"><i class="fa fa-moon fa-2x" style="color:#facc15;"></i><p style="font-size:11px;">Dark Mode</p></div>
</div>
</div>

<div class="card">
<h4 style="text-align:center;">🟢 Online Users</h4>
<div id="currentUsers"></div>
</div>
</div>

<!-- Chat Page -->
<div id="chat" class="page">
<div class="card">
<h4>Chat</h4>
<div id="users"></div>
<div id="msgs" style="height:180px;overflow-y:auto;border:1px solid #ccc;padding:4px;border-radius:6px;margin-bottom:6px;"></div>
<input type="text" id="msg" placeholder="Type message...">
<input type="file" id="img" accept="image/*" hidden>
<button onclick="sendMsg()">➤</button>
<button onclick="img.click()">🖼️</button>
<button onclick="clearChat()">Clear</button>
</div>
</div>

<!-- Groups Page -->
<div id="groups" class="page">
<div class="card">
<h4>Groups</h4>
<button onclick="createGroup()">+ Create Group</button>
<div id="groupList"></div>
</div>
</div>

<!-- Profile Page -->
<div id="profile" class="page">
<div class="card">
<h4>Profile</h4>
<img src="https://via.placeholder.com/50" id="avatar" style="border-radius:50%;width:50px;height:50px;margin-bottom:4px;">
<input type="file" id="avatarInput" accept="image/*">
<button onclick="uploadAvatar()">Upload</button>
<textarea id="status" placeholder="Status..."></textarea>
<button onclick="updateStatus()">Update</button>
</div>
</div>

<!-- About Page -->
<div id="about" class="page">
<div class="card">
<h4>About Live Connect 🚀</h4>
<p>Live Connect is simple, modern chat for friends & groups. Send messages, images, set status, stay online!</p>
<div class="social-icons">
<a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank"><i class="fab fa-facebook"></i></a>
<a href="https://www.instagram.com/mr_nazim073" target="_blank"><i class="fab fa-instagram"></i></a>
<a href="https://youtube.com/@crazykhantv" target="_blank"><i class="fab fa-youtube"></i></a>
</div>
</div>
</div>

<!-- Contact Page -->
<div id="contact" class="page">
<div class="card">
<h4>Contact Us</h4>
<p>Email: <a href="mailto:webhub262@gmail.com">webhub262@gmail.com</a></p>
<p>Reach out anytime for support!</p>
</div>
</div>

<!-- Bottom Navigation -->
<div class="bottom-nav">
<i class="fa fa-house active-nav" onclick="showPage('home');setActiveNav(this)"></i>
<i class="fa fa-comment" onclick="showPage('chat');setActiveNav(this)"></i>
<i class="fa fa-users" onclick="showPage('groups');setActiveNav(this)"></i>
<i class="fa fa-user" onclick="showPage('profile');setActiveNav(this)"></i>
<i class="fa fa-info-circle" onclick="showPage('about');setActiveNav(this)"></i>
<i class="fa fa-envelope" onclick="showPage('contact');setActiveNav(this)"></i>
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
const currentUsersDiv=document.getElementById("currentUsers");
const msgsDiv=document.getElementById("msgs");
const usersDiv=document.getElementById("users");
const groupListDiv=document.getElementById("groupList");
const avatarImg=document.getElementById("avatar");
const avatarInput=document.getElementById("avatarInput");
const statusInput=document.getElementById("status");

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username"); return;}
  currentUser=uname;
  set(ref(db,"users/"+uname),{name:uname,online:true,avatar:"https://via.placeholder.com/50"});
  showPage('home');
};

window.showPage=(p)=>{
  document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active'));
  document.getElementById(p).classList.add('active');
};

window.setActiveNav=(el)=>{
  document.querySelectorAll('.bottom-nav i').forEach(i=>i.classList.remove('active-nav'));
  el.classList.add('active-nav');
};

// Online users
onValue(ref(db,"users"), snap=>{
  if(currentUsersDiv) currentUsersDiv.innerHTML="";
  if(usersDiv) usersDiv.innerHTML="";
  snap.forEach(u=>{
    if(u.val().online && currentUsersDiv){
      const d=document.createElement("div");
      d.className="user online";
      d.textContent=u.val().name + (u.val().status?" — "+u.val().status:"");
      currentUsersDiv.appendChild(d);
    }
    if(usersDiv && u.key!==currentUser && u.val().online){
      const d=document.createElement("div");
      d.className="user online";
      d.textContent=u.val().name;
      d.onclick=()=>openChat(u.key,false);
      usersDiv.appendChild(d);
    }
  });
});

// Groups
onValue(ref(db,"groups"), snap=>{
  if(groupListDiv) groupListDiv.innerHTML="";
  snap.forEach(g=>{
    const d=document.createElement("div");
    d.className="user online";
    d.textContent=g.key;
    d.onclick=()=>openChat(g.key,true);
    groupListDiv.appendChild(d);
  });
});

// Chat
window.openChat=(u,g)=>{
  curChat=u; isGroup=g;
  const path=g?"groupChats/"+u:"chats/"+[currentUser,u].sort().join("_");
  onValue(ref(db,path), snap=>{
    msgsDiv.innerHTML="";
    snap.forEach(m=>{
      const div=document.createElement("div");
      div.className="msg "+(m.val().from===currentUser?"me":"other");
      div.innerHTML=m.val().img?`<img src=\"${m.val().img}\">`:m.val().text;
      const btn=document.createElement("button");
      btn.textContent="🗑️";
      btn.onclick=()=>remove(ref(db,path+"/"+m.key));
      div.appendChild(btn);
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

window.uploadAvatar=async()=>{
  const f=avatarInput.files[0]; if(!f) return alert("Select image");
  const r=sRef(st,"avatars/"+currentUser+"_"+Date.now()+f.name);
  await uploadBytes(r,f); const u=await getDownloadURL(r);
  set(ref(db,"users/"+currentUser+"/avatar"),u); avatarImg.src=u; alert("Avatar updated!");
};

window.updateStatus=()=>{set(ref(db,"users/"+currentUser+"/status"),statusInput.value.trim()); alert("Status updated!");};

window.clearChat=()=>{if(!curChat) return; remove(ref(db,isGroup?"groupChats/"+curChat:"chats/"+[currentUser,curChat].sort().join("_")));};

</script>

</body>
</html>
