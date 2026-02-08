<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap" rel="stylesheet">
<style>
body{margin:0;font-family:'Roboto',sans-serif;background:#f5f5f5;color:#111;transition:0.3s;}
.dark-mode{background:#111;color:#eee;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input{border:1px solid #ccc;border-radius:8px;padding:8px;width:100%;box-sizing:border-box;}
a{text-decoration:none;color:#0ff;}

/* Layout */
#login{display:flex;flex-direction:column;align-items:center;justify-content:center;height:100vh;}
#app{display:none;height:100vh;flex-direction:column;}

/* Navbar */
.navbar{display:flex;justify-content:space-between;align-items:center;padding:10px 15px;background:#0d6efd;color:#fff;}
.navbar h2{margin:0;font-size:20px;}
.navbar button{background:#fff;color:#0d6efd;padding:5px 10px;font-weight:bold;}

/* Main */
.main{display:flex;flex:1;overflow:hidden;}
.sidebar{width:250px;background:#e9ecef;overflow-y:auto;display:flex;flex-direction:column;}
.sidebar h3{padding:10px;text-align:center;margin:0;background:#dee2e6;}
.user{padding:10px 15px;cursor:pointer;border-bottom:1px solid #ccc;}
.user.online::after{content:"•";color:green;margin-left:5px;}
.chat-area{flex:1;display:flex;flex-direction:column;}

/* Messages */
.messages{flex:1;overflow-y:auto;padding:10px;display:flex;flex-direction:column;gap:5px;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;position:relative;}
.message.self{align-self:flex-end;background:#0d6efd;color:#fff;}
.message.other{align-self:flex-start;background:#e9ecef;color:#111;}
.timestamp{font-size:10px;color:#666;margin-top:2px;text-align:right;}

/* Input */
.input-area{display:flex;padding:10px;border-top:1px solid #ccc;gap:5px;}
.input-area input{flex:1;padding:8px;border-radius:20px;border:1px solid #ccc;}
.input-area button{background:#0d6efd;color:#fff;padding:8px 15px;border-radius:20px;font-weight:bold;}

/* Dark Mode */
.dark-mode .navbar{background:#222;}
.dark-mode .sidebar{background:#333;color:#eee;}
.dark-mode .user{border-bottom:1px solid #555;}
.dark-mode .chat-area{background:#111;}
.dark-mode .message.self{background:#0d6efd;color:#fff;}
.dark-mode .message.other{background:#333;color:#eee;}
.dark-mode input{background:#222;color:#eee;border:1px solid #555;}
</style>
</head>
<body>

<!-- Login -->
<div id="login">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter your username">
<br>
<button onclick="login()">Login</button>
</div>

<!-- App -->
<div id="app">
<div class="navbar">
<h2>LiveConnect</h2>
<button onclick="toggleDarkMode()">Toggle Dark/Light</button>
</div>
<div class="main">
<div class="sidebar">
<h3>Active Users (<span id="active-count">0</span>)</h3>
<div id="users-list"></div>
</div>
<div class="chat-area">
<div class="messages" id="messages"></div>
<div class="input-area">
<input type="text" id="message-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
</div>
</div>
</div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, onValue, push, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

// Login
function login(){
  currentUser = document.getElementById("username").value.trim();
  if(!currentUser){ alert("Enter username"); return; }
  
  document.getElementById("login").style.display="none";
  document.getElementById("app").style.display="flex";
  
  // Add to active users
  set(ref(db, "activeUsers/" + currentUser), {online:true});
  onDisconnect(ref(db, "activeUsers/" + currentUser)).remove();
  
  loadUsers();
}

// Load Active Users
function loadUsers(){
  const usersList = document.getElementById("users-list");
  const activeCount = document.getElementById("active-count");
  onValue(ref(db, "activeUsers"), snapshot=>{
    usersList.innerHTML="";
    const users = snapshot.val()||{};
    activeCount.textContent = Object.keys(users).length;
    for(let user in users){
      if(user !== currentUser){
        const div = document.createElement("div");
        div.textContent = user;
        div.className = "user online";
        div.onclick = ()=>openChat(user);
        usersList.appendChild(div);
      }
    }
  });
}

// Open chat with user
function openChat(user){
  chatWith = user;
  document.getElementById("messages").innerHTML="";
  const chatId = [currentUser,user].sort().join("_");
  const messagesRef = ref(db,"chats/"+chatId);
  onValue(messagesRef,snap=>{
    const messages = snap.val()||{};
    const container = document.getElementById("messages");
    container.innerHTML="";
    for(let key in messages){
      const msg = messages[key];
      const div = document.createElement("div");
      div.className = "message "+(msg.sender===currentUser?"self":"other");
      div.innerHTML = `<strong>${msg.sender}</strong>: ${msg.text}<div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>`;
      container.appendChild(div);
    }
    container.scrollTop = container.scrollHeight;
  });
}

// Send message
function sendMessage(){
  const text = document.getElementById("message-input").value.trim();
  if(!text || !chatWith) return;
  const chatId = [currentUser,chatWith].sort().join("_");
  const messagesRef = ref(db,"chats/"+chatId);
  push(messagesRef, {sender: currentUser, text:text, time:Date.now()});
  document.getElementById("message-input").value="";
}

// Dark mode toggle
function toggleDarkMode(){document.body.classList.toggle("dark-mode");}

</script>
</body>
</html>
