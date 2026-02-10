<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{--bg:#f3f4f6;--card:#fff;--pri:#22c55e;--txt:#111}
body.dark{--bg:#0f172a;--card:#020617;--pri:#10b981;--txt:#e5e7eb}
*{box-sizing:border-box}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--txt);font-size:16px}
header{position:sticky;top:0;height:56px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 14px;font-weight:700}
.container{padding:10px;max-width:720px;margin:auto;padding-bottom:90px}
.page{display:none}
.page.active{display:block}
.card{background:var(--card);padding:14px;border-radius:14px;margin-bottom:12px;box-shadow:0 6px 18px rgba(0,0,0,.08)}
.hero{height:190px;border-radius:16px;overflow:hidden;position:relative}
.hero img{width:100%;height:100%;object-fit:cover;filter:brightness(.7)}
.hero .t{position:absolute;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;color:#fff;text-align:center}
.hero h1{font-size:30px;margin:0}
.hero p{font-size:16px;margin-top:6px}
input,textarea,button{width:100%;padding:12px;border-radius:10px;border:1px solid #cbd5e1;font-size:16px}
button{background:var(--pri);border:none;color:#fff;margin-top:6px}
.user{padding:10px 14px;border-radius:999px;background:#e5e7eb;margin:6px;display:inline-flex;gap:8px;align-items:center;cursor:pointer}
.user::before{content:"🟢"}
.list{display:flex;overflow-x:auto}
.msg{padding:10px;border-radius:14px;margin:6px 0;max-width:80%}
.me{background:#dcfce7;margin-left:auto}
.other{background:#fff}
.bottom{position:fixed;left:0;right:0;bottom:0;height:72px;background:var(--pri);display:flex;justify-content:space-around;align-items:center;color:#fff;border-top-left-radius:18px;border-top-right-radius:18px}
.bottom i{font-size:28px}
.active-nav{opacity:1}
.social i{font-size:26px;margin:8px}
@media(max-width:480px){
  body{font-size:18px}
  .hero h1{font-size:28px}
  .bottom i{font-size:30px}
}
</style>
</head>
<body>

<header>
  <div>Live Connect 🚀</div>
  <i class="fa fa-moon" onclick="document.body.classList.toggle('dark')"></i>
</header>

<div class="container">

<!-- LOGIN -->
<div id="login" class="page active card">
  <h3>Enter Username</h3>
  <input id="uname" placeholder="Username">
  <button onclick="login()">Enter</button>
</div>

<!-- HOME -->
<div id="home" class="page">
  <div class="hero">
    <img src="https://images.unsplash.com/photo-1525182008055-f88b95ff7980?auto=format&fit=crop&w=900&q=60">
    <div class="t">
      <h1>Live Connect</h1>
      <p>Simple • Fast • Mobile-Friendly</p>
    </div>
  </div>

  <div class="card">
    <h3>Online Users</h3>
    <div id="online" class="list"></div>
  </div>

  <div class="card">
    <h3>Status Feed</h3>
    <div id="feed"></div>
  </div>
</div>

<!-- CHAT -->
<div id="chat" class="page card">
  <h3>Chat</h3>
  <div id="users" class="list"></div>
  <div id="msgs" style="height:260px;overflow:auto"></div>
  <input id="msg" placeholder="Message">
  <button onclick="send()">Send</button>
</div>

<!-- GROUPS -->
<div id="groups" class="page card">
  <h3>Groups</h3>
  <button onclick="createGroup()">+ Create Group</button>
  <div id="groupList" class="list"></div>
</div>

<!-- PROFILE -->
<div id="profile" class="page card">
  <h3>Profile</h3>
  <textarea id="status" placeholder="Your status"></textarea>
  <button onclick="saveStatus()">Post Status</button>
</div>

<!-- ABOUT -->
<div id="about" class="page card">
  <h3>About</h3>
  <p>Modern real-time chat platform with clean UX.</p>
  <div class="social">
    <a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank"><i class="fab fa-facebook"></i></a>
    <a href="https://www.instagram.com/mr_nazim073" target="_blank"><i class="fab fa-instagram"></i></a>
    <a href="https://youtube.com/@crazykhantv" target="_blank"><i class="fab fa-youtube"></i></a>
  </div>
</div>

<!-- CONTACT -->
<div id="contact" class="page card">
  <h3>Contact Us</h3>
  <p>Email: <b>webhub262@gmail.com</b></p>
</div>

</div>

<!-- BOTTOM NAV -->
<div class="bottom">
  <i class="fa fa-house" onclick="go('home',this)"></i>
  <i class="fa fa-comment" onclick="go('chat',this)"></i>
  <i class="fa fa-users" onclick="go('groups',this)"></i>
  <i class="fa fa-user" onclick="go('profile',this)"></i>
  <i class="fa fa-circle-info" onclick="go('about',this)"></i>
  <i class="fa fa-envelope" onclick="go('contact',this)"></i>
</div>

<script type="module">
import{initializeApp}from"https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import{getDatabase,ref,set,push,onValue,remove}from"https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const app=initializeApp({
 apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
 authDomain:"live-chat-b810c.firebaseapp.com",
 databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
 projectId:"live-chat-b810c",
 messagingSenderId:"555058795334",
 appId:"1:555058795334:web:f668887409800c32970b47"
});
const db=getDatabase(app);
let me="",chatWith="",isGroup=false;

window.login=()=>{
 me=uname.value.trim(); if(!me)return;
 set(ref(db,"users/"+me),{online:true});
 go('home');
};

window.go=(p,el)=>{
 document.querySelectorAll('.page').forEach(x=>x.classList.remove('active'));
 document.getElementById(p).classList.add('active');
 document.querySelectorAll('.bottom i').forEach(i=>i.classList.remove('active-nav'));
 if(el)el.classList.add('active-nav');
};

onValue(ref(db,"users"),s=>{
 online.innerHTML=""; users.innerHTML="";
 s.forEach(u=>{
  if(!u.val().online) return;
  const d=document.createElement("div"); d.className="user"; d.textContent=u.key;
  d.onclick=()=>openChat(u.key,false);
  online.appendChild(d.cloneNode(true));
  if(u.key!==me) users.appendChild(d);
 });
});

function openChat(u,g){
 chatWith=u; isGroup=g;
 onValue(ref(db,"chats/"+[me,u].sort().join("_")),s=>{
  msgs.innerHTML="";
  s.forEach(m=>{
   const d=document.createElement("div");
   d.className="msg "+(m.val().from===me?"me":"other");
   d.textContent=m.val().text;
   msgs.appendChild(d);
  });
 });
}

window.send=()=>{
 if(!chatWith) return;
 push(ref(db,"chats/"+[me,chatWith].sort().join("_")),{from:me,text:msg.value});
 msg.value="";
};

window.saveStatus=()=>{
 push(ref(db,"status"),{by:me,text:status.value,likes:0});
 status.value="";
};

onValue(ref(db,"status"),s=>{
 feed.innerHTML="";
 s.forEach(p=>{
  const c=document.createElement("div");
  c.className="card";
  c.innerHTML=`<b>${p.val().by}</b><p>${p.val().text}</p>
  <button onclick="like('${p.key}',${p.val().likes||0})">👍 ${p.val().likes||0}</button>`;
  feed.appendChild(c);
 });
});

window.like=(k,l)=>set(ref(db,"status/"+k+"/likes"),l+1);

window.addEventListener("beforeunload",()=>{
 if(me) set(ref(db,"users/"+me+"/online"),false);
});
</script>
</body>
</html>
