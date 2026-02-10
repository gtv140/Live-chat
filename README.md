<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Live Connect 🚀</title>

<!-- Icons -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css"/>

<!-- Firebase SDKs -->
<script src="https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js"></script>

<style>
:root{
  --bg:#f6f7fb;
  --card:#fff;
  --text:#111827;
  --muted:#6b7280;
  --primary:#2563eb;
}
body.dark{
  --bg:#0f172a;
  --card:#111827;
  --text:#e5e7eb;
  --muted:#9ca3af;
  --primary:#3b82f6;
}
*{box-sizing:border-box}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);}
.app{max-width:420px;margin:auto;min-height:100vh;display:flex;flex-direction:column;}
header{padding:14px 16px;display:flex;justify-content:space-between;align-items:center;background:var(--card);box-shadow:0 2px 10px rgba(0,0,0,.06);}
header h1{font-size:18px;margin:0;}
header button{background:none;border:none;font-size:18px;color:var(--text);}
.page{display:none;padding:16px;flex:1;}
.page.active{display:block;}
.hero{background:linear-gradient(135deg,var(--primary),#60a5fa);color:#fff;padding:24px;border-radius:18px;text-align:center;}
.hero h2{margin:0 0 8px;}
.hero p{margin:0;opacity:.9;}
.login-box{background:var(--card);padding:20px;border-radius:16px;box-shadow:0 2px 10px rgba(0,0,0,.1);text-align:center;}
.login-box input{width:100%;padding:12px;margin:10px 0;border-radius:10px;border:1px solid #ccc;font-size:14px;}
.login-box button{padding:12px 16px;border:none;border-radius:10px;background:var(--primary);color:#fff;font-size:16px;cursor:pointer;}
.chat-box{background:var(--card);border-radius:16px;padding:12px;height:45vh;overflow-y:auto;margin-bottom:10px;}
.msg{background:#e5e7eb;color:#000;padding:8px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;position:relative;}
body.dark .msg{background:#1f2937;color:#e5e7eb;}
.msg .actions{position:absolute;right:6px;top:6px;font-size:12px;}
.input-row{display:flex;gap:8px;margin-bottom:10px;}
.input-row input{flex:1;padding:12px;border-radius:12px;border:1px solid #ccc;}
.input-row button{padding:12px 16px;border:none;border-radius:12px;background:var(--primary);color:#fff;}
.user{display:flex;align-items:center;gap:8px;margin-bottom:10px;cursor:pointer;}
.dot{width:10px;height:10px;background:#22c55e;border-radius:50%;}
nav{display:flex;justify-content:space-around;background:var(--card);padding:10px 0;box-shadow:0 -2px 10px rgba(0,0,0,.06);}
nav button{background:none;border:none;font-size:20px;color:var(--muted);}
nav button.active{color:var(--primary);}
</style>
</head>

<body>
<div class="app">

<header>
  <h1>Live Connect 🚀</h1>
  <button id="darkBtn"><i class="fa-solid fa-moon"></i></button>
</header>

<!-- LOGIN -->
<div id="login" class="page active">
  <div class="login-box">
    <h2>Welcome!</h2>
    <p>Enter your username to continue</p>
    <input type="text" id="usernameInput" placeholder="Username">
    <button id="loginBtn">Continue</button>
  </div>
</div>

<!-- HOME -->
<div id="home" class="page">
  <div class="hero">
    <h2>Real-Time Live Chat</h2>
    <p>Fast • Secure • Mobile Friendly</p>
  </div>
  <p style="margin-top:16px;color:var(--muted)">Connect instantly, see who’s online, and chat smoothly on mobile.</p>
</div>

<!-- CHAT -->
<div id="chat" class="page">
  <div class="chat-box" id="chatBox"></div>
  <div class="input-row">
    <input id="msgInput" placeholder="Type message…" />
    <button id="sendBtn"><i class="fa-solid fa-paper-plane"></i></button>
  </div>
</div>

<!-- USERS -->
<div id="users" class="page">
  <h3>Online Users</h3>
  <div id="currentUsers"></div>
</div>

<!-- ABOUT -->
<div id="about" class="page">
  <h3>About</h3>
  <p>
    Live Connect is a modern real-time chat platform for mobile users. Chat in groups, see who is online, like, comment, and manage your profile easily.
  </p>
</div>

<!-- CONTACT -->
<div id="contact" class="page">
  <h3>Contact & Support</h3>
  <p><i class="fa-solid fa-envelope"></i> webhub262@gmail.com</p>
  <p>
    <a href="https://www.facebook.com/profile.php?id=100084218946114">Facebook</a> |
    <a href="https://www.instagram.com/mr_nazim073">Instagram</a> |
    <a href="https://youtube.com/@crazykhantv">YouTube</a>
  </p>
</div>

<nav>
  <button data-page="home" class="active"><i class="fa-solid fa-house"></i></button>
  <button data-page="chat"><i class="fa-solid fa-comments"></i></button>
  <button data-page="users"><i class="fa-solid fa-user-group"></i></button>
  <button data-page="about"><i class="fa-solid fa-circle-info"></i></button>
  <button data-page="contact"><i class="fa-solid fa-envelope"></i></button>
</nav>

</div>

<script>
const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.appspot.com",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();

let currentUser = null;
let curChat = "";

const loginPage = document.getElementById("login");
const homePage = document.getElementById("home");
const chatPage = document.getElementById("chat");
const usersPage = document.getElementById("users");

const currentUsersDiv = document.getElementById("currentUsers");
const chatBox = document.getElementById("chatBox");

document.getElementById("darkBtn").onclick = ()=>document.body.classList.toggle("dark");

document.getElementById("loginBtn").onclick = function(){
  const uname = document.getElementById("usernameInput").value.trim();
  if(!uname){alert("Enter username"); return;}
  currentUser = uname;
  firebase.database().ref("users/"+uname).set({name:uname,online:true});
  loginPage.classList.remove("active");
  homePage.classList.add("active");
  listenUsers();
  listenChats();
};

document.querySelectorAll('nav button').forEach(btn=>{
  btn.onclick = ()=>{
    document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
    const pageId = btn.dataset.page;
    document.getElementById(pageId).classList.add('active');
    document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');
  };
});

function listenUsers(){
  firebase.database().ref("users").on("value", snap=>{
    currentUsersDiv.innerHTML="";
    snap.forEach(u=>{
      if(u.val().online && u.key!==currentUser){
        const div=document.createElement("div");
        div.className="user";
        div.innerHTML=`<div class="dot"></div>${u.val().name}`;
        div.onclick=()=>{curChat=u.key; openPage('chat');};
        currentUsersDiv.appendChild(div);
      }
    });
  });
}

function sendMsg(){
  if(!curChat) return alert("Select a user first");
  const input = document.getElementById("msgInput");
  if(!input.value) return;
  const path = "chats/"+[currentUser,curChat].sort().join("_");
  firebase.database().ref(path).push({from:currentUser,text:input.value,likes:0});
  input.value="";
}

function listenChats(){
  setInterval(()=>{
    if(!curChat) return;
    const path = "chats/"+[currentUser,curChat].sort().join("_");
    firebase.database().ref(path).on("value", snap=>{
      chatBox.innerHTML="";
      snap.forEach(m=>{
        const div=document.createElement("div");
        div.className="msg";
        div.innerHTML=`<b>${m.val().from}:</b> ${m.val().text} <span class="actions"><span onclick="likeMsg('${path}','${m.key}')">👍</span><span onclick="deleteMsg('${path}','${m.key}')">🗑️</span></span>`;
        chatBox.appendChild(div);
        chatBox.scrollTop=chatBox.scrollHeight;
      });
    });
  },1000);
}

function openPage(pageId){document.getElementById(pageId).classList.add('active');}

function likeMsg(path,key){
  firebase.database().ref(path+"/"+key+"/likes").transaction(l=> (l||0)+1 );
}

function deleteMsg(path,key){
  firebase.database().ref(path+"/"+key).remove();
}
</script>
</body>
</html>
