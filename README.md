<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Empire Modern Dashboard</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
--primary:#8b5cf6; --secondary:#f43f5e; --bg:#0b0f1a; --glass:rgba(20,25,40,0.75); --text:#f8fafc;
--hover:#272b3d;
}
*{box-sizing:border-box;font-family:'Plus Jakarta Sans',sans-serif;transition:0.3s;}
body{margin:0;background:var(--bg);color:var(--text);height:100vh;display:flex;overflow:hidden;}
/* Sidebar */
.sidebar{width:70px;background:#01030b;display:flex;flex-direction:column;align-items:center;padding:20px 0;gap:20px;position:relative;}
.sidebar .icon-btn{width:50px;height:50px;border-radius:15px;display:flex;align-items:center;justify-content:center;color:#777;font-size:1.2rem;cursor:pointer;position:relative;}
.sidebar .icon-btn:hover{background:var(--hover);color:white;}
.sidebar .icon-btn.active{background:var(--primary);color:white;box-shadow:0 0 15px rgba(139,92,246,0.4);}
.sidebar .icon-btn .tooltip{position:absolute;left:70px;background:var(--glass);padding:5px 10px;border-radius:8px;font-size:0.75rem;opacity:0;transition:0.3s;white-space:nowrap;}
.sidebar .icon-btn:hover .tooltip{opacity:1;}
/* Header */
header{padding:12px 20px;display:flex;justify-content:space-between;align-items:center;background:rgba(0,0,0,0.8);backdrop-filter:blur(10px);border-bottom:1px solid rgba(255,255,255,0.05);}
.brand{font-weight:900;font-size:1.3rem;background:linear-gradient(to right,#8b5cf6,#f43f5e);-webkit-background-clip:text;-webkit-text-fill-color:transparent;}
/* Main Content */
.main{flex:1;display:flex;flex-direction:column;position:relative;}
.content{flex:1;display:flex;overflow:hidden;position:relative;}
.section{flex:1;overflow-y:auto;padding:20px;display:none;}
.section.active{display:block;}
/* Chat */
.chat-view{flex:1;display:flex;flex-direction:column;position:relative;}
.feed{flex:1;overflow-y:auto;padding:15px;display:flex;flex-direction:column;gap:12px;padding-bottom:110px;}
.msg{max-width:80%;padding:10px 14px;border-radius:18px;font-size:13px;position:relative;word-wrap:break-word;}
.me{align-self:flex-end;background:var(--primary);border-bottom-right-radius:4px;}
.other{align-self:flex-start;background:var(--glass);border-bottom-left-radius:4px;}
.broadcast{background:var(--secondary);color:white;padding:8px;border-radius:12px;align-self:center;font-weight:800;}
.msg img{max-width:100%;border-radius:10px;margin-top:6px;}
.timestamp{font-size:10px;opacity:0.5;margin-top:4px;}
/* Dock */
.dock{position:absolute;bottom:20px;left:15px;right:15px;background:var(--glass);padding:8px;border-radius:50px;display:flex;align-items:center;gap:8px;border:1px solid rgba(255,255,255,0.1);}
.dock input{flex:1;background:none;border:none;color:white;outline:none;padding-left:12px;font-size:0.95rem;}
.dock button{width:38px;height:38px;border-radius:50%;display:flex;align-items:center;justify-content:center;background:var(--primary);color:white;border:none;cursor:pointer;}
.mute-msg{position:absolute;inset:0;background:rgba(0,0,0,0.85);color:#f43f5e;display:none;align-items:center;justify-content:center;border-radius:50px;font-weight:800;font-size:12px;text-align:center;padding:10px;}
/* Users */
.user-list{width:250px;background:rgba(255,255,255,0.02);padding:15px;border-left:1px solid rgba(255,255,255,0.05);}
.u-card{display:flex;align-items:center;gap:10px;padding:10px;border-radius:12px;cursor:pointer;margin-bottom:6px;background:rgba(255,255,255,0.03);}
.u-card:hover{background:rgba(255,255,255,0.08);}
.ban-hammer{color:#f43f5e;margin-left:auto;font-size:11px;font-weight:800;border:1px solid #f43f5e;padding:2px 6px;border-radius:5px;display:none;}
/* Auth */
#auth{position:fixed;inset:0;z-index:2000;background:var(--bg);display:flex;align-items:center;justify-content:center;}
.auth-box{width:90%;max-width:350px;text-align:center;padding:40px;background:var(--glass);border-radius:30px;border:1px solid rgba(255,255,255,0.1);}
.auth-box input{font-size:1rem;padding:12px;margin-bottom:12px;border-radius:12px;border:none;background:#000;color:#fff;text-align:center;width:100%;}
.auth-box button{font-size:1rem;padding:12px;border-radius:12px;border:none;background:var(--primary);color:white;cursor:pointer;font-weight:800;}
/* Responsive */
@media(max-width:850px){.user-list{display:none;}.sidebar{position:fixed;left:-80px;top:0;height:100%;}.sidebar.show{left:0;}}
</style>
</head>
<body>

<div id="auth">
<div class="auth-box">
<h1 class="brand">EMPIRE_V3</h1>
<input type="text" id="loginName" placeholder="Codename">
<button onclick="boot()">LOGIN</button>
</div>
</div>

<nav class="sidebar">
<div class="icon-btn active" onclick="switchSection('home')"><i class="fa-solid fa-house"></i><span class="tooltip">Home</span></div>
<div class="icon-btn" onclick="switchSection('users')"><i class="fa-solid fa-users"></i><span class="tooltip">Users</span></div>
<div class="icon-btn" onclick="switchSection('groups')"><i class="fa-solid fa-comments"></i><span class="tooltip">Chat</span></div>
<div class="icon-btn" onclick="switchSection('about')"><i class="fa-solid fa-circle-info"></i><span class="tooltip">About</span></div>
<div class="icon-btn" onclick="switchSection('contact')"><i class="fa-solid fa-envelope"></i><span class="tooltip">Contact</span></div>
<div class="icon-btn" onclick="switchSection('settings')"><i class="fa-solid fa-gear"></i><span class="tooltip">Settings</span></div>
<div class="icon-btn" onclick="logoutAll()"><i class="fa-solid fa-power-off"></i><span class="tooltip">Logout</span></div>
</nav>

<div class="main">
<header>
<div class="brand" id="headerTitle">HOME</div>
<div id="badge" style="display:none;color:gold;"><i class="fa-solid fa-crown"></i></div>
</header>
<div class="content">
<div id="home" class="section active">
<h2>Welcome, <span id="uDisplay"></span>!</h2>
<p>Empire Realtime Dashboard v3 - Modern UI</p>
</div>
<div id="users" class="section">
<h3>Active Users</h3>
<div id="uList"></div>
</div>
<div id="groups" class="section">
<h3>Global Chat</h3>
<div class="chat-view">
<div class="feed" id="feed"></div>
<div class="dock">
<input type="text" id="mIn" placeholder="Message..." onkeydown="if(event.key==='Enter') send()">
<button onclick="send()"><i class="fa-solid fa-paper-plane"></i></button>
<input type="text" id="broadcastInput" placeholder="Broadcast..." style="display:none;">
<button id="broadcastBtn" style="display:none;" onclick="broadcast()"><i class="fa-solid fa-bullhorn"></i></button>
</div>
<div class="mute-msg" id="muteScreen">🔇 GLOBAL CHAT IS LOCKED</div>
</div>
</div>
<div id="about" class="section">
<h3>About</h3>
<p>This is the modern Empire Dashboard with full realtime features.</p>
</div>
<div id="contact" class="section">
<h3>Contact</h3>
<p>Email: support@empire.com</p>
</div>
<div id="settings" class="section">
<h3>Settings</h3>
<div id="adminTools" style="display:none;">
<span>GOD MODE ACTIVE</span>
<button onclick="toggleMute()">TOGGLE MUTE</button>
<button onclick="wipeData()">WIPE CHAT</button>
</div>
</div>
</div>
</div>

<script type="module">
// Firebase imports
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
import { getDatabase, ref, set, push, onValue, serverTimestamp, remove, query, limitToLast } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
const auth = getAuth(app);
const db = getDatabase(app);

let uUID,uName,isAdmin=false,currentRoom="global_v3";

onAuthStateChanged(auth,user=>{
if(user){
const savedName=localStorage.getItem("uN");
const savedAdmin=localStorage.getItem("uA")==="true";
if(savedName){uUID=user.uid; uName=savedName; isAdmin=savedAdmin; start();}
}
});

window.boot=async()=>{
const n=document.getElementById("loginName").value.trim();
if(!n)return;
let admin=false;
if(n.toLowerCase()==="nazim"){if(prompt("Master PIN:")==="786")admin=true;else{alert("Wrong PIN");return;}}
const res=await signInAnonymously(auth);
uUID=res.user.uid; uName=n; isAdmin=admin;
localStorage.setItem("uN",n); localStorage.setItem("uA",admin);
start();
};

function start(){
document.getElementById("auth").style.display="none";
document.getElementById("uDisplay").innerText=uName;
if(isAdmin){
document.getElementById("adminTools").style.display="flex";
document.getElementById("badge").style.display="block";
document.getElementById("broadcastInput").style.display="block";
document.getElementById("broadcastBtn").style.display="block";
}
set(ref(db,'nodes/'+uUID),{name:uName,online:true});
syncUsers(); syncMsgs();
onValue(ref(db,'system/mute'),s=>{document.getElementById("muteScreen").style.display=(s.val()&&!isAdmin)?"flex":"none";});
}

window.switchSection=(sec)=>{
document.querySelectorAll('.section').forEach(s=>s.classList.remove('active'));
document.getElementById(sec).classList.add('active');
document.getElementById('headerTitle').innerText=sec.toUpperCase();
document.querySelectorAll('.sidebar .icon-btn').forEach(b=>b.classList.remove('active'));
};

window.send=(p=null)=>{
const i=document.getElementById("mIn");
if(!i.value.trim()&&!p)return;
push(ref(db,currentRoom),{uid:uUID,sender:uName,text:i.value,img:p,ts:serverTimestamp()});
i.value="";
};

function broadcast(){
const msg=document.getElementById("broadcastInput").value.trim();
if(!msg)return;
push(ref(db,'broadcast'),{uid:uUID,sender:uName,text:msg,ts:serverTimestamp()});
document.getElementById("broadcastInput").value="";
}

function syncMsgs(){
onValue(query(ref(db,currentRoom),limitToLast(50)),s=>{
const f=document.getElementById("feed"); if(!f)return; f.innerHTML="";
s.forEach(m=>{
const d=m.val();
f.innerHTML+=`<div class="msg ${d.uid===uUID?'me':'other'}"><small style="opacity:0.5;font-size:10px;font-weight:800;">${d.sender}</small>${d.text?`<div>${d.text}</div>`:""}<div class="timestamp">${new Date(d.ts).toLocaleTimeString()}</div></div>`;
});
f.scrollTop=f.scrollHeight;
});
onValue(ref(db,'broadcast'),s=>{
const f=document.getElementById("feed"); if(!f)return;
s.forEach(m=>{
const d=m.val();
f.innerHTML+=`<div class="broadcast">${d.sender}: ${d.text}</div>`;
});
});
}

function syncUsers(){
onValue(ref(db,'nodes'),s=>{
const l=document.getElementById("uList"); l.innerHTML="";
s.forEach(u=>{
if(u.key===uUID)return;
const d=u.val();
const node=document.createElement('div');
node.className='u-card';
node.innerHTML=`<img src="https://ui-avatars.com/api/?name=${d.name}&background=random&color=fff" style="width:30px;border-radius:10px;"><span style="font-size:0.8rem;">${d.name}</span>${isAdmin?`<span class="ban-hammer" style="display:block;" onclick="if(confirm('Ban ${d.name}?'))set(ref(db,'banned/${u.key}'),true)">BAN</span>`:""}`;
l.appendChild(node);
});
}

window.toggleMute=()=>{onValue(ref(db,'system/mute'),s=>{set(ref(db,'system/mute'),!s.val());},{onlyOnce:true});};
window.wipeData=()=>{if(confirm("Wipe Global?"))remove(ref(db,currentRoom));};
window.logoutAll=()=>{localStorage.clear(); signOut(auth).then(()=>location.reload());};
</script>

</body>
</html>
