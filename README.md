<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect</title>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{
 --bg:#f5f7fb;
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
 z-index:1000;
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
 padding:14px;
 margin-bottom:12px;
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

.hero h1{margin:0;font-size:30px;}
.hero p{opacity:.9;}

input,textarea{
 width:100%;
 padding:14px;
 border-radius:12px;
 border:1px solid #ccc;
 font-size:15px;
 margin-bottom:8px;
}

button{
 width:100%;
 padding:14px;
 border:none;
 border-radius:12px;
 background:var(--pri);
 color:#fff;
 font-size:15px;
 cursor:pointer;
}

.user{
 display:inline-flex;
 align-items:center;
 gap:6px;
 background:#e5e7eb;
 padding:8px 14px;
 border-radius:20px;
 margin:4px;
 font-size:14px;
}
.user::before{content:"🟢";}

.msg{
 padding:10px;
 margin:6px 0;
 border-radius:14px;
 max-width:80%;
}
.me{background:#dcf8c6;margin-left:auto;}
.other{background:#fff;}

.status{
 border-bottom:1px solid #ddd;
 padding:8px 0;
}
.status small{opacity:.7}

.actions{
 display:flex;
 gap:16px;
 font-size:18px;
 margin-top:6px;
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
 z-index:1000;
}
.bottom-nav i{font-size:26px;}
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
   <p>Chat • Status • Reactions</p>
  </div>
 </div>

 <div class="card">
  <h3>Online Users</h3>
  <div id="onlineUsers"></div>
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

<!-- STATUS -->
<div id="statusPage" class="page">
 <div class="card">
  <textarea id="newStatus" placeholder="What's on your mind?"></textarea>
  <button onclick="postStatus()">Post Status</button>
 </div>

 <div class="card">
  <h3>All Status</h3>
  <div id="statusList"></div>
 </div>
</div>

<!-- PROFILE -->
<div id="profile" class="page">
 <div class="card">
  <h3>Your Status</h3>
  <textarea id="myStatus"></textarea>
  <button onclick="updateMyStatus()">Update</button>
 </div>
</div>

</div>

<!-- NAV -->
<div class="bottom-nav">
 <i class="fa fa-house" onclick="nav('home')"></i>
 <i class="fa fa-comment" onclick="nav('chat')"></i>
 <i class="fa fa-plus" onclick="nav('statusPage')"></i>
 <i class="fa fa-user" onclick="nav('profile')"></i>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const app=initializeApp({
 apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
 databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com"
});
const db=getDatabase(app);

let user=null,chatUser=null;

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

// USERS
onValue(ref(db,"users"),s=>{
 users.innerHTML=onlineUsers.innerHTML="";
 s.forEach(u=>{
  const d=document.createElement("div");
  d.className="user";
  d.textContent=u.key;
  if(u.key!==user){
   d.onclick=()=>openChat(u.key);
   users.appendChild(d);
  }
  onlineUsers.appendChild(d.cloneNode(true));
 });
});

// CHAT
function openChat(u){
 chatUser=u;
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
 if(!chatUser||!msg.value) return;
 push(ref(db,"chats/"+[user,chatUser].sort().join("_")),{from:user,text:msg.value});
 msg.value="";
};

// STATUS POST
window.postStatus=()=>{
 if(!newStatus.value) return;
 push(ref(db,"status"),{
  user,
  text:newStatus.value,
  likes:0
 });
 newStatus.value="";
};

// STATUS LIST
onValue(ref(db,"status"),s=>{
 statusList.innerHTML="";
 s.forEach(st=>{
  const d=document.createElement("div");
  d.className="status";
  d.innerHTML=`
   <b>${st.val().user}</b>
   <p>${st.val().text}</p>
   <small>Likes: ${st.val().likes||0}</small>
   <div class="actions">
    <i class="fa fa-heart" onclick="likeStatus('${st.key}',${st.val().likes||0})"></i>
   </div>
  `;
  statusList.prepend(d);
 });
});

window.likeStatus=(id,count)=>{
 update(ref(db,"status/"+id),{likes:count+1});
};

// PROFILE STATUS
window.updateMyStatus=()=>{
 update(ref(db,"users/"+user),{status:myStatus.value});
 alert("Updated");
};
</script>

</body>
</html>
