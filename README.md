<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0a0a0a;
  --card:#111;
  --text:#e5e5e5;
  --muted:#aaa;
  --primary:#6b5bff;
  --accent:#ff5c8d;
  --success:#22c55e;
}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);transition:.3s;}
body.light{--bg:#f1f1f1;--card:#fff;--text:#111;--muted:#555;}
.app{max-width:480px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 3px 15px rgba(0,0,0,.7);}
header h1{margin:0;font-size:22px;color:var(--primary);text-shadow:0 0 12px var(--accent);}
header button{background:none;border:none;font-size:20px;color:var(--text);cursor:pointer;transition:.3s;}
header button:hover{color:var(--accent);}
.page{display:none;padding:16px;flex:1;transition:0.3s;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,#ff5c8d,#6b5bff);color:#fff;padding:28px;border-radius:20px;text-align:center;text-shadow:0 0 12px #fff;box-shadow:0 0 15px #6b5bff;transition:.3s;}
.hero h2{margin:0 0 10px;font-size:22px;}
.hero p{margin:0;font-size:14px;opacity:.9;}
.dashboard-stat{background:var(--card);padding:16px;margin-bottom:12px;border-radius:16px;box-shadow:0 0 12px var(--primary);display:flex;justify-content:space-between;align-items:center;transition:.3s;}
.dashboard-stat:hover{box-shadow:0 0 15px var(--accent);}
.dashboard-stat h4{margin:0;color:var(--accent);}
.dashboard-stat span{font-weight:bold;}
.chat-box{background:var(--card);border-radius:18px;padding:14px;height:36vh;overflow-y:auto;margin-bottom:12px;box-shadow:0 0 12px var(--primary);}
.msg{background:#222;color:#fff;padding:10px 14px;border-radius:16px;margin-bottom:10px;font-size:14px;position:relative;box-shadow:0 0 6px var(--primary);transition:.3s;}
.msg:hover{box-shadow:0 0 12px var(--accent);}
.msg .actions{position:absolute;top:6px;right:8px;display:flex;gap:6px;font-size:13px;cursor:pointer;}
.msg .comments{margin-top:8px;padding-left:14px;border-left:2px solid #444;}
.input-row{display:flex;gap:10px;}
.input-row input{flex:1;padding:14px;border-radius:14px;border:1px solid #444;background:#111;color:#fff;outline:none;}
.input-row input:focus{border-color:var(--primary);}
.input-row button{padding:14px 18px;border:none;border-radius:14px;background:linear-gradient(90deg,#ff5c8d,#6b5bff);color:#fff;cursor:pointer;box-shadow:0 0 10px #ff5c8d;transition:.3s;}
.input-row button:hover{box-shadow:0 0 15px #6b5bff;}
.user,.group{display:flex;align-items:center;gap:10px;margin-bottom:12px;cursor:pointer;padding:12px;background:var(--card);border-radius:14px;box-shadow:0 0 8px var(--primary);transition:.3s;}
.user:hover,.group:hover{background:#222;transform:translateY(-2px);}
.dot{width:12px;height:12px;background:var(--success);border-radius:50%;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:12px 0;box-shadow:0 -3px 12px rgba(0,0,0,.7);}
nav button{background:none;border:none;font-size:22px;color:var(--muted);cursor:pointer;transition:.3s;}
nav button.active{color:var(--primary);}
nav button:hover{color:var(--accent);}
.login-card{background:var(--card);padding:20px;border-radius:18px;box-shadow:0 0 15px var(--primary);}
.login-card input{width:100%;padding:14px;border-radius:14px;margin-bottom:12px;border:1px solid #444;background:#111;color:#fff;outline:none;}
.login-card button{width:100%;padding:14px;border:none;border-radius:14px;background:linear-gradient(90deg,#ff5c8d,#6b5bff);color:#fff;cursor:pointer;box-shadow:0 0 12px #ff5c8d;transition:.3s;}
.login-card button:hover{box-shadow:0 0 15px #6b5bff;}
.comment-input{margin-top:6px;display:flex;gap:6px;}
.comment-input input{flex:1;padding:8px;border-radius:10px;border:1px solid #444;font-size:13px;background:#111;color:#fff;outline:none;}
.comment-input button{padding:8px 10px;border:none;border-radius:10px;background:linear-gradient(90deg,#ff5c8d,#6b5bff);color:#fff;font-size:13px;cursor:pointer;transition:.3s;}
.comment-input button:hover{box-shadow:0 0 10px var(--primary);}
.contact-card{display:flex;align-items:center;gap:14px;background:var(--card);padding:14px 18px;border-radius:14px;margin-bottom:14px;box-shadow:0 0 12px var(--accent);transition:0.3s;}
.contact-card:hover{transform:translateY(-3px);box-shadow:0 0 18px var(--primary);}
.contact-card h4{margin:0;color:var(--accent);text-shadow:0 0 6px var(--primary);}
.contact-card p{margin:2px 0 0;font-size:14px;}
.contact-card a{text-decoration:none;color:var(--primary);}
.contact-card a:hover{text-decoration:underline;}
</style>
</head>
<body>
<div class="app">
<header>
<h1>Live Connect</h1>
<button onclick="toggleTheme()"><i class="fa-solid fa-moon"></i></button>
<button onclick="logout()"><i class="fa-solid fa-right-from-bracket"></i></button>
</header>

<!-- LOGIN -->
<div id="loginPage" class="page active">
<div class="login-card">
<h3>Enter Username</h3>
<input type="text" id="usernameInput" placeholder="Username">
<button onclick="login()">Continue</button>
</div>
</div>

<!-- DASHBOARD -->
<div id="home" class="page">
<div class="hero">
<h2>Welcome <span id="dashUser"></span> 🚀</h2>
<p>Real-Time Chat & Active Connections</p>
</div>
<div class="dashboard-stat"><h4>Total Users</h4><span id="onlineCount">0</span></div>
<div class="dashboard-stat"><h4>Total Groups</h4><span id="groupCount">0</span></div>
</div>

<!-- CHAT -->
<div id="chat" class="page">
<div class="chat-box" id="chatBox"></div>
<div class="input-row">
<input id="msgInput" placeholder="Type message…"/>
<button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
</div>
</div>

<!-- USERS & GROUPS -->
<div id="users" class="page">
<h3>Online Users</h3><div id="userList"></div>
<h3 style="margin-top:16px;">Groups</h3><div id="groupList"></div>
<div class="input-row" style="margin-top:6px;">
<input id="groupInput" placeholder="Create group"/>
<button onclick="createGroup()">➕</button>
</div>
</div>

<!-- ABOUT -->
<div id="about" class="page">
<h3>About Live Connect 🚀</h3>
<p>Live Connect is developed by <b>WebHub Technologies</b>, a leading provider of real-time communication apps.</p>
<ul>
<li>Real-time private & group chats with comments and likes</li>
<li>Live user tracking and professional dashboard</li>
<li>Dark/Light mode and modern UI design</li>
<li>Customizable groups, notifications, and admin controls</li>
<li>Secure, fast, and fully responsive on mobile & desktop</li>
</ul>
<p>Our mission is to connect people instantly with a safe, modern, and professional platform.</p>
</div>

<!-- CONTACT -->
<div id="contact" class="page">
<h3 style="color:var(--accent);text-shadow:0 0 8px var(--primary)">Contact & Support</h3>
<p style="margin-bottom:12px;">Reach out via:</p>

<div class="contact-card">
<i class="fa-solid fa-envelope" style="font-size:24px;"></i>
<div><h4>Email</h4><p><a href="mailto:webhub262@gmail.com">webhub262@gmail.com</a></p></div>
</div>

<div class="contact-card">
<i class="fab fa-facebook" style="color:#3b5998;font-size:24px;"></i>
<div><h4>Facebook</h4><p><a href="https://www.facebook.com/profile.php?id=100084218946114" target="_blank">Visit Profile</a></p></div>
</div>

<div class="contact-card">
<i class="fab fa-instagram" style="color:#c13584;font-size:24px;"></i>
<div><h4>Instagram</h4><p><a href="https://www.instagram.com/mr_nazim073?igsh=MXd4d2hmcWNvNjVsdQ==" target="_blank">Follow Us</a></p></div>
</div>

<div class="contact-card">
<i class="fab fa-youtube" style="color:#ff0000;font-size:24px;"></i>
<div><h4>YouTube</h4><p><a href="https://youtube.com/@CrazyKhanTV" target="_blank">Subscribe</a></p></div>
</div>

<div class="contact-card">
<i class="fa-solid fa-phone" style="color:var(--success);font-size:24px;"></i>
<div><h4>Phone</h4><p><a href="tel:+923001234567">+92 300 1234567</a></p></div>
</div>
</div>

<nav>
<button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
<button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
<button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
<button onclick="openPage('about',this)"><i class="fa-solid fa-circle-info"></i></button>
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

let currentUser=null;
let curChat="";
let isGroup=false;

const chatBox=document.getElementById("chatBox");
const userList=document.getElementById("userList");
const groupList=document.getElementById("groupList");
const dashUser=document.getElementById("dashUser");
const onlineCount=document.getElementById("onlineCount");
const groupCount=document.getElementById("groupCount");

window.login=()=>{
  const uname=document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username");return;}
  currentUser=uname;
  dashUser.textContent=currentUser;
  const userRef=ref(db,"users/"+uname);
  set(userRef,{name:uname,online:true});
  onDisconnect(userRef).update({online:false});
  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
};

window.logout=()=>{
  if(!currentUser) return;
  const userRef=ref(db,"users/"+currentUser);
  set(userRef,{name:currentUser,online:false});
  location.reload();
};

window.openPage=(id,btn)=>{
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
};

window.toggleTheme=()=>{document.body.classList.toggle('light');};

onValue(ref(db,"users"),snap=>{
  let online=0;
  userList.innerHTML="";
  snap.forEach(u=>{
    if(u.val().online){
      online++;
      if(u.key!==currentUser){
        const d=document.createElement("div");
        d.className="user";
        d.innerHTML=`<div class="dot"></div>${u.val().name}`;
        d.onclick=()=>{
          curChat=u.key; isGroup=false; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'));
        };
        userList.appendChild(d);
      }
    }
  });
  onlineCount.textContent=online;
});

onValue(ref(db,"groups"),snap=>{
  groupList.innerHTML="";
  groupCount.textContent=snap.size;
  snap.forEach(g=>{
    const d=document.createElement("div");
    d.className="group";
    d.textContent=g.key;
    d.onclick=()=>{
      curChat=g.key; isGroup=true; loadChat(); openPage('chat',document.querySelector('nav button:nth-child(2)'));
    };
    groupList.appendChild(d);
  });
});

window.createGroup=()=>{
  const g=document.getElementById("groupInput").value.trim();
  if(!g){alert("Enter group name");return;}
  set(ref(db,"groups/"+g),{createdBy:currentUser});
  document.getElementById("groupInput").value='';
};

function loadChat(){
  if(!curChat) return;
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  onValue(ref(db,path),snap=>{
    chatBox.innerHTML="";
    snap.forEach(m=>{
      const div=document.createElement("div");
      div.className="msg";
      let actions=`<div class="actions"><span onclick="deleteMsg('${path}','${m.key}')">🗑️</span></div>`;
      let commentsHTML='<div class="comments">'+(m.val().comments?Object.values(m.val().comments).map(c=>`<div><b>${c.from}:</b> ${c.text}</div>`).join(''):'')+'</div>';
      let commentInput=`<div class="comment-input"><input placeholder="Comment"/><button onclick="addComment('${path}','${m.key}',this)">💬</button></div>`;
      div.innerHTML=`<b>${m.val().from}:</b> ${m.val().text} ${actions} ${commentsHTML} ${commentInput}`;
      chatBox.appendChild(div);
      chatBox.scrollTop=chatBox.scrollHeight;
    });
  });
}

window.sendMsg=()=>{
  if(!curChat) return alert("Select a user/group first");
  const input=document.getElementById('msgInput');
  if(!input.value) return;
  const path=(isGroup?"groupChats/":"chats/")+ [currentUser,curChat].sort().join("_");
  push(ref(db,path),{from:currentUser,text:input.value});
  input.value='';
};

window.deleteMsg=(path,key)=>{remove(ref(db,path+"/"+key));};
window.addComment=(path,key,btn)=>{
  const input=btn.previousElementSibling;
  if(!input.value) return;
  push(ref(db,path+"/"+key+"/comments"),{from:currentUser,text:input.value});
  input.value='';
};
</script>
</body>
</html>
