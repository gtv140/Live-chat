<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect Ultimate 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

<style>
:root {
    --bg:#0a0d12; --card:#141b22; --primary:#00d2ff; --accent:#ff0055; --text:#e6edf3;
    --glass: rgba(20,27,34,0.8); --grad: linear-gradient(135deg,#00d2ff 0%,#3a7bd5 100%);
}
* {margin:0; padding:0; box-sizing:border-box; outline:none; -webkit-tap-highlight-color:transparent;}
body {font-family:'Segoe UI', Roboto, sans-serif; background:var(--bg); color:var(--text); display:flex; flex-direction:column; height:100vh; overflow:hidden;}
header {padding:40px 20px 15px; display:flex; justify-content:space-between; align-items:center; background:var(--bg); border-bottom:1px solid #1a1d21; z-index:1000;}
header h1 {font-size:22px; font-weight:900; background: var(--grad); -webkit-background-clip:text; -webkit-text-fill-color:transparent;}
.viewport {flex:1; overflow:hidden; position:relative;}
.page {display:none; flex-direction:column; padding:15px; height:100%; animation:slideIn 0.3s ease-out;}
.page.active {display:flex;}
@keyframes slideIn {from {opacity:0; transform:translateX(20px);} to {opacity:1; transform:translateX(0);}}
.search-bar {background:#161b22; border-radius:15px; padding:10px 15px; display:flex; align-items:center; gap:10px; margin-bottom:10px;}
.search-bar input {flex:1; background:none; border:none; color:#fff; font-size:14px;}
.scroll-container {flex:1; overflow-y:auto; background: rgba(255,255,255,0.03); border-radius:15px; border:1px solid #21262d; padding:10px; margin-bottom:10px; -webkit-overflow-scrolling:touch;}
.label {font-size:10px; font-weight:700; opacity:0.6; text-transform:uppercase; letter-spacing:1.5px; margin-bottom:5px;}
.card {display:flex; align-items:center; gap:12px; background:var(--card); padding:12px; border-radius:15px; margin-bottom:8px; border:1px solid #30363d; transition:0.2s;}
.card:active {transform:scale(0.97); background:#1c2128;}
.pfp {width:40px; height:40px; border-radius:50%; background:var(--grad); display:flex; align-items:center; justify-content:center; font-weight:bold; color:white; overflow:hidden;}
#chatBox {display:flex; flex-direction:column; gap:10px; padding-bottom:20px;}
.m-bubble {padding:10px 14px; border-radius:20px; max-width:75%; font-size:14px; line-height:1.5; word-wrap:break-word; position:relative;}
.m-bubble.me {align-self:flex-end; background: var(--grad); border-bottom-right-radius:2px; box-shadow:0 4px 12px rgba(0,210,255,0.2);}
.m-bubble.other {align-self:flex-start; background:#21262d; border-bottom-left-radius:2px;}
.reactions {position:absolute; bottom:-18px; right:10px; font-size:12px; opacity:0.7; cursor:pointer;}
.bottom-shelf {flex-shrink:0; padding:10px 0 30px 0; background:var(--bg); border-top:1px solid #1a1d21;}
.msg-input-wrap {width:92%; max-width:500px; margin:0 auto; background:#161b22; padding:10px 15px; border-radius:30px; display:flex; align-items:center; gap:10px; border:1px solid #30363d; display:none;}
.msg-input-wrap input {flex:1; background:none; border:none; color:white; font-size:14px;}
nav {width:92%; max-width:500px; margin:0 auto; height:65px; background: var(--glass); backdrop-filter:blur(15px); border-radius:35px; display:flex; justify-content:space-around; align-items:center; border:1px solid rgba(255,255,255,0.1); box-shadow:0 -8px 20px rgba(0,0,0,0.4);}
nav button {background:none; border:none; color:#484f58; font-size:22px; transition:0.3s; padding:10px; cursor:pointer;}
nav button.active {color: var(--primary); transform:translateY(-4px);}
#loginOverlay, #banOverlay {position:fixed; inset:0; display:flex; align-items:center; justify-content:center; z-index:10000; flex-direction:column;}
#loginOverlay {background:#000;}
#banOverlay {display:none; background:#000; text-align:center; padding:30px;}
button {cursor:pointer; font-family:'Segoe UI', Roboto, sans-serif;}
</style>
</head>
<body>

<!-- LOGIN -->
<div id="loginOverlay">
    <img id="loginPic" src="temp_tieba_picture_2_temp1771736492722.jpg" style="width:80px; height:80px; border-radius:50%; margin-bottom:10px;">
    <i class="fa-solid fa-bolt-lightning" style="font-size:50px; color:var(--primary); margin-bottom:15px;"></i>
    <h2 style="font-weight:900; letter-spacing:1px; margin-bottom:15px;">LIVE CONNECT</h2>
    <input type="text" id="uInp" placeholder="Enter Nickname" style="width:80%; padding:14px; border-radius:18px; border:1px solid #333; background:#111; color:#fff; text-align:center; margin-bottom:15px;">
    <button onclick="doLogin()" style="width:80%; padding:14px; border-radius:18px; background:var(--grad); color:#fff; border:none; font-weight:700;">START SESSION</button>
</div>

<div id="banOverlay">
    <i class="fa-solid fa-hand-slash" style="font-size:60px; color:var(--accent); margin-bottom:15px;"></i>
    <h1 style="color:var(--accent); margin-bottom:5px;">TERMINAL BANNED</h1>
    <p style="opacity:0.6;">Your access has been revoked by the administrator.</p>
</div>

<header>
<h1>Live Connect</h1>
<div style="display:flex; gap:15px; align-items:center;">
<i class="fa-solid fa-crown" id="admIco" style="display:none; color:gold; font-size:20px;" onclick="nav('admin')"></i>
<i class="fa-solid fa-power-off" style="color:var(--accent); font-size:20px;" onclick="logout()"></i>
</div>
</header>

<div class="viewport">
  <!-- HOME -->
  <div id="home" class="page active">
      <div style="background:var(--grad); padding:25px; border-radius:20px; margin-bottom:15px;">
          <h2 id="welcome" style="margin:0; font-size:22px;">Hello!</h2>
          <p style="font-size:12px; opacity:0.9; margin-top:5px;">Secure Protocol V150.0 Active</p>
      </div>
      <div class="label">Latest Broadcast</div>
      <div class="scroll-container" id="sysAlert" style="font-size:13px; line-height:1.5; padding:15px;">
          No active system-wide alerts at the moment.
      </div>
  </div>

  <!-- CHAT -->
  <div id="chat" class="page">
      <div class="scroll-container" id="chatScroll">
          <div id="chatBox"></div>
      </div>
  </div>

  <!-- NODES -->
  <div id="nodes" class="page">
      <div class="search-bar">
          <i class="fa-solid fa-magnifying-glass" style="opacity:0.4;"></i>
          <input type="text" id="uSearch" placeholder="Search for users..." onkeyup="filterNodes()">
      </div>
      <div class="label">Online Terminals</div>
      <div class="scroll-container" id="uList"></div>
      <div style="display:flex; justify-content:space-between; align-items:center; margin-top:5px;">
          <div class="label">Active Clusters</div>
          <button onclick="mkGr()" style="background:none; border:1px solid var(--primary); color:var(--primary); font-size:10px; padding:3px 10px; border-radius:8px;">+ NEW</button>
      </div>
      <div class="scroll-container" id="gList"></div>
  </div>

  <!-- ADMIN -->
  <div id="admin" class="page">
      <div class="label">Broadcast Command</div>
      <div class="card">
          <input type="text" id="bcMsg" placeholder="Type global alert..." style="flex:1; background:none; border:none; color:white;">
          <i class="fa-solid fa-paper-plane" style="color:var(--primary);" onclick="sendBC()"></i>
      </div>
      <div class="label">Terminal Management</div>
      <div class="scroll-container" id="aList"></div>
      <button onclick="nuke()" style="width:100%; padding:14px; background:var(--accent); color:white; border:none; border-radius:18px; font-weight:bold; margin-top:5px;">NUKE SYSTEM DATA</button>
  </div>
</div>

<!-- BOTTOM INPUT -->
<div class="bottom-shelf">
  <div class="msg-input-wrap" id="chatInputArea">
      <label for="imgInp"><i class="fa-solid fa-image" style="color:var(--primary); font-size:22px;"></i></label>
      <input type="file" id="imgInp" hidden onchange="upImg(this)">
      <input id="msgInp" placeholder="Type a message...">
      <i class="fa-solid fa-circle-arrow-up" style="color:var(--primary); font-size:26px;" onclick="sendMsg()"></i>
  </div>
  <nav>
      <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
      <button onclick="nav('chat', this)"><i class="fa-solid fa-comments"></i></button>
      <button onclick="nav('nodes', this)"><i class="fa-solid fa-users-viewfinder"></i></button>
  </nav>
</div>

<script>
// Firebase Config
var firebaseConfig = {apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", authDomain:"live-chat-b810c.firebaseapp.com", databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com", projectId:"live-chat-b810c", storageBucket:"live-chat-b810c.firebasestorage.app", messagingSenderId:"555058795334", appId:"1:555058795334:web:f668887409800c32970b47"};
firebase.initializeApp(firebaseConfig);
var db=firebase.database();
var currentUser = localStorage.getItem("lc_u");

// LOGIN
if(currentUser){ document.getElementById("loginOverlay").style.display="none"; initApp(currentUser);}
function doLogin(){
    var u=document.getElementById("uInp").value.trim();
    var pic=document.getElementById("loginPic").src;
    if(u){
        db.ref("users/"+u).update({online:true,frozen:false,avatar:pic}).then(()=>{
            localStorage.setItem("lc_u",u);
            document.getElementById("loginOverlay").style.display="none";
            initApp(u);
        });
    }
}

// INIT APP
function initApp(u){
    document.getElementById("welcome").innerText="Hi, "+u;
    if(u==="Admin786") document.getElementById("admIco").style.display="block";
    db.ref("broadcast").on("value",s=>{ if(s.val()) document.getElementById("sysAlert").innerHTML=`<div class='card' style='background:#ff005511; border:1px solid var(--accent);'><i class='fa-solid fa-bullhorn' style='color:var(--accent);'></i> ${s.val().msg}</div>`;});
    db.ref("users/"+u+"/frozen").on("value",s=>{document.getElementById("banOverlay").style.display=s.val()?"flex":"none";});
    db.ref("users").on("value",s=>{
        var ul=document.getElementById("uList"), al=document.getElementById("aList"); ul.innerHTML=""; al.innerHTML="";
        s.forEach(n=>{
            var l=n.key.charAt(0).toUpperCase();
            if(n.key!==u){
                ul.innerHTML+=`<div class='card node-item' data-name='${n.key}' onclick='startChat("${n.key}", false)'><div class='pfp'><img src='${n.val().avatar||""}' style='width:100%; height:100%; border-radius:50%;'></div><div style='flex:1;'><b>${n.key}</b></div><i class='fa-solid fa-circle' style='color:${n.val().online?'#00ff88':'#333'}; font-size:8px;'></i></div>`;
                if(u==="Admin786") al.innerHTML+=`<div class='card'><div class='pfp' style='background:#333'>${l}</div><div style='flex:1;'><b>${n.key}</b></div><button onclick='hjk("${n.key}",${!n.val().frozen})' style='background:${n.val().frozen?'#00ff88':'#ff0055'}; border:none; padding:6px 12px; border-radius:12px; color:white; font-weight:bold;'>${n.val().frozen?'FREE':'BAN'}</button></div>`;
            }
        });
    });
    db.ref("groups").on("value",s=>{ var gl=document.getElementById("gList"); gl.innerHTML=""; s.forEach(g=>{ gl.innerHTML+=`<div class='card' onclick='startChat("${g.key}", true)'><div class='pfp' style='background:#444'>#</div><div style='flex:1;'><b>${g.key}</b></div><i class='fa-solid fa-chevron-right' style='opacity:0.2'></i></div>`;});});
    startChat("global", true);
}

// CHAT FUNCTIONS
var currentPath="global";
function startChat(t,isG){
    currentPath=isG?"groups/"+t:"pvt/"+[currentUser,t].sort().join("_");
    nav('chat',document.querySelectorAll('nav button')[1]);
    db.ref(currentPath).off();
    db.ref(currentPath).on("value",s=>{
        var box=document.getElementById("chatBox"); box.innerHTML="";
        s.forEach(m=>{
            var d=m.val();
            var reactions=d.reactions? '❤️'.repeat(d.reactions.length) : '';
            box.innerHTML+=`<div class='m-bubble ${d.from===currentUser?'me':'other'}'><b>${d.from}</b><br>${d.type==='img'?'<img src="'+d.txt+'" style="width:100%; border-radius:12px; margin-top:5px;">':d.txt}<div class='reactions'>${reactions}</div></div>`;
        });
        document.getElementById("chatScroll").scrollTop=999999;
    });
}

// NAVIGATION
function nav(p,b){ document.querySelectorAll('.page').forEach(x=>x.classList.remove('active')); document.getElementById(p).classList.add('active'); document.querySelectorAll('nav button').forEach(x=>x.classList.remove('active')); if(b && b.tagName==='BUTTON') b.classList.add('active'); document.getElementById("chatInputArea").style.display=(p==='chat')?'flex':'none'; }

// MESSAGES
function sendMsg(){ var i=document.getElementById("msgInp"); if(i.value.trim()!==""){ db.ref(currentPath).push({from:currentUser,txt:i.value.trim(),type:'text',reactions:[]}); i.value=""; } }
function upImg(e){ var file=e.files[0]; if(!file)return; var r=new FileReader(); r.onload=function(f){ db.ref(currentPath).push({from:currentUser,txt:f.target.result,type:'img',reactions:[]}); } r.readAsDataURL(file); }

// ADMIN FUNCTIONS
function sendBC(){ var m=document.getElementById("bcMsg").value.trim(); if(m && currentUser==='Admin786'){ db.ref("broadcast").set({msg:m}); document.getElementById("bcMsg").value=""; } }
function hjk(user,freeze){ if(currentUser==='Admin786'){ db.ref("users/"+user).update({frozen:freeze}); } }
function nuke(){ if(currentUser==='Admin786' && confirm("Are you sure? This will delete ALL data!")){ db.ref().remove(); location.reload(); } }
function mkGr(){
    var g = prompt("Enter new group name:");
    if(g && g.trim() !== ""){
        db.ref("groups/"+g.trim()).set({createdBy: currentUser, createdAt: Date.now()});
    }
}

// FILTER USERS
function filterNodes(){
    var f = document.getElementById("uSearch").value.toLowerCase();
    document.querySelectorAll(".node-item").forEach(n=>{
        n.style.display = n.dataset.name.toLowerCase().includes(f) ? "flex" : "none";
    });
}

// LOGOUT
function logout(){
    if(currentUser){
        db.ref("users/"+currentUser).update({online:false});
        localStorage.removeItem("lc_u");
        location.reload();
    }
}

// BEFORE UNLOAD: SET USER OFFLINE
window.addEventListener("beforeunload", ()=>{
    if(currentUser){
        db.ref("users/"+currentUser).update({online:false});
    }
});
</script>
</body>
</html>
