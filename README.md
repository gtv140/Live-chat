<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
body{margin:0;font-family:Segoe UI,Roboto,sans-serif;background:#f5f5f5;color:#111;}
.dark-mode{background:#111;color:#eee;}
.navbar{background:#0d6efd;color:#fff;padding:10px;display:flex;justify-content:space-between;align-items:center;}
.navbar button{background:none;border:none;color:#fff;font-size:16px;cursor:pointer;}
.container{display:flex;height:calc(100vh - 50px);}
.sidebar{width:250px;background:#f1f1f1;border-right:1px solid #ccc;overflow-y:auto;padding:10px;}
.dark-mode .sidebar{background:#1a1a1a;}
.sidebar h3{margin-top:0;}
.user{padding:8px;margin:5px 0;border-radius:5px;cursor:pointer;background:#e0e0e0;}
.user:hover{background:#ccc;}
.dark-mode .user{background:#222;}
.dark-mode .user:hover{background:#333;}
.chat-area{flex:1;display:flex;flex-direction:column;}
.messages{flex:1;overflow-y:auto;padding:10px;}
.message{margin:5px 0;padding:8px 12px;border-radius:20px;max-width:60%;word-wrap:break-word;}
.self{background:#0d6efd;color:#fff;align-self:flex-end;}
.other{background:#e0e0e0;align-self:flex-start;}
.dark-mode .other{background:#222;color:#eee;}
.input-area{display:flex;padding:10px;border-top:1px solid #ccc;}
.input-area input{flex:1;padding:8px;border-radius:20px;border:1px solid #ccc;margin-right:5px;}
.input-area button{padding:8px 12px;border:none;border-radius:20px;background:#0d6efd;color:#fff;cursor:pointer;}
.login-screen{text-align:center;margin-top:100px;}
.active-users-count{font-size:12px;color:#555;}
.dark-mode .active-users-count{color:#aaa;}
</style>
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, onValue, onDisconnect, push } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

// Firebase config
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

let currentUser = "";
let chatWith = "";
let chatRef = null;

// Login function
window.login = function(){
  const username = document.getElementById("username").value.trim();
  if(!username) return alert("Enter username");
  currentUser = username;
  document.getElementById("login-screen").style.display = "none";
  document.getElementById("app-screen").style.display = "flex";

  const userRef = ref(db,"active_users/"+currentUser);
  set(userRef,{online:true});
  onDisconnect(userRef).remove();

  loadActiveUsers();
  setupChat();
};

// Load active users
function loadActiveUsers(){
  const usersList = document.getElementById("users-list");
  onValue(ref(db,"active_users"),snap=>{
    usersList.innerHTML = "";
    const users = snap.val()||{};
    let count = 0;
    for(let user in users){
      if(user !== currentUser){
        count++;
        const div = document.createElement("div");
        div.textContent = user;
        div.className = "user";
        div.onclick = ()=> openChat(user);
        usersList.appendChild(div);
      }
    }
    document.getElementById("active-count").textContent = `Active Users: ${count}`;
  });
}

// Open chat with user
function openChat(user){
  chatWith = user;
  chatRef = ref(db,"chats/"+[currentUser,user].sort().join("_"));
  document.getElementById("messages").innerHTML = "";
  onValue(chatRef,snap=>{
    const data = snap.val()||{};
    const messagesDiv = document.getElementById("messages");
    messagesDiv.innerHTML = "";
    for(let key in data){
      const m = data[key];
      const div = document.createElement("div");
      div.className = "message "+(m.sender===currentUser?"self":"other");
      div.textContent = m.sender+": "+m.text;
      messagesDiv.appendChild(div);
      messagesDiv.scrollTop = messagesDiv.scrollHeight;
    }
  });
}

// Send message
window.sendMessage = function(){
  const text = document.getElementById("msg-input").value.trim();
  if(!text || !chatRef) return;
  push(chatRef,{sender:currentUser,text:text,time:Date.now()});
  document.getElementById("msg-input").value = "";
}

// Dark/Light toggle
window.toggleDarkMode = function(){
  document.body.classList.toggle("dark-mode");
}

</script>
</head>
<body>

<!-- Login -->
<div id="login-screen" class="login-screen">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter your username"><br><br>
<button onclick="login()">Enter</button>
</div>

<!-- App -->
<div id="app-screen" style="display:none;flex-direction:column;">
<div class="navbar">
<span>LiveConnect | <span id="welcome-user"></span></span>
<button onclick="toggleDarkMode()">Toggle Dark/Light</button>
</div>

<div class="container">
<div class="sidebar">
<h3>Active Users <span class="active-users-count" id="active-count">Active Users: 0</span></h3>
<div id="users-list"></div>
</div>

<div class="chat-area">
<div class="messages" id="messages"></div>
<div class="input-area">
<input type="text" id="msg-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
</div>
</div>
</div>
</div>

</body>
</html>
