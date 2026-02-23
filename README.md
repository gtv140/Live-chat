<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0a0a0a; --card:#111; --text:#e5e5e5; --muted:#aaa;
  --primary:#6b5bff; --accent:#ff5c8d; --success:#22c55e;
}
/* Mobile Height Fix */
body{margin:0;font-family:system-ui,-apple-system;background:var(--bg);color:var(--text); height: 100dvh; display: flex; flex-direction: column; overflow: hidden;}
body.light{--bg:#f1f1f1;--card:#fff;--text:#111;--muted:#555;}

.app{max-width:480px;margin:auto;height:100%;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 3px 15px rgba(0,0,0,.7);z-index:10;}
header h1{margin:0;font-size:22px;color:var(--primary);text-shadow:0 0 12px var(--accent);}

.page{display:none;padding:16px;flex:1;overflow-y:auto;transition:0.3s;}
.page.active{display:block;}

/* Chat Box Area Fix for Mobile */
#chat.page { display:none; flex-direction:column; padding:10px; height:100%; }
#chat.page.active { display:flex; }
.chat-box{background:var(--card);border-radius:18px;padding:14px;flex:1;overflow-y:auto;margin-bottom:10px;box-shadow:0 0 12px var(--primary);}

.msg{background:#222;color:#fff;padding:10px 14px;border-radius:16px;margin-bottom:10px;font-size:14px;position:relative;box-shadow:0 0 6px var(--primary);}
.msg img{width:100%; border-radius:10px; margin-top:5px;}
.time{font-size:9px; opacity:0.5; display:block; text-align:right; margin-top:4px;}

.input-row{display:flex;gap:8px;background:var(--card);padding:8px;border-radius:14px;align-items:center;}
.input-row input{flex:1;padding:12px;border-radius:10px;border:1px solid #444;background:#111;color:#fff;outline:none;}
.input-row button{padding:12px 16px;border:none;border-radius:10px;background:linear-gradient(90deg,#ff5c8d,#6b5bff);color:#fff;cursor:pointer;}

nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;border-top:1px solid #333;}
nav button{background:none;border:none;font-size:20px;color:var(--muted);cursor:pointer;}
nav button.active{color:var(--primary);}

.user,.group{display:flex;align-items:center;gap:10px;margin-bottom:10px;padding:12px;background:var(--card);border-radius:12px;box-shadow:0 0 8px var(--primary);}
</style>
</head>
<body>
<div class="app">
<header>
<h1 id="topTitle">Live Connect</h1>
<div>
    <button onclick="toggleTheme()" style="background:none;border:none;color:white;"><i class="fa-solid fa-moon"></i></button>
    <button onclick="logout()" style="background:none;border:none;color:var(--accent);"><i class="fa-solid fa-right-from-bracket"></i></button>
</div>
</header>

<div id="loginPage" class="page active">
    <div style="text-align:center; padding:20px; background:var(--card); border-radius:20px;">
        <h3>Welcome Back</h3>
        <input type="text" id="usernameInput" placeholder="Enter Name" style="width:100%; padding:14px; margin:15px 0; border-radius:10px; background:#000; color:white; border:1px solid #444;">
        <button onclick="login()" style="width:100%; padding:14px; background:var(--primary); color:white; border:none; border-radius:10px; font-weight:bold;">Continue</button>
    </div>
</div>

<div id="home" class="page">
    <div style="background:linear-gradient(135deg,#ff5c8d,#6b5bff); padding:25px; border-radius:20px; text-align:center; color:white;">
        <h2>Hi, <span id="dashUser"></span> 👋</h2>
        <p>Online: <span id="onlineCount">0</span> | Groups: <span id="groupCount">0</span></p>
    </div>
</div>

<div id="chat" class="page">
    <div class="chat-box" id="chatBox"></div>
    <div class="input-row">
        <label for="imgInp"><i class="fa-solid fa-paperclip" style="color:var(--primary); cursor:pointer;"></i></label>
        <input type="file" id="imgInp" hidden onchange="upImg(this)" accept="image/*">
        <input id="msgInput" placeholder="Message…"/>
        <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div id="users" class="page">
    <h3>Online Users</h3><div id="userList"></div>
    <h3 style="margin-top:16px;">Groups</h3><div id="groupList"></div>
    <div class="input-row" style="margin-top:10px;">
        <input id="groupInput" placeholder="New group name"/>
        <button onclick="createGroup()">➕</button>
    </div>
</div>

<nav>
    <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
    <button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
    <button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
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

let currentUser=localStorage.getItem("lc_user");
let curChat="";
let isGroup=false;

if(currentUser){ 
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("home").classList.add("active");
    initApp(); 
}

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname) return;
  currentUser=uname;
  localStorage.setItem("lc_user", uname);
  initApp();
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

function initApp(){
    document.getElementById("dashUser").textContent=currentUser;
    const userRef=ref(db,"users/"+currentUser);
    update(userRef,{online:true});
    onDisconnect(userRef).update({online:false});
    loadLists();
}

window.logout=()=>{
    update(ref(db,"users/"+currentUser),{online:false}).then(()=>{
        localStorage.clear();
        location.reload();
    });
};

function loadLists(){
    onValue(ref(db,"users"),snap=>{
        let online=0; document.getElementById("userList").innerHTML="";
        snap.forEach(u=>{
            if(u.val().online){
                online++;
                if(u.key!==currentUser){
                    let d=document.createElement("div"); d.className="user";
                    d.innerHTML=`<div style="width:10px;height:10px;background:var(--success);border-radius:50%"></div> ${u.key}`;
                    d.onclick=()=>{ curChat=u.key; isGroup=false; openPage('chat',document.querySelectorAll('nav button')[1]); loadChat(); };
                    document.getElementById("userList").appendChild(d);
                }
            }
        });
        document.getElementById("onlineCount").textContent=online;
    });

    onValue(ref(db,"groups"),snap=>{
        document.getElementById("groupList").innerHTML="";
        document.getElementById("groupCount").textContent=snap.size || 0;
        snap.forEach(g=>{
            let d=document.createElement("div"); d.className="group"; d.textContent=g.key;
            d.onclick=()=>{ curChat=g.key; isGroup=true; openPage('chat',document.querySelectorAll('nav button')[1]); loadChat(); };
            document.getElementById("groupList").appendChild(d);
        });
    });
}

function loadChat(){
    if(!curChat) return;
    document.getElementById("topTitle").textContent = curChat;
    const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
    onValue(ref(db,path),snap=>{
        const box=document.getElementById("chatBox"); box.innerHTML="";
        snap.forEach(m=>{
            const d=m.val();
            const div=document.createElement("div"); div.className="msg";
            let content = d.type === 'img' ? `<img src="${d.text}">` : d.text;
            div.innerHTML=`<small style="color:var(--primary)">${d.from}</small><br>${content}<span class="time">12:00 PM</span>`;
            box.appendChild(div);
        });
        box.scrollTop=box.scrollHeight;
    });
}

window.sendMsg=()=>{
    const input=document.getElementById('msgInput');
    if(!input.value || !curChat) return;
    const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
    push(ref(db,path),{from:currentUser, text:input.value, type:'text'});
    input.value='';
};

window.upImg=(e)=>{
    const file = e.files[0];
    const reader = new FileReader();
    reader.onload=(f)=>{
        const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
        push(ref(db,path),{from:currentUser, text:f.target.result, type:'img'});
    };
    reader.readAsDataURL(file);
};

window.createGroup=()=>{
    const g=document.getElementById("groupInput").value.trim();
    if(g) set(ref(db,"groups/"+g),{owner:currentUser});
    document.getElementById("groupInput").value="";
};

window.openPage=(id,btn)=>{
    document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');
};

window.toggleTheme=()=>{document.body.classList.toggle('light');};
</script>
</body>
</html>
