<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Live Connect Pro</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{
 --pri:#25D366;
 --bg:#eef2f5;
 --card:rgba(255,255,255,.75);
 --me:#dcf8c6;
 --other:#fff;
 --blur:blur(14px);
}
body.dark{
 --bg:#0b141a;
 --card:rgba(17,27,33,.85);
 --me:#005c4b;
 --other:#1f2c34;
}
*{box-sizing:border-box}
body{margin:0;font-family:system-ui;background:var(--bg);transition:.3s}

header{
 height:56px;
 background:linear-gradient(135deg,#1ebea5,#25D366);
 color:#fff;
 display:flex;align-items:center;justify-content:center;
 font-weight:700;letter-spacing:.5px;
}

.hero{
 height:180px;
 background-size:cover;
 display:flex;align-items:center;justify-content:center;
 color:#fff;font-size:22px;
 text-shadow:0 4px 12px #000;
}

/* LOGIN */
.login{
 position:fixed;inset:0;
 display:flex;align-items:center;justify-content:center;
 background:linear-gradient(135deg,#25D366,#1ebea5);
 z-index:9;
}
.login-card{
 width:90%;max-width:360px;
 background:var(--card);
 backdrop-filter:var(--blur);
 border-radius:18px;
 padding:22px;
 box-shadow:0 20px 40px rgba(0,0,0,.2);
 animation:pop .4s ease;
}
@keyframes pop{
 from{transform:scale(.9);opacity:0}
 to{transform:scale(1);opacity:1}
}
.login-card h2{text-align:center;margin:0 0 6px}
.login-card p{text-align:center;font-size:13px;opacity:.7}
.login-card input{width:100%;padding:14px;border-radius:14px;border:none;margin:14px 0;font-size:15px}
.login-card button{width:100%;padding:14px;border-radius:14px;border:none;background:linear-gradient(135deg,#25D366,#1ebea5);color:#fff;font-size:15px;font-weight:600;cursor:pointer}

/* APP */
.app{display:none;flex-direction:column;height:calc(100vh - 236px)}
.users{
 display:flex;gap:8px;padding:8px;
 overflow-x:auto;background:var(--card);
 backdrop-filter:var(--blur);
}
.user{
 padding:6px 14px;border-radius:999px;
 background:#00000015;font-size:14px;
 white-space:nowrap;
 cursor:pointer;
}
.user.online::before{content:"● ";color:#25D366}
.user.offline::before{content:"● ";color:#888}

/* CHAT */
.chat{flex:1;display:flex;flex-direction:column;min-height:0}
.chat-head{
 padding:8px;background:var(--card);
 backdrop-filter:var(--blur);
 display:flex;justify-content:space-between;
 font-size:13px;
}
.messages{flex:1;overflow-y:auto;padding:10px}
.msg{
 max-width:75%;margin-bottom:8px;
 padding:10px 12px;border-radius:14px;
 font-size:14px;animation:fade .2s;
 position:relative;
}
@keyframes fade{
 from{opacity:0;transform:translateY(6px)}
 to{opacity:1}
}
.me{background:var(--me);margin-left:auto}
.other{background:var(--other)}
.msg img{max-width:180px;border-radius:10px}
.msg .del{position:absolute;top:4px;right:4px;background:#f44336;color:#fff;border:none;border-radius:50%;width:22px;height:22px;font-size:12px;cursor:pointer}

/* INPUT */
.input{
 display:flex;gap:6px;padding:8px;
 background:var(--card);backdrop-filter:var(--blur)
}
.input input{
 flex:1;padding:12px;border-radius:999px;
 border:1px solid #ccc;
}
.input button{
 width:40px;height:40px;border-radius:50%;
 border:none;background:var(--pri);color:#fff;
 cursor:pointer;
}
</style>
</head>

<body>

<header>Live Connect Pro</header>
<div class="hero" id="hero">Chat. Connect. Instantly.</div>

<!-- LOGIN -->
<div class="login" id="login">
 <div class="login-card">
  <h2>Welcome 👋</h2>
  <p>Enter your full name to continue</p>
  <input id="name" placeholder="Full Name">
  <button onclick="start()">Continue</button>
 </div>
</div>

<div class="app" id="app">
 <div class="users" id="users"></div>

 <div class="chat">
  <div class="chat-head">
   <span id="chatWith">Select user</span>
   <i class="fa fa-moon" onclick="document.body.classList.toggle('dark')"></i>
  </div>

  <div class="messages" id="msgs"></div>

  <div class="input">
   <input id="msg" placeholder="Type a message">
   <input type="file" id="img" accept="image/*" hidden>
   <button onclick="send()"><i class="fa fa-paper-plane"></i></button>
   <button onclick="img.click()"><i class="fa fa-image"></i></button>
  </div>
 </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";
import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-storage.js";

// Firebase config
const firebaseConfig={
 apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
 databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
 projectId:"live-chat-b810c",
 storageBucket:"live-chat-b810c.appspot.com"
};

const fb = initializeApp(firebaseConfig);
const db = getDatabase(fb);
const st = getStorage(fb);

let me="", cur="";

// START
window.start=()=>{
 me = name.value.trim();
 if(!me) return;
 login.style.display="none";
 app.style.display="flex";
 set(ref(db,"users/"+me),{online:true});
 onDisconnect(ref(db,"users/"+me)).set({online:false});
 loadUsers();
};

// LOAD USERS
function loadUsers(){
 onValue(ref(db,"users"), s=>{
  users.innerHTML="";
  s.forEach(u=>{
   if(u.key!==me){
    let d=document.createElement("div");
    d.className="user "+(u.val().online?"online":"offline");
    d.textContent=u.key;
    d.onclick=()=>openChat(u.key);
    users.appendChild(d);
   }
  });
 });
}

// OPEN CHAT
window.openChat=(u)=>{
 cur = u; chatWith.textContent = u;
 const path = "chats/"+[me,u].sort().join("_");
 onValue(ref(db,path), s=>{
  msgs.innerHTML="";
  s.forEach(m=>{
   let d = document.createElement("div");
   d.className="msg "+(m.val().from===me?"me":"other");
   d.innerHTML = m.val().img ? `<img src=\"${m.val().img}\">` : m.val().text;
   if(m.val().from===me){
     let b=document.createElement("button");
     b.className="del"; b.textContent="×";
     b.onclick=()=>remove(ref(db,path+"/"+m.key));
     d.appendChild(b);
   }
   msgs.appendChild(d);
  });
  msgs.scrollTop = msgs.scrollHeight;
 });
};

// SEND MESSAGE
window.send=()=>{
 if(!cur || !msg.value.trim()) return;
 push(ref(db,"chats/"+[me,cur].sort().join("_")), {from:me,text:msg.value});
 msg.value="";
};

// SEND IMAGE
img.onchange=async()=>{
 let f = img.files[0];
 let r = sRef(st,"imgs/"+Date.now()+f.name);
 await uploadBytes(r,f);
 let u = await getDownloadURL(r);
 push(ref(db,"chats/"+[me,cur].sort().join("_")), {from:me,img:u});
};

// HERO SLIDES
const slides=[
 "https://images.unsplash.com/photo-1525182008055-f88b95ff7980",
 "https://images.unsplash.com/photo-1515378791036-0648a3ef77b2",
 "https://images.unsplash.com/photo-1492724441997-5dc865305da7"
];
let i=0;
setInterval(()=>hero.style.backgroundImage=`url(${slides[i++%slides.length]})`,3000);

</script>
</body>
</html>
