<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Empire Dashboard App Style</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
--primary:#8b5cf6; --secondary:#f43f5e; --bg:#0b0f1a; --glass:rgba(20,25,40,0.75); --text:#f8fafc; --hover:#272b3d;
}
*{box-sizing:border-box;font-family:sans-serif;}
body{margin:0;background:var(--bg);color:var(--text);display:flex;flex-direction:column;height:100vh;}
header{padding:10px;background:rgba(0,0,0,0.8);display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid rgba(255,255,255,0.05);}
.main{flex:1;display:flex;overflow:hidden;position:relative;}
.section{flex:1;overflow-y:auto;padding:15px;display:none;}
.section.active{display:block;}
.feed{flex:1;overflow-y:auto;padding:10px;display:flex;flex-direction:column;gap:8px;}
.msg{max-width:80%;padding:8px 12px;border-radius:15px;}
.me{align-self:flex-end;background:var(--primary);}
.other{align-self:flex-start;background:var(--glass);}
.dock{position:absolute;bottom:70px;left:15px;right:15px;background:var(--glass);padding:8px;border-radius:50px;display:flex;gap:5px;}
.dock input{flex:1;background:none;border:none;color:white;padding-left:8px;}
.dock button{background:var(--primary);border:none;color:white;padding:0 10px;border-radius:50%;cursor:pointer;}
.user-list{padding:10px;}
.u-card{padding:8px;border-radius:10px;display:flex;align-items:center;gap:8px;background:rgba(255,255,255,0.03);cursor:pointer;}
.u-card:hover{background:rgba(255,255,255,0.07);}
.auth-box{position:fixed;inset:0;background:var(--bg);display:flex;align-items:center;justify-content:center;}
.auth-inner{background:var(--glass);padding:25px;border-radius:20px;text-align:center;width:90%;max-width:300px;}
.auth-inner input, .auth-inner button{width:100%;padding:10px;margin:5px 0;border:none;border-radius:12px;}
.auth-inner button{background:var(--primary);color:white;cursor:pointer;}
.bottom-nav{height:60px;background:rgba(0,0,0,0.85);display:flex;justify-content:space-around;align-items:center;position:fixed;bottom:0;left:0;right:0;border-top:1px solid rgba(255,255,255,0.05);}
.bottom-nav .nav-btn{color:#777;font-size:1.5rem;cursor:pointer;position:relative;}
.bottom-nav .nav-btn.active{color:var(--primary);}
#moreMenu{position:absolute;bottom:60px;right:10px;background:var(--glass);border-radius:12px;display:none;flex-direction:column;gap:5px;padding:8px;}
#moreMenu button{background:none;border:none;color:white;text-align:left;padding:5px;border-radius:8px;cursor:pointer;}
#moreMenu button:hover{background:var(--hover);}
.welcome-box{background:var(--glass);padding:15px;border-radius:15px;margin-bottom:15px;text-align:center;}
.active-count{background:#272b3d;padding:10px;border-radius:12px;margin-top:10px;}
.about-box{background:var(--glass);padding:15px;border-radius:15px;margin-top:15px;}
.typing-indicator{font-size:12px;color:#aaa;margin-top:2px;}
</style>
</head>
<body>

<div id="auth" class="auth-box">
<div class="auth-inner">
<h2>EMPIRE LOGIN</h2>
<input type="text" id="loginName" placeholder="Enter Codename">
<div id="adminPinDiv" style="display:none;"><input type="password" id="adminPin" placeholder="Admin PIN"></div>
<button onclick="boot()">Login</button>
</div>
</div>

<header><span id="headerTitle">HOME</span><span id="badge" style="display:none;">👑</span></header>

<div class="main">
<!-- HOME -->
<div id="home" class="section active">
<div class="welcome-box">
<h3>Welcome, <span id="uDisplay"></span>!</h3>
<div class="active-count">Active Users: <span id="activeCount">0</span></div>
</div>
<div class="about-box">
<h4>About Our Website</h4>
<p>This is a modern real-time chat dashboard with admin powers, multi-user chat, broadcast messages, and private messaging features. Connect instantly!</p>
</div>
</div>

<!-- USERS -->
<div id="users" class="section"><h3>Active Users</h3><div id="uList"></div></div>

<!-- CHAT -->
<div id="chat" class="section">
<div class="feed" id="feed"></div>
<div class="typing-indicator" id="typing"></div>
<div class="dock">
<input type="text" id="mIn" placeholder="Type a message..." onkeydown="typingIndicator();if(event.key==='Enter') send()">
<button onclick="send()"><i class="fa-solid fa-paper-plane"></i></button>
<input type="text" id="broadcastInput" placeholder="Broadcast..." style="display:none;">
<button id="broadcastBtn" style="display:none;" onclick="broadcast()"><i class="fa-solid fa-bullhorn"></i></button>
</div>
</div>

<!-- SETTINGS -->
<div id="settings" class="section">
<div id="adminTools" style="display:none;">
<button onclick="toggleMute()">Mute/Unmute Chat</button>
<button onclick="wipeData()">Wipe Chat</button>
</div>
</div>
</div>

<div class="bottom-nav">
<div class="nav-btn active" onclick="switchSection('home')"><i class="fa-solid fa-house"></i></div>
<div class="nav-btn" onclick="switchSection('chat')"><i class="fa-solid fa-comments"></i></div>
<div class="nav-btn" onclick="switchSection('users')"><i class="fa-solid fa-users"></i></div>
<div class="nav-btn" onclick="toggleMore()"><i class="fa-solid fa-ellipsis"></i></div>
</div>

<div id="moreMenu">
<button onclick="switchSection('settings')">Settings</button>
<button onclick="logoutAll()">Logout</button>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
import { getDatabase, ref, set, push, onValue, serverTimestamp, remove, query, limitToLast } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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

let uUID,uName,isAdmin=false,currentRoom="global_simple";
let typingTimeout;

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
if(n.toLowerCase()==="nazim"){
document.getElementById("adminPinDiv").style.display="block";
const pin=document.getElementById("adminPin").value.trim();
if(pin!=="786"){alert("Wrong PIN"); return;} admin=true;
}
try{
const res=await signInAnonymously(auth);
uUID=res.user.uid; uName=n; isAdmin=admin;
localStorage.setItem("uN",n); localStorage.setItem("uA",admin);
start();
}catch(e){alert("Login failed");}
};

function start(){
document.getElementById("auth").style.display="none";
document.getElementById("uDisplay").innerText=uName;
if(isAdmin){
document.getElementById("adminTools").style.display="block";
document.getElementById("broadcastInput").style.display="block";
document.getElementById("broadcastBtn").style.display="block";
document.getElementById("badge").style.display="inline";
}
set(ref(db,'nodes/'+uUID),{name:uName,online:true});
syncUsers(); syncMsgs(); updateActiveCount();
onValue(ref(db,'system/mute'),s=>{document.getElementById("feed").style.opacity=(s.val()&&!isAdmin)?0.5:1;});
}

// Sections
window.switchSection=(sec)=>{
document.querySelectorAll('.section').forEach(s=>s.classList.remove('active'));
document.getElementById(sec).classList.add('active');
document.getElementById('headerTitle').innerText=sec.toUpperCase();
document.querySelectorAll('.bottom-nav .nav-btn').forEach(b=>b.classList.remove('active'));
};

// Chat
window.send=()=>{
const i=document.getElementById("mIn");
if(!i.value.trim())return;
push(ref(db,currentRoom),{uid:uUID,sender:uName,text:i.value,ts:serverTimestamp()});
i.value="";
typingIndicator(false);
};

// Typing Indicator
function typingIndicator(active=true){
const tRef=ref(db,'typing/'+currentRoom+'/'+uUID);
set(tRef,active?uName:null);
clearTimeout(typingTimeout);
if(active) typingTimeout=setTimeout(()=>{set(tRef,null);},3000);
onValue(ref(db,'typing/'+currentRoom),s=>{
let usersTyping=[];
s.forEach(c=>{if(c.val()) usersTyping.push(c.val());});
document.getElementById('typing').innerText=usersTyping.length?usersTyping.join(', ')+' is typing...':'';
});
}

// Broadcast
function broadcast(){
const msg=document.getElementById("broadcastInput").value.trim();
if(!msg)return;
push(ref(db,'broadcast'),{uid:uUID,sender:uName,text:msg,ts:serverTimestamp()});
document.getElementById("broadcastInput").value="";
}

// Sync Messages
function syncMsgs(){
onValue(query(ref(db,currentRoom),limitToLast(50)),s=>{
const f=document.getElementById("feed"); f.innerHTML="";
s.forEach(m=>{const d=m.val(); f.innerHTML+=`<div class="msg ${d.uid===uUID?'me':'other'}"><strong>${d.sender}:</strong> ${d.text}</div>`;});
});
onValue(ref(db,'broadcast'),s=>{const f=document.getElementById("feed"); s.forEach(m=>{const d=m.val(); f.innerHTML+=`<div class="msg" style="background:var(--secondary);color:white;text-align:center;">${d.sender}: ${d.text}</div>`;});});
}

// Users
function syncUsers(){
onValue(ref(db,'nodes'),s=>{
const l=document.getElementById("uList"); l.innerHTML="";
let count=1;
s.forEach(u=>{
if(u.key===uUID) return; 
count++;
const d=u.val();
const node=document.createElement('div'); node.className='u-card';
node.innerHTML=`<span>${d.name} ${d.online?'🟢':'⚪'}</span>${isAdmin?` <button onclick="if(confirm('Ban ${d.name}?'))set(ref(db,'banned/${u.key}'),true)">BAN</button>`:""}`;
node.onclick=()=>openPrivateChat(u.key,d.name);
l.appendChild(node);
});
document.getElementById('activeCount').innerText=count;
});
}

// Private chat
function openPrivateChat(tid,tname){
const rid = uUID<tid?`${uUID}_${tid}`:`${tid}_${uUID}`;
currentRoom='private/'+rid;
switchSection('chat');
document.getElementById('headerTitle').innerText='🔒 '+tname;
syncMsgs();
typingIndicator();
}

// Admin & Utilities
window.toggleMute=()=>{onValue(ref(db,'system/mute'),s=>{set(ref(db,'system/mute'),!s.val());},{onlyOnce:true});};
window.wipeData=()=>{if(confirm("Wipe Chat?"))remove(ref(db,currentRoom));};
window.logoutAll=()=>{localStorage.clear(); signOut(auth).then(()=>location.reload());};

// Bottom menu
window.toggleMore=()=>{
const m=document.getElementById("moreMenu");
m.style.display=m.style.display==='flex'?'none':'flex';
};
window.updateActiveCount=()=>{syncUsers();};
</script>

</body>
</html>
