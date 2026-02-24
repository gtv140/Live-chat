<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Web-Hub Chat - Final</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
--primary:#6d28d9;--secondary:#f472b6;--bg:#0f172a;--glass:rgba(30,41,59,0.85);--text-light:#f1f5f9;--accent:#fbbf24;
}
*{box-sizing:border-box;font-family:'Plus Jakarta Sans',sans-serif;transition:0.3s;}
body{margin:0;background:var(--bg);color:var(--text-light);height:100vh;display:flex;flex-direction:column;}

/* Login */
#loginScreen{position:fixed;inset:0;background:var(--bg);display:flex;align-items:center;justify-content:center;z-index:1000;}
.login-box{background:var(--glass);padding:40px;border-radius:25px;text-align:center;width:90%;max-width:350px;}
.login-box h1{background:linear-gradient(to right,var(--primary),var(--secondary));-webkit-background-clip:text;-webkit-text-fill-color:transparent;font-size:2rem;margin-bottom:20px;}
.login-box input{width:100%;padding:15px;margin-bottom:15px;border-radius:15px;border:none;background:#000;color:#fff;text-align:center;outline:none;}
.login-box button{width:100%;padding:15px;border-radius:15px;border:none;background:var(--primary);color:white;font-weight:800;cursor:pointer;}

/* Header & Nav */
header{display:flex;justify-content:space-between;align-items:center;padding:15px 25px;background:rgba(15,23,42,0.9);backdrop-filter:blur(10px);}
.brand{font-weight:900;font-size:1.5rem;background:linear-gradient(to right,var(--primary),var(--secondary));-webkit-background-clip:text;-webkit-text-fill-color:transparent;}
.bottom-nav{position:fixed;bottom:0;left:0;right:0;height:60px;background:rgba(15,23,42,0.95);display:flex;justify-content:space-around;align-items:center;border-top:1px solid rgba(255,255,255,0.05);}
.bottom-nav i{font-size:1.5rem;color:#9ca3af;cursor:pointer;}
.bottom-nav i.active{color:var(--primary);}
.three-dot-menu{position:relative;cursor:pointer;}
.menu-dropdown{display:none;position:absolute;bottom:50px;right:0;background:var(--glass);border-radius:15px;overflow:hidden;min-width:150px;flex-direction:column;display:flex;}
.menu-dropdown div{padding:10px;cursor:pointer;border-bottom:1px solid rgba(255,255,255,0.1);}
.menu-dropdown div:last-child{border:none;}
.menu-dropdown div:hover{background:rgba(255,255,255,0.1);}

/* Pages */
.page{display:none;flex:1;overflow:auto;padding:20px;flex-direction:column;gap:15px;}
.page.active{display:flex;}
.welcome-box{background:var(--glass);padding:20px;border-radius:20px;text-align:center;font-size:1.2rem;font-weight:600;}
.quotes-box{background:rgba(30,41,59,0.6);padding:15px;border-radius:15px;font-style:italic;text-align:center;font-size:0.9rem;}
.users-list{background:var(--glass);padding:15px;border-radius:15px;display:flex;flex-direction:column;gap:10px;}
.user-card{display:flex;align-items:center;gap:10px;padding:10px;border-radius:12px;cursor:pointer;background:rgba(255,255,255,0.03);}
.user-card:hover{background:rgba(255,255,255,0.08);}
.chat-box{flex:1;display:flex;flex-direction:column;gap:10px;overflow-y:auto;padding-bottom:120px;}
.msg{max-width:80%;padding:10px 15px;border-radius:20px;font-size:14px;position:relative;word-wrap:break-word;}
.msg.me{align-self:flex-end;background:var(--primary);border-bottom-right-radius:4px;}
.msg.other{align-self:flex-start;background:var(--glass);border-bottom-left-radius:4px;}
.msg img{max-width:100%;border-radius:12px;margin-top:8px;}
.dock{position:fixed;bottom:70px;left:10px;right:10px;background:rgba(30,41,59,0.95);padding:8px;border-radius:50px;display:flex;align-items:center;gap:10px;border:1px solid rgba(255,255,255,0.1);}
.dock input{flex:1;background:none;border:none;color:white;outline:none;padding-left:10px;}
.dock label{cursor:pointer;}
.admin-tools{position:fixed;top:70px;right:10px;background:var(--glass);padding:10px;border-radius:15px;display:flex;flex-direction:column;gap:10px;}
.admin-tools button{padding:5px 10px;border:none;border-radius:10px;cursor:pointer;font-weight:700;}
.admin-tools button.broadcast{background:#facc15;color:#000;}
.admin-tools button.mute{background:#ef4444;color:#fff;}
.group-box{background:var(--glass);padding:15px;border-radius:15px;margin-top:10px;display:flex;gap:5px;}
.group-box input{padding:8px;border-radius:10px;border:none;width:70%;outline:none;}
.group-box button{padding:8px 10px;border-radius:10px;border:none;background:var(--primary);color:#fff;cursor:pointer;}
.typing-indicator{font-size:0.8rem;color:#fbbf24;opacity:0.8;margin-left:10px;}
</style>
</head>
<body>

<div id="loginScreen">
  <div class="login-box">
    <h1>WEB-HUB</h1>
    <input type="text" id="loginName" placeholder="Enter Username">
    <button onclick="boot()">Login</button>
  </div>
</div>

<header>
  <div class="brand">WEB-HUB CHAT</div>
  <div class="three-dot-menu" onclick="toggleMenu()">
    <i class="fa-solid fa-ellipsis-vertical"></i>
    <div class="menu-dropdown" id="menuDropdown">
      <div onclick="showPage('about')">About</div>
      <div onclick="showPage('contact')">Contact Us</div>
      <div onclick="logout()">Logout</div>
    </div>
  </div>
</header>

<div class="page active" id="home">
  <div class="welcome-box" id="welcomeBox">Welcome, User!</div>
  <div class="quotes-box" id="quoteBox">Loading quotes...</div>
  <div id="dateBox" style="text-align:center;margin-top:10px;font-size:0.9rem;color:#fbbf24;"></div>
</div>

<div class="page" id="chatPage">
  <div class="chat-box" id="chatFeed"></div>
  <div class="typing-indicator" id="typingIndicator"></div>
  <div class="dock">
    <label for="pUp"><i class="fa-solid fa-image"></i></label>
    <input type="file" id="pUp" hidden onchange="upImg(this)">
    <input type="text" id="chatInput" placeholder="Enter message..." onkeydown="typing();if(event.key==='Enter') sendMessage()">
    <button onclick="sendMessage()"><i class="fa-solid fa-paper-plane"></i></button>
    <label for="emojiPicker"><i class="fa-solid fa-face-smile"></i></label>
    <input type="color" id="emojiPicker" hidden onchange="insertEmoji(this.value)">
  </div>
  <div class="group-box">
    <input type="text" id="groupName" placeholder="Enter group name">
    <button onclick="createGroup()">Create Group</button>
  </div>
</div>

<div class="page" id="usersPage">
  <div class="users-list" id="usersList"></div>
</div>

<div class="page" id="about">
  <div style="padding:20px;background:var(--glass);border-radius:15px;">
    <h2>About Web-Hub</h2>
    <p>Web-Hub is an innovative real-time chat platform connecting users instantly with private messages, group chats, media, emoji, and admin controls. Fast, modern, responsive, and fully secure. Perfect for web & mobile users.</p>
  </div>
</div>

<div class="page" id="contact">
  <div style="padding:20px;background:var(--glass);border-radius:15px;">
    <h2>Contact Us</h2>
    <p>Email: <a href="mailto:webhub262@gmail.com" style="color:var(--secondary)">webhub262@gmail.com</a></p>
  </div>
</div>

<div class="bottom-nav">
  <i class="fa-solid fa-house active" onclick="showPage('home',this)"></i>
  <i class="fa-solid fa-comments" onclick="showPage('chatPage',this)"></i>
  <i class="fa-solid fa-users" onclick="showPage('usersPage',this)"></i>
</div>

<div class="admin-tools" id="adminTools" style="display:none;">
  <button class="broadcast" onclick="broadcastMessage()">Broadcast</button>
  <button class="mute" onclick="toggleMute()">Toggle Mute</button>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
import { getDatabase, ref, set, push, onValue, serverTimestamp, query, limitToLast, update } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

const firebaseConfig = {
  apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain:"live-chat-b810c.firebaseapp.com",
  databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId:"live-chat-b810c",
  storageBucket:"live-chat-b810c.firebasestorage.app",
  messagingSenderId:"555058795334",
  appId:"1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getDatabase(app);

let uUID,uName,isAdmin=false;
let currentRoom="global_v2";

const quotes=[
"Success is not final, failure is not fatal: It is the courage to continue that counts.",
"Believe you can and you're halfway there.",
"Hard work beats talent when talent doesn't work hard.",
"Stay positive, work hard, make it happen.",
"Don't wait for opportunity. Create it."
];

function updateDateQuote(){
  const d=new Date();
  document.getElementById("dateBox").innerText=d.toLocaleString();
  document.getElementById("quoteBox").innerText=quotes[Math.floor(Math.random()*quotes.length)];
}
setInterval(updateDateQuote,5000);
updateDateQuote();

function toggleMenu(){document.getElementById("menuDropdown").style.display=document.getElementById("menuDropdown").style.display==='flex'?'none':'flex';}
function showPage(id,el=null){document.querySelectorAll(".page").forEach(p=>p.classList.remove("active"));document.getElementById(id).classList.add("active");if(el){document.querySelectorAll(".bottom-nav i").forEach(i=>i.classList.remove("active"));el.classList.add("active");}}

window.boot=async()=>{
  const n=document.getElementById("loginName").value.trim();
  if(!n)return alert("Enter username!");
  if(n.toLowerCase()==='nazim'){
    const pin=prompt("Enter admin PIN:");
    if(pin!=='786')return alert("Wrong PIN!");
    isAdmin=true;
    document.getElementById("adminTools").style.display="flex";
  }
  const res=await signInAnonymously(auth);
  uUID=res.user.uid; uName=n;
  localStorage.setItem("uN",n); localStorage.setItem("uA",isAdmin);
  document.getElementById("loginScreen").style.display="none";
  document.getElementById("welcomeBox").innerText=`Welcome, ${uName}!`;
  set(ref(db,'nodes/'+uUID),{name:uName,online:true,typing:false});
  syncUsers(); syncMsgs();
}

function logout(){localStorage.clear();signOut(auth).then(()=>location.reload());}

function sendMessage(){
  const i=document.getElementById("chatInput");
  if(!i.value.trim())return;
  push(ref(db,currentRoom),{uid:uUID,sender:uName,text:i.value,ts:serverTimestamp()});
  update(ref(db,'nodes/'+uUID),{typing:false});
  i.value="";
}

function upImg(el){
  const r=new FileReader();
  r.onload=e=>sendMessageWithMedia(e.target.result);
  r.readAsDataURL(el.files[0]);
}
function sendMessageWithMedia(p){
  push(ref(db,currentRoom),{uid:uUID,sender:uName,img:p,ts:serverTimestamp()});
}

function insertEmoji(e){document.getElementById("chatInput").value+=e;}

function broadcastMessage(){const msg=prompt("Enter broadcast message:");if(!msg)return;push(ref(db,'global_v2'),{uid:uUID,sender:'ADMIN',text:msg,ts:serverTimestamp(),broadcast:true});}
function toggleMute(){onValue(ref(db,'system/mute'),s=>{set(ref(db,'system/mute'),!s.val());},{onlyOnce:true});}

function syncMsgs(){
  onValue(query(ref(db,currentRoom),limitToLast(50)),s=>{
    const f=document.getElementById("chatFeed");f.innerHTML="";
    s.forEach(m=>{
      const d=m.val();
      let cls=d.uid===uUID?'me':'other';
      if(d.broadcast) cls='other';
      f.innerHTML+=`<div class="msg ${cls}"><small style="opacity:0.5;font-size:10px;">${d.sender}</small>${d.text?`<div>${d.text}</div>`:''}${d.img?`<img src="${d.img}">`:''}</div>`;
    });
    f.scrollTop=f.scrollHeight;
  });
}

function typing(){update(ref(db,'nodes/'+uUID),{typing:true});setTimeout(()=>update(ref(db,'nodes/'+uUID),{typing:false}),3000);}
function showTyping(){onValue(ref(db,'nodes'),s=>{let typingUsers=[];s.forEach(u=>{const d=u.val();if(d.typing && u.key!==uUID) typingUsers.push(d.name);});document.getElementById("typingIndicator").innerText = typingUsers.length>0 ? typingUsers.join(", ")+" is typing..." : "";});}
setInterval(showTyping,1000);

function syncUsers(){
  onValue(ref(db,'nodes'),s=>{
    const l=document.getElementById("usersList"); l.innerHTML="";
    s.forEach(u=>{
      const d=u.val();
      if(!d.online)return;
      const node=document.createElement('div'); node.className='user-card'; node.innerText=d.name;
      node.onclick=()=>{if(u.key!==uUID){currentRoom=uUID<u.key?`${uUID}_${u.key}`:`${u.key}_${uUID}`;showPage('chatPage');syncMsgs();}};
      l.appendChild(node);
    });
  });
}

function createGroup(){
  const g=document.getElementById("groupName").value.trim();
  if(!g)return alert("Enter group name!");
  currentRoom=`group_${g}`;
  document.getElementById("groupName").value="";
  syncMsgs();
  alert("Group '"+g+"' created. Users can join by same name!");
}

onAuthStateChanged(auth,user=>{
  if(user){
    uUID=user.uid;
    const savedName=localStorage.getItem("uN");
    const savedAdmin=localStorage.getItem("uA")==="true";
    if(savedName){ uName=savedName; isAdmin=savedAdmin; if(isAdmin) document.getElementById("adminTools").style.display="flex"; document.getElementById("loginScreen").style.display="none"; document.getElementById("welcomeBox").innerText=`Welcome, ${uName}!`; set(ref(db,'nodes/'+uUID),{name:uName,online:true,typing:false}); syncUsers(); syncMsgs(); }
  }
});
</script>
</body>
</html>
