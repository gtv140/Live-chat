<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0a0a0a; --card:#121212; --text:#e5e5e5;
  --primary:#6b5bff; --accent:#ff5c8d; --success:#22c55e;
}
*{box-sizing: border-box;}
body{margin:0; font-family:system-ui,-apple-system; background:var(--bg); color:var(--text); height: 100dvh; display: flex; flex-direction: column; overflow: hidden;}

/* Header */
header{padding:15px; display:flex; justify-content:space-between; align-items:center; background:var(--card); border-bottom:1px solid #222; flex-shrink: 0;}
header h1{margin:0; font-size:22px; color:var(--primary); text-shadow:0 0 10px var(--primary);}
header .btns{display:flex; gap:15px;}
header button{background:none; border:none; color:white; font-size:18px; cursor:pointer;}

/* Main Content Area */
.app{max-width:480px; margin:auto; width: 100%; flex:1; display:flex; flex-direction:column; overflow: hidden;}
.page{display:none; padding:15px; flex:1; overflow-y:auto;}
.page.active{display:block;}

/* Hero & Stats - Screenshot Style */
.hero{background:linear-gradient(135deg,#ff5c8d,#6b5bff); color:#fff; padding:25px; border-radius:20px; text-align:center; margin-bottom:15px; box-shadow: 0 5px 15px rgba(107,91,255,0.4);}
.hero h2{margin:0; font-size:20px;}
.dashboard-stat{background:var(--card); padding:18px; margin-bottom:12px; border-radius:18px; border:1px solid #222; display:flex; justify-content:space-between; align-items:center; box-shadow: 0 4px 10px rgba(0,0,0,0.3);}
.dashboard-stat h4{margin:0; color:var(--accent); font-size: 15px;}

/* Chat Area Fix */
.chat-box{background:var(--card); border-radius:18px; padding:15px; height:50vh; overflow-y:auto; margin-bottom:12px; border:1px solid #222; display:flex; flex-direction:column; gap:10px;}
.msg{background:#1a1a1a; padding:12px; border-radius:15px; font-size:14px; position:relative; border-left:3px solid var(--primary); align-self: flex-start; max-width: 85%;}
.msg.me{align-self: flex-end; border-left:none; border-right:3px solid var(--accent); background:#222;}
.msg img{width:100%; border-radius:10px; margin-top:5px; display:block;}
.msg .actions{position:absolute; top:5px; right:8px; cursor:pointer; font-size: 11px; opacity: 0.5;}

/* Input Fix - Screenshot Style */
.input-row{display:flex; gap:10px; background:var(--card); padding:10px; border-radius:15px; border:1px solid #333; margin-bottom: 5px; flex-shrink: 0;}
.input-row input{flex:1; background:transparent; border:none; color:#fff; outline:none; padding:5px;}
.input-row button, .input-row label{background:none; border:none; color:var(--primary); font-size:20px; cursor:pointer; display:flex; align-items:center;}

/* Navigation Fix */
nav{display:flex; justify-content:space-around; background:var(--card); padding:12px 0; border-top:1px solid #222; flex-shrink: 0;}
nav button{background:none; border:none; font-size:22px; color:#555; cursor:pointer;}
nav button.active{color:var(--primary);}

/* Lists */
.user, .group{display:flex; align-items:center; gap:10px; padding:12px; background:var(--card); border-radius:14px; border:1px solid #222; margin-bottom:10px;}
.dot{width:10px; height:10px; background:var(--success); border-radius:50%; box-shadow: 0 0 5px var(--success);}

/* About & Contact */
.contact-card{background:var(--card); padding:15px; border-radius:15px; border:1px solid #222; margin-bottom:10px;}
.contact-card a{color:var(--primary); text-decoration:none;}
</style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div class="btns">
            <button onclick="toggleTheme()"><i class="fa-solid fa-moon"></i></button>
            <button onclick="logout()"><i class="fa-solid fa-right-from-bracket"></i></button>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="hero">
            <h2>Welcome <span id="dashUser">User</span> 🚀</h2>
            <p>Real-Time Chat & Active Connections</p>
        </div>
        <div class="dashboard-stat"><h4>Total Users</h4><span id="onlineCount">0</span></div>
        <div class="dashboard-stat"><h4>Total Groups</h4><span id="groupCount">0</span></div>
    </div>

    <div id="chat" class="page">
        <div class="chat-box" id="chatBox"></div>
        <div class="input-row">
            <label for="imgInp"><i class="fa-solid fa-paperclip"></i></label>
            <input type="file" id="imgInp" hidden accept="image/*" onchange="upImg(this)">
            <input id="msgInput" placeholder="Message…"/>
            <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="users" class="page">
        <h3 style="font-size:14px; color:var(--muted)">ONLINE NODES</h3>
        <div id="userList"></div>
        <h3 style="font-size:14px; color:var(--muted); margin-top:20px;">ACTIVE GROUPS</h3>
        <div id="groupList"></div>
        <div class="input-row" style="margin-top:10px;">
            <input id="groupInput" placeholder="Group name..."/>
            <button onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
        </div>
    </div>

    <div id="contact" class="page">
        <div class="contact-card">
            <h4 style="margin:0; color:var(--accent);">Support Email</h4>
            <p><a href="mailto:webhub262@gmail.com">webhub262@gmail.com</a></p>
        </div>
        <div class="contact-card">
            <h4 style="margin:0; color:var(--accent);">Instagram</h4>
            <p><a href="#">@mr_nazim073</a></p>
        </div>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
        <button onclick="openPage('contact',this)"><i class="fa-solid fa-envelope"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let currentUser = localStorage.getItem("chat_user") || prompt("Enter Username:");
if(currentUser){
    localStorage.setItem("chat_user", currentUser);
    document.getElementById("dashUser").textContent = currentUser;
    updateStatus(true);
}

function updateStatus(status){
    const userRef=ref(db,"users/"+currentUser);
    set(userRef,{name:currentUser,online:status});
    if(status) onDisconnect(userRef).update({online:false});
}

window.logout=()=>{ updateStatus(false); localStorage.removeItem("chat_user"); location.reload(); };

window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

let curChat = "";
let isGroup = false;

// Online Users Logic
onValue(ref(db,"users"),snap=>{
  let online=0; document.getElementById("userList").innerHTML="";
  snap.forEach(u=>{
    if(u.val().online){
      online++;
      if(u.key!==currentUser){
        let d=document.createElement("div"); d.className="user";
        d.innerHTML=`<div class="dot"></div>${u.val().name}`;
        d.onclick=()=>{ curChat=u.key; isGroup=false; loadChat(); openPage('chat',document.querySelectorAll('nav button')[1]); };
        document.getElementById("userList").appendChild(d);
      }
    }
  });
  document.getElementById("onlineCount").textContent=online;
});

// Groups Logic
onValue(ref(db,"groups"),snap=>{
  document.getElementById("groupList").innerHTML="";
  document.getElementById("groupCount").textContent=snap.size || 0;
  snap.forEach(g=>{
    let d=document.createElement("div"); d.className="group"; d.textContent=g.key;
    d.onclick=()=>{ curChat=g.key; isGroup=true; loadChat(); openPage('chat',document.querySelectorAll('nav button')[1]); };
    document.getElementById("groupList").appendChild(d);
  });
});

window.createGroup=()=>{
  let g=document.getElementById("groupInput").value.trim();
  if(g){ set(ref(db,"groups/"+g),{createdBy:currentUser}); document.getElementById("groupInput").value=''; }
};

function loadChat(){
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  onValue(ref(db,path),snap=>{
    const box = document.getElementById("chatBox"); box.innerHTML="";
    snap.forEach(m=>{
      const d = m.val();
      const div=document.createElement("div");
      div.className=`msg ${d.from===currentUser?'me':''}`;
      let content = d.type === 'img' ? `<img src="${d.text}">` : d.text;
      div.innerHTML=`<b style="font-size:10px; display:block;">${d.from}</b>${content} <div class="actions" onclick="deleteMsg('${path}','${m.key}')">🗑️</div>`;
      box.appendChild(div);
    });
    box.scrollTop=box.scrollHeight;
  });
}

window.sendMsg=()=>{
  const inp=document.getElementById('msgInput');
  if(!inp.value || !curChat) return;
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  push(ref(db,path),{from:currentUser, text:inp.value, type:'text'});
  inp.value='';
};

window.upImg=(e)=>{
    const reader = new FileReader();
    reader.onload=(f)=>{
        const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
        push(ref(db,path),{from:currentUser, text:f.target.result, type:'img'});
    };
    reader.readAsDataURL(e.files[0]);
};

window.deleteMsg=(path,key)=>{ if(confirm("Delete?")) remove(ref(db,path+"/"+key)); };
window.toggleTheme=()=>{ document.body.style.filter = document.body.style.filter === 'invert(1)' ? 'invert(0)' : 'invert(1)'; };
</script>
</body>
</html>
