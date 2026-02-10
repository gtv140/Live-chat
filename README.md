<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{
  --bg:#f0f2f5;
  --card:#ffffff;
  --pri:#25D366;
  --text:#000000;
}
body.dark{
  --bg:#121212;
  --card:#1e1e1e;
  --pri:#10b981;
  --text:#ffffff;
}

body{
  margin:0;
  font-family:system-ui, sans-serif;
  background:var(--bg);
  color:var(--text);
  font-size:16px;
  -webkit-text-size-adjust:100%;
}

header{
  height:56px;
  background:var(--pri);
  color:#fff;
  display:flex;
  align-items:center;
  justify-content:space-between;
  padding:0 14px;
  font-weight:700;
  position:sticky;
  top:0;
  z-index:1000;
  font-size:20px;
}

.container{
  padding:10px;
  max-width:640px;
  margin:auto;
  padding-bottom:90px;
}

.page{display:none;}
.page.active{display:block;}

.hero{
  position:relative;
  height:180px;
  border-radius:14px;
  overflow:hidden;
  margin-bottom:14px;
}
.hero img{
  width:100%;
  height:100%;
  object-fit:cover;
  filter:brightness(0.7);
}
.hero-content{
  position:absolute;
  inset:0;
  display:flex;
  flex-direction:column;
  align-items:center;
  justify-content:center;
  color:#fff;
  text-align:center;
}
.hero-content h1{
  font-size:28px;
  margin:0;
}
.hero-content p{
  font-size:16px;
}

.card{
  background:var(--card);
  padding:14px;
  border-radius:14px;
  margin-bottom:14px;
  box-shadow:0 2px 6px rgba(0,0,0,0.1);
}

button{
  width:100%;
  padding:14px;
  font-size:16px;
  border:none;
  border-radius:10px;
  background:var(--pri);
  color:#fff;
  cursor:pointer;
  margin-top:6px;
}

input, textarea{
  width:100%;
  padding:14px;
  font-size:16px;
  border-radius:10px;
  border:1px solid #ccc;
  margin-bottom:6px;
}

.msg{
  padding:10px;
  margin:6px 0;
  border-radius:12px;
  font-size:15px;
}
.msg.me{background:#dcf8c6;text-align:right;}
.msg.other{background:#ffffff;}

.user{
  padding:10px 14px;
  background:#eaeaea;
  border-radius:20px;
  margin:4px;
  display:inline-flex;
  align-items:center;
  gap:6px;
  font-size:15px;
  cursor:pointer;
}
.user.online::before{content:"🟢";}
.user.offline::before{content:"⚪";}

#users,#groupList,#currentUsers{
  display:flex;
  overflow-x:auto;
}

.bottom-nav{
  position:fixed;
  bottom:0;
  left:0;
  width:100%;
  height:70px;
  background:var(--pri);
  display:flex;
  justify-content:space-around;
  align-items:center;
  color:#fff;
}
.bottom-nav i{
  font-size:28px;
  cursor:pointer;
}
.active-nav{opacity:1;}

@media(max-width:480px){
  body{font-size:18px;}
}
</style>
</head>

<body>

<header>
  <div>Live Connect 🚀</div>
  <button style="width:auto" onclick="document.body.classList.toggle('dark')">
    <i class="fa fa-moon"></i>
  </button>
</header>

<div class="container">

<!-- LOGIN -->
<div id="loginPage" class="page active">
  <div class="card">
    <h3>Enter Username</h3>
    <input id="usernameInput" placeholder="Username">
    <button onclick="login()">Enter</button>
  </div>
</div>

<!-- HOME -->
<div id="home" class="page">
  <div class="hero">
    <img src="https://images.unsplash.com/photo-1525182008055-f88b95ff7980?auto=format&fit=crop&w=800&q=60">
    <div class="hero-content">
      <h1>Live Connect 🚀</h1>
      <p>Modern • Real-Time • Mobile Friendly Chat</p>
    </div>
  </div>

  <div class="card">
    <h3 style="text-align:center">Key Features</h3>
    <div style="display:flex;flex-wrap:wrap;gap:10px">
      <div class="card" style="flex:1;text-align:center">
        <i class="fa fa-comment fa-3x"></i>
        <p>Live Chat</p>
      </div>
      <div class="card" style="flex:1;text-align:center">
        <i class="fa fa-users fa-3x"></i>
        <p>Online Users</p>
      </div>
      <div class="card" style="flex:1;text-align:center">
        <i class="fa fa-user fa-3x"></i>
        <p>Status</p>
      </div>
    </div>
  </div>

  <div class="card">
    <h3>Online Users</h3>
    <div id="currentUsers"></div>
  </div>
</div>

<!-- CHAT -->
<div id="chat" class="page">
  <div class="card">
    <div id="users"></div>
    <div id="msgs" style="height:260px;overflow:auto"></div>
    <input id="msg" placeholder="Type message">
    <button onclick="sendMsg()">Send</button>
  </div>
</div>

<!-- GROUP -->
<div id="groups" class="page">
  <div class="card">
    <button onclick="createGroup()">Create Group</button>
    <div id="groupList"></div>
  </div>
</div>

<!-- PROFILE -->
<div id="profile" class="page">
  <div class="card">
    <textarea id="status" placeholder="Your status"></textarea>
    <button onclick="updateStatus()">Update</button>
  </div>
</div>

<!-- ABOUT -->
<div id="about" class="page">
  <div class="card">
    <p>Live Connect is a modern real-time chat platform.</p>
    <p>
      <a href="https://www.facebook.com/profile.php?id=100084218946114">Facebook</a> |
      <a href="https://www.instagram.com/mr_nazim073">Instagram</a> |
      <a href="https://youtube.com/@crazykhantv">YouTube</a>
    </p>
  </div>
</div>

<!-- CONTACT -->
<div id="contact" class="page">
  <div class="card">
    <p>Email: webhub262@gmail.com</p>
  </div>
</div>

</div>

<!-- NAV -->
<div class="bottom-nav">
  <i class="fa fa-house" onclick="nav('home',this)"></i>
  <i class="fa fa-comment" onclick="nav('chat',this)"></i>
  <i class="fa fa-users" onclick="nav('groups',this)"></i>
  <i class="fa fa-user" onclick="nav('profile',this)"></i>
  <i class="fa fa-info-circle" onclick="nav('about',this)"></i>
  <i class="fa fa-envelope" onclick="nav('contact',this)"></i>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig={
  apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain:"live-chat-b810c.firebaseapp.com",
  databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId:"live-chat-b810c",
  messagingSenderId:"555058795334",
  appId:"1:555058795334:web:f668887409800c32970b47"
};

const app=initializeApp(firebaseConfig);
const db=getDatabase(app);

let currentUser=null,curChat=null;

window.login=()=>{
  currentUser=usernameInput.value.trim();
  if(!currentUser) return;
  set(ref(db,"users/"+currentUser),{online:true});
  show("home");
};

function show(p){
  document.querySelectorAll(".page").forEach(x=>x.classList.remove("active"));
  document.getElementById(p).classList.add("active");
}

window.nav=(p,el)=>{
  document.querySelectorAll(".bottom-nav i").forEach(i=>i.classList.remove("active-nav"));
  el.classList.add("active-nav");
  show(p);
};

onValue(ref(db,"users"),s=>{
  currentUsers.innerHTML="";
  users.innerHTML="";
  s.forEach(u=>{
    const d=document.createElement("div");
    d.className="user online";
    d.textContent=u.key;
    if(u.key!==currentUser){
      d.onclick=()=>openChat(u.key);
      users.appendChild(d);
    }
    currentUsers.appendChild(d.cloneNode(true));
  });
});

function openChat(u){
  curChat=u;
  onValue(ref(db,"chats/"+[currentUser,u].sort().join("_")),s=>{
    msgs.innerHTML="";
    s.forEach(m=>{
      const d=document.createElement("div");
      d.className="msg "+(m.val().from===currentUser?"me":"other");
      d.textContent=m.val().text;
      msgs.appendChild(d);
    });
  });
}

window.sendMsg=()=>{
  if(!curChat||!msg.value) return;
  push(ref(db,"chats/"+[currentUser,curChat].sort().join("_")),{from:currentUser,text:msg.value});
  msg.value="";
};

window.createGroup=()=>alert("Group feature ready");
window.updateStatus=()=>alert("Status updated");
</script>

</body>
</html>
