<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Empire Dashboard | Realtime Chat</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --primary:#a855f7; --secondary:#ec4899; --bg:#030712; --glass:rgba(30,41,59,0.7); --text:#f9fafb;
}
*{box-sizing:border-box;font-family:'Plus Jakarta Sans',sans-serif;transition:0.3s;}
body{margin:0;background:var(--bg);color:var(--text);height:100vh;display:flex;overflow:hidden;}
/* Sidebar */
.sidebar{width:70px;background:#000;display:flex;flex-direction:column;align-items:center;padding:20px 0;gap:25px;transition:all 0.3s;}
.sidebar .icon-btn{width:50px;height:50px;border-radius:15px;display:flex;align-items:center;justify-content:center;color:#4b5563;font-size:1.2rem;cursor:pointer;}
.sidebar .icon-btn.active{background:var(--primary);color:white;box-shadow:0 0 20px rgba(168,85,247,0.4);}
#menuToggle{display:none;position:fixed;top:15px;left:15px;z-index:2000;width:40px;height:40px;border-radius:50%;background:var(--primary);color:#fff;border:none;font-size:1.2rem;cursor:pointer;}
/* Main */
.main{flex:1;display:flex;flex-direction:column;position:relative;overflow:hidden;}
header{padding:12px 20px;display:flex;justify-content:space-between;align-items:center;background:rgba(3,7,18,0.9);backdrop-filter:blur(15px);border-bottom:1px solid rgba(255,255,255,0.05);}
.brand{font-weight:900;font-size:1.2rem;background:linear-gradient(to right,#c084fc,#fb7185);-webkit-background-clip:text;-webkit-text-fill-color:transparent;}
/* Content */
.content{flex:1;display:flex;overflow:hidden;position:relative;}
.chat-view{flex:1;display:flex;flex-direction:column;position:relative;}
.feed{flex:1;overflow-y:auto;padding:15px;display:flex;flex-direction:column;gap:12px;padding-bottom:100px;}
.user-list{width:220px;background:rgba(255,255,255,0.02);padding:15px;border-left:1px solid rgba(255,255,255,0.05);transition:all 0.3s;}
.u-card{display:flex;align-items:center;gap:10px;padding:8px;border-radius:12px;cursor:pointer;margin-bottom:6px;background:rgba(255,255,255,0.03);}
.u-card:hover{background:rgba(255,255,255,0.08);}
.ban-hammer{color:#ef4444;margin-left:auto;font-size:11px;font-weight:800;border:1px solid #ef4444;padding:2px 5px;border-radius:4px;display:none;}
/* Messages */
.msg{max-width:80%;padding:10px 14px;border-radius:18px;font-size:13px;position:relative;word-wrap:break-word;}
.me{align-self:flex-end;background:var(--primary);border-bottom-right-radius:4px;}
.other{align-self:flex-start;background:var(--glass);border-bottom-left-radius:4px;}
.msg img{max-width:100%;border-radius:10px;margin-top:6px;}
/* Dock */
.dock{position:absolute;bottom:15px;left:10px;right:10px;background:rgba(30,41,59,0.95);padding:6px;border-radius:50px;display:flex;align-items:center;gap:8px;border:1px solid rgba(255,255,255,0.1);}
.dock input{flex:1;background:none;border:none;color:white;outline:none;padding-left:8px;font-size:0.95rem;}
.dock button{width:36px;height:36px;border-radius:50%;display:flex;align-items:center;justify-content:center;}
.mute-msg{position:absolute;inset:0;background:rgba(0,0,0,0.85);color:#ef4444;display:none;align-items:center;justify-content:center;border-radius:50px;font-weight:800;font-size:12px;text-align:center;padding:10px;}
/* Auth */
#auth{position:fixed;inset:0;z-index:2000;background:var(--bg);display:flex;align-items:center;justify-content:center;}
.auth-box{width:90%;max-width:350px;text-align:center;padding:35px 25px;background:var(--glass);border-radius:30px;border:1px solid rgba(255,255,255,0.1);}
.auth-box input{font-size:1rem;padding:12px;margin-bottom:12px;border-radius:12px;border:none;background:#000;color:#fff;text-align:center;width:100%;}
.auth-box button{font-size:1rem;padding:12px;border-radius:12px;border:none;background:var(--primary);color:white;cursor:pointer;font-weight:800;}
@media(max-width:850px){.user-list{display:none;}#menuToggle{display:block;}.sidebar{position:fixed;left:-80px;top:0;height:100%;flex-direction:column;}.sidebar.show{left:0;}}
</style>
</head>
<body>
<button id="menuToggle"><i class="fa-solid fa-bars"></i></button>
<div id="auth">
  <div class="auth-box">
    <h1 class="brand" style="font-size:2rem;">EMPIRE_V2</h1>
    <input type="text" id="loginName" placeholder="Codename">
    <button onclick="boot()">LOGIN</button>
  </div>
</div>

<nav class="sidebar" id="sidebar">
  <div class="icon-btn active" onclick="switchSection('home')"><i class="fa-solid fa-house"></i></div>
  <div class="icon-btn" onclick="switchSection('users')"><i class="fa-solid fa-users"></i></div>
  <div class="icon-btn" onclick="switchSection('groups')"><i class="fa-solid fa-comments"></i></div>
  <div class="icon-btn" onclick="switchSection('about')"><i class="fa-solid fa-circle-info"></i></div>
  <div class="icon-btn" onclick="switchSection('contact')"><i class="fa-solid fa-envelope"></i></div>
  <div class="icon-btn" onclick="switchSection('settings')"><i class="fa-solid fa-gear"></i></div>
</nav>

<div class="main">
  <header>
    <div class="brand" id="headerTitle">HOME</div>
    <div id="badge" style="display:none;color:gold;"><i class="fa-solid fa-crown"></i></div>
  </header>
  <div class="content">
    <div id="home" class="section">Welcome to Empire Dashboard!</div>
    <div id="users" class="section" style="display:none;">
      <h3>Active Users</h3>
      <div id="uList"></div>
    </div>
    <div id="groups" class="section" style="display:none;">
      <h3>Chat Groups</h3>
      <div class="chat-view">
        <div class="feed" id="feed"></div>
        <div class="dock">
          <input type="text" id="mIn" placeholder="Enter message..." onkeydown="if(event.key==='Enter') send()">
          <button onclick="send()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
      </div>
    </div>
    <div id="about" class="section" style="display:none;">
      <h3>About Us</h3><p>This is a modern Empire Realtime Chat Dashboard.</p>
    </div>
    <div id="contact" class="section" style="display:none;">
      <h3>Contact Us</h3><p>Email: support@empire.com</p>
    </div>
    <div id="settings" class="section" style="display:none;">
      <h3>Settings</h3><p>Profile and preferences.</p>
      <div id="adminTools" style="display:none;">
        <span>GOD MODE ACTIVE</span>
        <button onclick="toggleMute()">TOGGLE MUTE</button>
        <button onclick="wipeData()">WIPE CHAT</button>
      </div>
    </div>
  </div>
</div>

<script type="module">
// Firebase setup
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
let uUID,uName,isAdmin=false,currentRoom="global_v2";

onAuthStateChanged(auth,user=>{if(user){const savedName=localStorage.getItem("uN");const savedAdmin=localStorage.getItem("uA")==="true";if(savedName){uUID=user.uid;uName=savedName;isAdmin=savedAdmin;checkBan(user.uid);}}});

window.boot=async()=>{const n=document.getElementById("loginName").value.trim();if(!n)return;let admin=false;if(n.toLowerCase()==="nazim"){if(prompt("Master PIN:")==="786")admin=true;else{alert("Wrong PIN");return;}}const res=await signInAnonymously(auth);uUID=res.user.uid;uName=n;isAdmin=admin;localStorage.setItem("uN",n);localStorage.setItem("uA",admin);checkBan(uUID);};

function checkBan(uid){onValue(ref(db,'banned/'+uid),s=>{if(s.val()){document.body.innerHTML="<h1 style='color:red;text-align:center;padding:50px;'>🚫 BANNED</h1>";localStorage.clear();}else start();});}

function start(){
document.getElementById("auth").style.display="none";
if(isAdmin){document.getElementById("adminTools").style.display="flex";document.getElementById("badge").style.display="block";}
set(ref(db,'nodes/'+uUID),{name:uName,online:true});syncUsers();syncMsgs();onValue(ref(db,'system/mute'),s=>{document.getElementById("muteScreen")&&(document.getElementById("muteScreen").style.display=(s.val()&&!isAdmin)?"flex":"none");});}

window.switchSection=(sec)=>{document.querySelectorAll('.section').forEach(s=>s.style.display='none');document.getElementById(sec).style.display='block';document.getElementById('headerTitle').innerText=sec.toUpperCase();document.querySelectorAll('.sidebar .icon-btn').forEach(b=>b.classList.remove('active'));if(sec==='home')document.querySelector('.sidebar .icon-btn:first-child').classList.add('active');};

window.send=(p=null)=>{const i=document.getElementById("mIn");if(!i.value.trim()&&!p)return;push(ref(db,currentRoom),{uid:uUID,sender:uName,text:i.value,img:p,ts:serverTimestamp()});i.value="";};

function syncMsgs(){onValue(query(ref(db,currentRoom),limitToLast(50)),s=>{const f=document.getElementById("feed");if(!f)return;f.innerHTML="";s.forEach(m=>{const d=m.val();f.innerHTML+=`<div class="msg ${d.uid===uUID?'me':'other'}"><small style="opacity:0.5;font-size:10px;font-weight:800;">${d.sender}</small>${d.text?`<div>${d.text}</div>`:""}${d.img?`<img src='${d.img}'>`:""}</div>`;});f.scrollTop=f.scrollHeight;});}

function syncUsers(){onValue(ref(db,'nodes'),s=>{const l=document.getElementById("uList");l.innerHTML="";s.forEach(u=>{if(u.key===uUID)return;const d=u.val();const node=document.createElement('div');node.className='u-card';node.innerHTML=`<img src="https://ui-avatars.com/api/?name=${d.name}&background=random&color=fff" style="width:30px;border-radius:10px;"><span style="font-size:0.8rem;">${d.name}</span>${isAdmin?`<span class="ban-hammer" style="display:block;" onclick="if(confirm('Ban ${d.name}?'))set(ref(db,'banned/${u.key}'),true)">BAN</span>`:""}`;l.appendChild(node);});});}

window.toggleMute=()=>{onValue(ref(db,'system/mute'),s=>{set(ref(db,'system/mute'),!s.val());},{onlyOnce:true});};
window.wipeData=()=>{if(confirm("Wipe Global?"))remove(ref(db,'global_v2'));};
window.logout=()=>{localStorage.clear();signOut(auth).then(()=>location.reload());};
document.getElementById("menuToggle").addEventListener("click",()=>{document.getElementById("sidebar").classList.toggle("show");});
</script>
</body>
</html>
