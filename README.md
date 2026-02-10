<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect Pro</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
 --pri:#25D366;
 --bg:#f0f2f5;
 --card:rgba(255,255,255,0.95);
 --me:#dcf8c6;
 --other:#fff;
 --text:#111;
 --blur:blur(12px);
}
body.dark{
 --bg:#121212;
 --card:rgba(20,20,20,0.95);
 --me:#1f4b2f;
 --other:#222;
 --text:#fff;
}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text);transition:.3s}
header{
 height:60px;
 background:linear-gradient(135deg,#1ebea5,#25D366);
 color:#fff;
 display:flex;
 align-items:center;
 justify-content:center;
 font-weight:800;
 font-size:22px;
 letter-spacing:.7px;
 text-shadow:0 2px 4px rgba(0,0,0,.3);
 position:relative;
}
header::after{
 content:"";
 position:absolute;bottom:0;left:0;width:100%;height:4px;
 background:rgba(255,255,255,0.3);
 box-shadow:0 2px 6px rgba(0,0,0,0.2);
}
.hero{
 height:200px;
 width:100%;
 display:flex;
 align-items:center;
 justify-content:center;
 font-size:22px;
 font-weight:600;
 color:#fff;
 text-shadow:0 4px 12px rgba(0,0,0,0.5);
 background-size:cover;
 background-position:center;
 transition:.5s;
}

/* LOGIN */
.login{
 position:fixed;inset:0;
 display:flex;align-items:center;justify-content:center;
 background:linear-gradient(135deg,#25D366,#1ebea5);
 z-index:10;
}
.login-card{
 width:90%;max-width:380px;
 background:var(--card);
 backdrop-filter:var(--blur);
 border-radius:20px;
 padding:24px;
 box-shadow:0 20px 40px rgba(0,0,0,.25);
 text-align:center;
}
.login-card h2{margin:0 0 8px;font-size:22px;}
.login-card p{font-size:13px;color:#555;margin-bottom:16px;}
.login-card input{
 width:100%;padding:14px 16px;margin:10px 0;border-radius:16px;border:none;font-size:15px;
}
.login-card button{
 width:100%;padding:14px;border-radius:16px;border:none;background:linear-gradient(135deg,#25D366,#1ebea5);
 color:#fff;font-size:16px;font-weight:600;cursor:pointer;
 transition:.3s;
}
.login-card button:hover{opacity:.9;}

/* APP */
.app{display:none;flex-direction:column;height:calc(100vh - 260px)}
.users{
 display:flex;gap:10px;padding:8px;
 overflow-x:auto;background:var(--card);
 backdrop-filter:var(--blur);
 border-radius:12px;
 margin:8px;
}
.user{
 padding:8px 16px;border-radius:999px;
 background:#00000015;font-size:14px;
 white-space:nowrap;cursor:pointer;
 position:relative;transition:.3s;
}
.user:hover{background:#00000025;}
.user.online::before{content:"● ";color:#25D366;}
.user.offline::before{content:"● ";color:#888;}
.user .unread{position:absolute;top:-4px;right:-4px;background:#f44336;color:#fff;border-radius:50%;font-size:10px;padding:2px 5px;}
.user img{width:26px;height:26px;border-radius:50%;margin-right:6px;vertical-align:middle;}

/* CHAT */
.chat{
 flex:1;display:flex;flex-direction:column;min-height:0;margin:8px;
}
.chat-head{
 padding:10px;background:var(--card);backdrop-filter:var(--blur);
 display:flex;justify-content:space-between;align-items:center;
 font-size:15px;font-weight:600;
 border-radius:12px 12px 0 0;
}
.messages{flex:1;overflow-y:auto;padding:10px;background:var(--card);border-radius:0 0 12px 12px;}
.msg{
 max-width:75%;margin-bottom:8px;padding:10px 14px;border-radius:16px;font-size:14px;word-wrap:break-word;
 animation:fadeIn .3s;position:relative;
}
@keyframes fadeIn{from{opacity:0;transform:translateY(8px)}to{opacity:1;}}
.me{background:var(--me);margin-left:auto;color:#000;}
.other{background:var(--other);color:#000;}
body.dark .me{color:#fff;}
body.dark .other{color:#fff;}
.msg img{max-width:180px;border-radius:12px;}
.msg .del{position:absolute;top:4px;right:4px;background:#f44336;color:#fff;border:none;border-radius:50%;width:22px;height:22px;font-size:12px;cursor:pointer;}
.msg .time{position:absolute;bottom:-16px;right:8px;font-size:10px;color:#555;}
body.dark .msg .time{color:#ccc;}

/* INPUT */
.input{
 display:flex;gap:6px;padding:8px;
 background:var(--card);backdrop-filter:var(--blur);
 border-radius:12px;
}
.input input{
 flex:1;padding:12px;border-radius:999px;border:1px solid #ccc;font-size:14px;
}
.input button{
 width:42px;height:42px;border-radius:50%;
 border:none;background:var(--pri);color:#fff;cursor:pointer;font-size:16px;
 transition:.3s;
}
.input button:hover{opacity:.9;}
.typing-indicator{font-size:12px;color:#888;margin:2px 0 4px 6px;}
.fab{
 position:fixed;bottom:24px;right:24px;width:56px;height:56px;
 border-radius:50%;background:var(--pri);color:#fff;
 display:flex;align-items:center;justify-content:center;
 font-size:24px;cursor:pointer;
 box-shadow:0 6px 12px rgba(0,0,0,.2);
}
.fab:hover{opacity:.9;}
.search-bar{
 width:calc(100% - 16px);margin:8px;padding:6px 12px;border-radius:999px;border:1px solid #ccc;
}
</style>
</head>
<body>

<header>Live Connect Pro</header>
<div class="hero" id="hero">Connect Instantly • Chat Anywhere</div>

<div class="login" id="login">
 <div class="login-card">
  <h2>Welcome 👋</h2>
  <p>Enter your full name to continue</p>
  <input id="name" placeholder="Full Name">
  <button onclick="start()">Continue</button>
 </div>
</div>

<div class="app" id="app">
 <input type="text" id="searchUser" class="search-bar" placeholder="Search users...">
 <div class="users" id="users"></div>

 <div class="chat">
  <div class="chat-head">
   <span id="chatWith">Select user</span>
   <i class="fa fa-moon" onclick="document.body.classList.toggle('dark')"></i>
  </div>
  <div class="typing-indicator" id="typing"></div>
  <div class="messages" id="msgs"></div>

  <div class="input">
   <input id="msg" placeholder="Type a message">
   <input type="file" id="img" accept="image/*" hidden>
   <button onclick="send()"><i class="fa fa-paper-plane"></i></button>
   <button onclick="img.click()"><i class="fa fa-image"></i></button>
  </div>
 </div>
</div>

<div class="fab" onclick="newGroup()"><i class="fa fa-users"></i></div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";
import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-storage.js";

const firebaseConfig={
 apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
 authDomain:"live-chat-b810c.firebaseapp.com",
 databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
 projectId:"live-chat-b810c",
 storageBucket:"live-chat-b810c.appspot.com",
 messagingSenderId:"555058795334",
 appId:"1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
const st = getStorage(app);

let me="", cur="";

// LOGIN
window.start=()=>{
 me=document.getElementById("name").value.trim();
 if(!me) return alert("Enter your full name!");
 const meRef=ref(db,"users/"+me);
 set(meRef,{online:true});
 onDisconnect(meRef).set({online:false});
 document.getElementById("login").style.display="none";
 document.getElementById("app").style.display="flex";
 loadUsers();
}

// LOAD USERS
function loadUsers(){
 const usersEl=document.getElementById("users");
 const searchInput=document.getElementById("searchUser");
 onValue(ref(db,"users"), snapshot=>{
  usersEl.innerHTML="";
  snapshot.forEach(u=>{
   if(u.key!==me && u.key.toLowerCase().includes(searchInput.value.toLowerCase())){
    const d=document.createElement("div");
    d.className="user "+(u.val().online?"online":"offline");
    d.textContent=u.key;
    d.onclick=()=>openChat(u.key);
    usersEl.appendChild(d);
   }
  });
 });
 searchInput.addEventListener("input",loadUsers);
}

// OPEN CHAT
window.openChat=(u)=>{
 cur=u;
 chatWith.textContent=u;
 const path="chats/"+[me,u].sort().join("_");
 onValue(ref(db,path),s=>{
  msgs.innerHTML="";
  s.forEach(m=>{
   const d=document.createElement("div");
   d.className="msg "+(m.val().from===me?"me":"other");
   d.innerHTML=m.val().img?`<img src="${m.val().img}">`:m.val().text;
   const t=document.createElement("div");
   t.className="time";
   t.textContent=new Date(m.val().time||Date.now()).toLocaleTimeString();
   d.appendChild(t);
   if(m.val().from===me){
     const b=document.createElement("button");
     b.className="del";b.textContent="×";
     b.onclick=()=>remove(ref(db,path+"/"+m.key));
     d.appendChild(b);
   }
   msgs.appendChild(d);
  });
  msgs.scrollTop=msgs.scrollHeight;
 });
};

// SEND MESSAGE
window.send=()=>{
 if(!cur || !msg.value.trim()) return;
 push(ref(db,"chats/"+[me,cur].sort().join("_")),{from:me,text:msg.value,time:Date.now()});
 msg.value="";
 playNotification();
};

// SEND IMAGE
img.onchange=async()=>{
 let f=img.files[0];
 let r=sRef(st,"imgs/"+Date.now()+f.name);
 await uploadBytes(r,f);
 let u=await getDownloadURL(r);
 push(ref(db,"chats/"+[me,cur].sort().join("_")),{from:me,img:u,time:Date.now()});
 playNotification();
};

// HERO SLIDES
const slides=[
 "https://images.unsplash.com/photo-1525182008055-f88b95ff7980",
 "https://images.unsplash.com/photo-1515378791036-0648a3ef77b2",
 "https://images.unsplash.com/photo-1492724441997-5dc865305da7"
];
let i=0;
setInterval(()=>hero.style.backgroundImage=`url(${slides[i++%slides.length]})`,3000);

// NOTIFICATION
function playNotification(){let a=new Audio("https://www.myinstants.com/media/sounds/facebook_messenger.mp3");a.play();}

// FAB NEW GROUP (placeholder)
window.newGroup=()=>alert("Group feature coming soon!");
</script>

</body>
</html>
