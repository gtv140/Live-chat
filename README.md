<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
body,html{margin:0;padding:0;font-family:'Segoe UI',sans-serif;background:#e5ddd5;color:#111;overflow:hidden;}
*{box-sizing:border-box;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input{padding:8px;border-radius:20px;border:1px solid #ccc;width:100%;}

/* Dark Mode */
body.dark{background:#121b22;color:#eee;}
body.dark input{border:1px solid #555;background:#1a1a1a;color:#eee;}

/* Layout */
#login-screen,#app-screen{display:flex;justify-content:center;align-items:center;height:100vh;}
#app-screen{display:none;flex-direction:column;}
.navbar{width:100%;background:#075e54;color:#fff;padding:10px;display:flex;justify-content:space-between;align-items:center;}
.navbar h2{margin:0;}
.main{display:flex;flex:1;width:100%;height:calc(100vh - 50px);}
.sidebar{width:250px;background:#128c7e;color:#fff;display:flex;flex-direction:column;}
.sidebar h3{padding:10px;margin:0;border-bottom:1px solid #075e54;}
.user{padding:10px;cursor:pointer;border-bottom:1px solid #075e54;}
.user:hover{background:#075e54;}
.content{flex:1;display:flex;flex-direction:column;background:#e5ddd5;}
.content.dark{background:#121b22;}
.chat-messages{flex:1;overflow-y:auto;padding:10px;display:flex;flex-direction:column;gap:5px;scroll-behavior:smooth;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;position:relative;}
.message.self{align-self:flex-end;background:#dcf8c6;}
.message.other{align-self:flex-start;background:#fff;}
.timestamp{font-size:10px;color:#888;margin-top:2px;text-align:right;}
.typing{font-size:12px;color:#555;margin:2px 10px;}
.chat-input{display:flex;padding:10px;border-top:1px solid #ccc;gap:5px;}
.chat-input input{flex:1;}
.chat-input button{background:#128c7e;color:#fff;padding:8px 12px;border-radius:20px;}
.toggle-mode{background:#25d366;color:#000;padding:5px 10px;border-radius:20px;}
.active-users-btn{background:#25d366;color:#000;margin:5px;padding:5px 10px;border-radius:20px;cursor:pointer;}
</style>
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, onValue, push, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let currentUser="",chatId="",typingTimeout;
const notifySound = new Audio("https://www.myinstants.com/media/sounds/facebook_messenger.mp3");

// Login
function loginUser(){
  const username = document.getElementById("username").value.trim();
  if(!username){alert("Enter username"); return;}
  currentUser = username;
  document.getElementById("login-screen").style.display="none";
  document.getElementById("app-screen").style.display="flex";

  // Active user
  set(ref(db,"active_users/"+currentUser),{online:true});
  onDisconnect(ref(db,"active_users/"+currentUser)).remove();

  loadActiveUsers();
}

// Load active users
function loadActiveUsers(){
  const list = document.getElementById("users-list");
  list.innerHTML="";
  onValue(ref(db,"active_users"),snap=>{
    const users=snap.val()||{};
    list.innerHTML="";
    let count=0;
    for(let u in users){
      if(u!==currentUser){
        count++;
        const div=document.createElement("div");
        div.className="user";
        div.textContent=u;
        div.onclick=()=>openChat(u);
        list.appendChild(div);
      }
    }
    document.getElementById("active-count").textContent=count;
  });
}

// Open chat
function openChat(user){
  chatId=[currentUser,user].sort().join("_");
  const container = document.getElementById("messages");
  container.innerHTML="";
  const input = document.getElementById("msg-input");
  const typingDiv = document.getElementById("typing-indicator");

  onValue(ref(db,"chat/"+chatId),snap=>{
    container.innerHTML="";
    const msgs=snap.val()||{};
    for(let mid in msgs){
      const m=msgs[mid];
      const div=document.createElement("div");
      div.className="message "+(m.user===currentUser?"self":"other");
      div.innerHTML=`<strong>${m.user}</strong>: ${m.text}<div class="timestamp">${new Date(m.time).toLocaleTimeString()}</div>`;
      container.appendChild(div);
      if(m.user!==currentUser) notifySound.play();
    }
    container.scrollTop=container.scrollHeight;
  });

  // Typing indicator
  input.oninput=()=>{
    set(ref(db,"typing/"+chatId+"/"+currentUser),true);
    clearTimeout(typingTimeout);
    typingTimeout=setTimeout(()=>{ remove(ref(db,"typing/"+chatId+"/"+currentUser)); },1500);
  };

  onValue(ref(db,"typing/"+chatId),snap=>{
    const typingUsers = snap.val()||{};
    const others = Object.keys(typingUsers).filter(u=>u!==currentUser);
    typingDiv.textContent = others.length>0 ? others.join(", ")+" is typing..." : "";
  });

  document.getElementById("send-btn").onclick=()=>{
    const text=input.value.trim();
    if(!text) return;
    push(ref(db,"chat/"+chatId),{user:currentUser,text:text,time:Date.now()});
    input.value="";
  };
}

// Dark mode
function toggleMode(){
  document.body.classList.toggle("dark");
}

</script>
</head>
<body>
<div id="login-screen">
  <div>
    <h2>LiveConnect</h2>
    <input type="text" id="username" placeholder="Enter your name"><br><br>
    <button onclick="loginUser()">Enter</button>
  </div>
</div>

<div id="app-screen">
  <div class="navbar">
    <h2>LiveConnect</h2>
    <button class="toggle-mode" onclick="toggleMode()">Dark/Light</button>
    <span class="active-users-btn">Active Users: <span id="active-count">0</span></span>
  </div>
  <div class="main">
    <div class="sidebar">
      <h3>Active Users</h3>
      <div id="users-list"></div>
    </div>
    <div class="content">
      <div class="chat-messages" id="messages"></div>
      <div class="typing" id="typing-indicator"></div>
      <div class="chat-input">
        <input type="text" id="msg-input" placeholder="Type a message...">
        <button id="send-btn">Send</button>
      </div>
    </div>
  </div>
</div>
</body>
</html>
