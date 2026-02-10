<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect</title>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{
  --bg:#f4f6f8;
  --card:#ffffff;
  --pri:#22c55e;
  --text:#111;
}
body.dark{
  --bg:#0f172a;
  --card:#1e293b;
  --pri:#10b981;
  --text:#f9fafb;
}

body{
  margin:0;
  font-family:system-ui,sans-serif;
  background:var(--bg);
  color:var(--text);
}

header{
  height:56px;
  background:var(--pri);
  color:#fff;
  display:flex;
  align-items:center;
  justify-content:space-between;
  padding:0 16px;
  font-size:20px;
  font-weight:700;
  position:sticky;
  top:0;
}

.container{
  max-width:640px;
  margin:auto;
  padding:12px;
  padding-bottom:90px;
}

.page{display:none;}
.page.active{display:block;}

.card{
  background:var(--card);
  border-radius:16px;
  padding:16px;
  margin-bottom:14px;
  box-shadow:0 4px 14px rgba(0,0,0,.08);
}

.hero{
  height:180px;
  border-radius:18px;
  background:url("https://images.unsplash.com/photo-1525182008055-f88b95ff7980?auto=format&fit=crop&w=900&q=60") center/cover;
  display:flex;
  align-items:center;
  justify-content:center;
  color:#fff;
  text-align:center;
}
.hero h1{font-size:30px;margin:0;}
.hero p{opacity:.9;}

input,textarea{
  width:100%;
  padding:14px;
  border-radius:12px;
  border:1px solid #ccc;
  font-size:16px;
  margin-bottom:8px;
}

button{
  width:100%;
  padding:14px;
  border:none;
  border-radius:12px;
  background:var(--pri);
  color:#fff;
  font-size:16px;
  cursor:pointer;
}

.features{
  display:grid;
  grid-template-columns:repeat(2,1fr);
  gap:12px;
}
.feature{
  text-align:center;
}
.feature i{
  font-size:36px;
  color:var(--pri);
  margin-bottom:6px;
}

.user{
  padding:10px 14px;
  background:#e5e7eb;
  border-radius:20px;
  margin:4px;
  display:inline-flex;
  align-items:center;
  gap:6px;
  font-size:15px;
}
.user::before{content:"🟢";}

#users,#currentUsers{
  display:flex;
  overflow-x:auto;
}

.msg{
  padding:10px;
  margin:6px 0;
  border-radius:14px;
}
.me{background:#dcf8c6;text-align:right;}
.other{background:#fff;}

.socials{
  display:flex;
  justify-content:center;
  gap:24px;
  margin-top:10px;
}
.socials i{
  font-size:34px;
  color:var(--pri);
}

.bottom-nav{
  position:fixed;
  bottom:0;
  width:100%;
  height:70px;
  background:var(--pri);
  display:flex;
  justify-content:space-around;
  align-items:center;
  color:#fff;
}
.bottom-nav i{font-size:28px;}
</style>
</head>

<body>

<header>
  <span>Live Connect</span>
  <i class="fa fa-moon" onclick="document.body.classList.toggle('dark')"></i>
</header>

<div class="container">

<!-- LOGIN -->
<div id="login" class="page active">
  <div class="card">
    <h3>Enter Username</h3>
    <input id="username">
    <button onclick="login()">Continue</button>
  </div>
</div>

<!-- HOME -->
<div id="home" class="page">
  <div class="hero">
    <div>
      <h1>Live Connect</h1>
      <p>Fast • Secure • Mobile Chat</p>
    </div>
  </div>

  <div class="card">
    <div class="features">
      <div class="feature"><i class="fa fa-comment"></i><p>Live Chat</p></div>
      <div class="feature"><i class="fa fa-users"></i><p>Online Users</p></div>
      <div class="feature"><i class="fa fa-user"></i><p>Status</p></div>
      <div class="feature"><i class="fa fa-moon"></i><p>Dark Mode</p></div>
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
    <input id="msg" placeholder="Message">
    <button onclick="sendMsg()">Send</button>
  </div>
</div>

<!-- ABOUT -->
<div id="about" class="page">
  <div class="card">
    <p>Modern real-time chat platform made for mobile users.</p>
    <div class="socials">
      <a href="https://www.facebook.com/profile.php?id=100084218946114"><i class="fab fa-facebook"></i></a>
      <a href="https://www.instagram.com/mr_nazim073"><i class="fab fa-instagram"></i></a>
      <a href="https://youtube.com/@crazykhantv"><i class="fab fa-youtube"></i></a>
    </div>
  </div>
</div>

<!-- CONTACT -->
<div id="contact" class="page">
  <div class="card">
    <p><i class="fa fa-envelope"></i> webhub262@gmail.com</p>
  </div>
</div>

</div>

<!-- NAV -->
<div class="bottom-nav">
  <i class="fa fa-house" onclick="nav('home')"></i>
  <i class="fa fa-comment" onclick="nav('chat')"></i>
  <i class="fa fa-info-circle" onclick="nav('about')"></i>
  <i class="fa fa-envelope" onclick="nav('contact')"></i>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const app=initializeApp({
  apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com"
});
const db=getDatabase(app);

let user=null,chat=null;

window.login=()=>{
  user=username.value.trim();
  if(!user) return;
  set(ref(db,"users/"+user),{online:true});
  show("home");
};

function show(p){
  document.querySelectorAll(".page").forEach(x=>x.classList.remove("active"));
  document.getElementById(p).classList.add("active");
}
window.nav=show;

onValue(ref(db,"users"),s=>{
  users.innerHTML=currentUsers.innerHTML="";
  s.forEach(u=>{
    const d=document.createElement("div");
    d.className="user";
    d.textContent=u.key;
    if(u.key!==user){
      d.onclick=()=>openChat(u.key);
      users.appendChild(d);
    }
    currentUsers.appendChild(d.cloneNode(true));
  });
});

function openChat(u){
  chat=u;
  onValue(ref(db,"chats/"+[user,u].sort().join("_")),s=>{
    msgs.innerHTML="";
    s.forEach(m=>{
      const d=document.createElement("div");
      d.className="msg "+(m.val().from===user?"me":"other");
      d.textContent=m.val().text;
      msgs.appendChild(d);
    });
  });
}

window.sendMsg=()=>{
  if(!chat||!msg.value) return;
  push(ref(db,"chats/"+[user,chat].sort().join("_")),{from:user,text:msg.value});
  msg.value="";
};
</script>

</body>
</html>
