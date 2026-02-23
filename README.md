<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#05070a; --card:#12141d; --text:#fafafa; --muted:#8b949e;
  --primary:#6366f1; --accent:#f43f5e; --success:#10b981;
}
body{margin:0;font-family:'Inter',system-ui,-apple-system;background:var(--bg);color:var(--text);transition:.3s; height: 100dvh; display: flex; flex-direction: column; overflow: hidden;}
body.light{--bg:#f8fafc;--card:#ffffff;--text:#0f172a;--muted:#64748b;}

.app{max-width:480px;margin:auto;height:100%;display:flex;flex-direction:column;width:100%;}
header{padding:16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);border-bottom:1px solid rgba(255,255,255,0.05);z-index:100;}
header h1{margin:0;font-size:20px;font-weight:800;background:linear-gradient(90deg, var(--primary), var(--accent)); -webkit-background-clip:text; -webkit-text-fill-color:transparent;}

.page{display:none;padding:16px;flex:1;overflow-y:auto; animation: fadeIn 0.3s ease;}
.page.active{display:block;}
@keyframes fadeIn { from{opacity:0; transform:translateY(10px);} to{opacity:1; transform:translateY(0);} }

.hero{background:linear-gradient(135deg,var(--primary),var(--accent));color:#fff;padding:24px;border-radius:24px;text-align:center;box-shadow:0 10px 30px rgba(99,102,241,0.3);margin-bottom:20px;}
.dashboard-stat{background:var(--card);padding:18px;margin-bottom:12px;border-radius:20px;border:1px solid rgba(255,255,255,0.05);display:flex;justify-content:space-between;align-items:center;}
.dashboard-stat h4{margin:0; color:var(--muted); font-size:14px;}

/* CHAT AREA FIX */
#chat.page { display:none; flex-direction:column; padding:10px; height:100%; }
#chat.page.active { display:flex; }
.chat-box{background:var(--card);border-radius:24px;padding:15px;flex:1;overflow-y:auto;margin-bottom:12px;border:1px solid rgba(255,255,255,0.05);}
.msg{background:#1e293b;color:#fff;padding:12px 16px;border-radius:18px;margin-bottom:12px;font-size:14px;position:relative;max-width:85%;}
.msg.me{background:var(--primary); margin-left:auto; border-bottom-right-radius:2px;}
.msg img{width:100%; border-radius:12px; margin-top:8px;}

.input-row{display:flex;gap:10px;background:var(--card);padding:10px;border-radius:20px;border:1px solid rgba(255,255,255,0.1);}
.input-row input{flex:1;padding:12px;border:none;background:transparent;color:var(--text);outline:none;font-size:15px;}
.input-row button{width:45px; height:45px; border:none;border-radius:15px;background:var(--primary);color:#fff;cursor:pointer;}

.user, .group{display:flex;align-items:center;gap:12px;margin-bottom:10px;padding:14px;background:var(--card);border-radius:18px;border:1px solid rgba(255,255,255,0.05);}
.dot{width:10px;height:10px;background:var(--success);border-radius:50%;box-shadow:0 0 10px var(--success);}

nav{display:flex;justify-content:space-around;background:var(--card);padding:12px 0;border-top:1px solid rgba(255,255,255,0.05);padding-bottom:env(safe-area-inset-bottom);}
nav button{background:none;border:none;font-size:22px;color:var(--muted);transition:.3s;}
nav button.active{color:var(--primary); transform:scale(1.2);}

/* ADMIN PANEL */
.ban-btn{background:rgba(244,63,94,0.1); color:var(--accent); border:none; padding:6px 12px; border-radius:8px; font-size:12px; font-weight:bold;}
</style>
</head>
<body>
<div class="app">
<header>
<h1 id="topTitle">Live Connect</h1>
<div style="display:flex; gap:12px;">
    <button onclick="toggleTheme()"><i class="fa-solid fa-circle-half-stroke"></i></button>
    <button onclick="logout()" style="color:var(--accent)"><i class="fa-solid fa-power-off"></i></button>
</div>
</header>

<div id="loginPage" class="page active">
    <div style="text-align:center; padding:20px; margin-top:30%;">
        <div style="font-size:50px; margin-bottom:10px;">🛡️</div>
        <h2>Access Terminal</h2>
        <input type="text" id="usernameInput" placeholder="Enter Username" style="width:100%; padding:16px; border-radius:16px; border:1px solid #333; background:#111; color:white; margin:20px 0;">
        <button onclick="login()" style="width:100%; padding:16px; border-radius:16px; background:var(--primary); color:white; border:none; font-weight:bold; box-shadow:0 10px 20px rgba(99,102,241,0.3);">Initialize Connection</button>
    </div>
</div>

<div id="home" class="page">
    <div class="hero">
        <h2 style="margin:0">Welcome, <span id="dashUser"></span></h2>
        <p style="font-size:13px; opacity:0.8;">Status: Secure Connection Active</p>
    </div>
    <div id="bcShow"></div>
    <div class="dashboard-stat"><h4>Total Network Users</h4><span id="onlineCount">0</span></div>
    <div class="dashboard-stat"><h4>Encrypted Groups</h4><span id="groupCount">0</span></div>
    <div id="adminPanel" style="display:none; margin-top:20px;">
        <h3 style="color:var(--accent)">Admin Control</h3>
        <div class="input-row">
            <input id="bcInput" placeholder="Broadcast message..."/>
            <button onclick="sendBC()"><i class="fa-solid fa-bullhorn"></i></button>
        </div>
        <div id="allUsers" style="margin-top:10px;"></div>
    </div>
</div>

<div id="chat" class="page">
    <div class="chat-box" id="chatBox"></div>
    <div class="input-row">
        <label for="imgInp"><i class="fa-solid fa-paperclip" style="color:var(--muted); margin-left:10px; cursor:pointer;"></i></label>
        <input type="file" id="imgInp" hidden onchange="upImg(this)" accept="image/*">
        <input id="msgInput" placeholder="Secure message…"/>
        <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div id="users" class="page">
    <h3 style="font-size:14px; color:var(--muted)">ACTIVE NODES</h3><div id="userList"></div>
    <h3 style="font-size:14px; color:var(--muted); margin-top:20px;">CLUSTERS</h3><div id="groupList"></div>
    <div class="input-row" style="margin-top:15px;">
        <input id="groupInput" placeholder="Cluster name..."/>
        <button onclick="createGroup()">➕</button>
    </div>
</div>

<nav>
    <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house-chimney-window"></i></button>
    <button onclick="openPage('chat',this)"><i class="fa-solid fa-message-code"></i></button>
    <button onclick="openPage('users',this)"><i class="fa-solid fa-users-viewfinder"></i></button>
</nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.appspot.com",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser=localStorage.getItem("lc_u");
let curChat="";
let isGroup=false;

if(currentUser){ loginSuccess(currentUser); }

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname) return;
  currentUser=uname;
  localStorage.setItem("lc_u", uname);
  loginSuccess(uname);
};

function loginSuccess(uname){
    document.getElementById("dashUser").textContent=uname;
    const userRef=ref(db,"users/"+uname);
    update(userRef,{online:true, banned:false});
    onDisconnect(userRef).update({online:false});
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("home").classList.add("active");
    if(uname==="Admin786") document.getElementById("adminPanel").style.display="block";
    initData();
}

function initData(){
    onValue(ref(db,"users"),snap=>{
        let online=0; document.getElementById("userList").innerHTML=""; 
        const adminList = document.getElementById("allUsers"); if(adminList) adminList.innerHTML="";
        snap.forEach(u=>{
            if(u.val().banned && u.key===currentUser) logout();
            if(u.val().online){
                online++;
                if(u.key!==currentUser){
                    let d=document.createElement("div"); d.className="user";
                    d.innerHTML=`<div class="dot"></div><b>${u.key}</b>`;
                    d.onclick=()=>{ curChat=u.key; isGroup=false; openChat(); };
                    document.getElementById("userList").appendChild(d);
                }
            }
            if(currentUser==="Admin786" && u.key!==currentUser){
                adminList.innerHTML += `<div class="dashboard-stat"><span>${u.key}</span><button class="ban-btn" onclick="banUser('${u.key}')">BAN</button></div>`;
            }
        });
        document.getElementById("onlineCount").textContent=online;
    });

    onValue(ref(db,"groups"),snap=>{
        document.getElementById("groupList").innerHTML="";
        document.getElementById("groupCount").textContent=snap.size || 0;
        snap.forEach(g=>{
            let d=document.createElement("div"); d.className="group"; d.innerHTML=`<i class="fa-solid fa-hashtag" style="color:var(--primary)"></i> <b>${g.key}</b>`;
            d.onclick=()=>{ curChat=g.key; isGroup=true; openChat(); };
            document.getElementById("groupList").appendChild(d);
        });
    });

    onValue(ref(db,"broadcast"),s=>{
        if(s.val()) document.getElementById("bcShow").innerHTML=`<div class="dashboard-stat" style="border-color:var(--accent); background:rgba(244,63,94,0.05); color:var(--accent)"><span><i class="fa-solid fa-bullhorn"></i> ${s.val()}</span></div>`;
    });
}

function openChat(){
    document.getElementById("topTitle").textContent = curChat;
    openPage('chat',document.querySelectorAll('nav button')[1]);
    const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
    onValue(ref(db,path),snap=>{
        const box=document.getElementById("chatBox"); box.innerHTML="";
        snap.forEach(m=>{
            const d=m.val(); const isMe = d.from===currentUser;
            const div=document.createElement("div"); div.className=`msg ${isMe?'me':''}`;
            let content = d.type==='img' ? `<img src="${d.text}">` : d.text;
            div.innerHTML=`<small style="font-size:10px; opacity:0.6">${d.from}</small><br>${content}`;
            box.appendChild(div);
        });
        box.scrollTop=box.scrollHeight;
    });
}

window.sendMsg=()=>{
    const inp=document.getElementById('msgInput'); if(!inp.value || !curChat) return;
    const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
    push(ref(db,path),{from:currentUser, text:inp.value, type:'text'});
    inp.value='';
};

window.upImg=(e)=>{
    const reader = new FileReader(); reader.onload=(f)=>{
        const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
        push(ref(db,path),{from:currentUser, text:f.target.result, type:'img'});
    }; reader.readAsDataURL(e.files[0]);
};

window.sendBC=()=>{ const m=document.getElementById("bcInput").value; if(m) set(ref(db,"broadcast"),m); };
window.banUser=(u)=>{ if(confirm("Ban "+u+"?")) update(ref(db,"users/"+u),{banned:true, online:false}); };
window.createGroup=()=>{ const g=document.getElementById("groupInput").value; if(g) set(ref(db,"groups/"+g),{by:currentUser}); };
window.openPage=(id,btn)=>{
    document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');
};
window.logout=()=>{ update(ref(db,"users/"+currentUser),{online:false}).then(()=>{ localStorage.clear(); location.reload(); }); };
window.toggleTheme=()=>{document.body.classList.toggle('light');};
</script>
</body>
</html>
