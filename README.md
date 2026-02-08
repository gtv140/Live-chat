<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
<style>
body{margin:0;font-family:Segoe UI,Arial,sans-serif;background:#f0f0f0;color:#111;transition:0.3s;}
.dark-mode{background:#111;color:#eee;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input{border:1px solid #ccc;border-radius:20px;padding:8px;width:100%;box-sizing:border-box;}
a{text-decoration:none;color:#0ff;}

/* Navbar */
.navbar{display:flex;justify-content:space-between;align-items:center;padding:10px 15px;background:#0d6efd;color:#fff;}
.navbar h2{margin:0;font-size:20px;}
.navbar button{background:#fff;color:#0d6efd;font-weight:bold;}

/* Layout */
.container{display:flex;flex-direction:row;height:calc(100vh - 50px);}
.sidebar{width:250px;background:#f8f9fa;border-right:1px solid #ccc;overflow-y:auto;display:none;flex-direction:column;}
.sidebar.dark-mode{background:#222;border-color:#444;color:#eee;}
.sidebar h3{margin:10px;text-align:center;}
.sidebar .user{padding:10px;display:flex;align-items:center;gap:10px;cursor:pointer;border-bottom:1px solid #ddd;}
.sidebar .user img{width:35px;height:35px;border-radius:50%;object-fit:cover;}
.sidebar .user.online::after{content:"•";color:green;margin-left:5px;}
.content{flex:1;display:flex;flex-direction:column;}
.header{padding:10px;background:#e9ecef;}
.header.dark-mode{background:#1a1a1a;}
.chat-area{flex:1;overflow-y:auto;padding:10px;display:flex;flex-direction:column;gap:5px;}
.message{max-width:70%;padding:10px 15px;border-radius:20px;word-wrap:break-word;position:relative;display:flex;align-items:flex-end;gap:10px;}
.message.self{align-self:flex-end;background:#0d6efd;color:#fff;}
.message.other{align-self:flex-start;background:#e0e0e0;color:#111;}
.message img.profile{width:30px;height:30px;border-radius:50%;object-fit:cover;}
.timestamp{font-size:10px;color:#666;align-self:flex-end;}
.chat-input{display:flex;padding:10px;gap:5px;border-top:1px solid #ccc;}
.chat-input input{flex:1;border-radius:20px;padding:8px;border:1px solid #ccc;}
.chat-input button{background:#0d6efd;color:#fff;font-weight:bold;border-radius:20px;padding:8px 12px;}

/* Login */
#login{display:flex;flex-direction:column;align-items:center;justify-content:center;height:100vh;gap:10px;}
#login input{width:250px;}
#login button{padding:10px 20px;background:#0d6efd;color:#fff;font-weight:bold;border-radius:20px;}

/* Typing indicator */
#typing{font-size:12px;color:green;margin:2px 10px;}

/* Responsive */
@media(max-width:768px){.container{flex-direction:column;}.sidebar{width:100%;height:auto;}}
</style>
</head>
<body>

<div id="login">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter your username">
<button onclick="login()">Enter</button>
</div>

<div id="app" style="display:none;flex-direction:column;height:100vh;">
<div class="navbar">
<h2>LiveConnect</h2>
<button onclick="toggleDark()">Dark/Light</button>
</div>

<div class="container">
<div class="sidebar" id="usersSidebar">
<h3>Active Users (<span id="activeCount">0</span>)</h3>
<div id="userList"></div>
</div>

<div class="content">
<div class="header" id="chatHeader">Select a user to chat</div>
<div class="chat-area" id="chatArea"></div>
<div id="typing"></div>
<div class="chat-input">
<input type="text" id="messageInput" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
</div>
</div>
</div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, onValue, push, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let currentUser="",currentChat="";
let typingTimeout;

// Login
function login(){
currentUser=document.getElementById("username").value.trim();
if(!currentUser){alert("Enter username");return;}
document.getElementById("login").style.display="none";
document.getElementById("app").style.display="flex";
set(ref(db,"activeUsers/"+currentUser),{online:true});
onDisconnect(ref(db,"activeUsers/"+currentUser)).remove();
loadUsers();
}

// Load users
function loadUsers(){
const userList=document.getElementById("userList");
onValue(ref(db,"activeUsers"),snap=>{
userList.innerHTML="";
const users=snap.val()||{};
document.getElementById("activeCount").textContent=Object.keys(users).length;
for(let u in users){
if(u!==currentUser){
const div=document.createElement("div");
div.className="user online";
div.innerHTML=`<img class="profile" src="https://i.pravatar.cc/40?u=${u}"> ${u}`;
div.onclick=()=>openChat(u);
userList.appendChild(div);
}
}
});
}

// Open chat
function openChat(user){
currentChat=user;
document.getElementById("chatHeader").textContent="Chat with "+user;
document.getElementById("chatArea").innerHTML="";
listenMessages();
}

// Listen messages
function listenMessages(){
const chatArea=document.getElementById("chatArea");
const chatRef=ref(db,"messages");
onValue(chatRef,snap=>{
chatArea.innerHTML="";
const msgs=snap.val()||{};
for(let id in msgs){
const m=msgs[id];
if((m.from===currentUser && m.to===currentChat)||(m.from===currentChat && m.to===currentUser)){
const div=document.createElement("div");
div.className="message "+(m.from===currentUser?"self":"other");
div.innerHTML=`${m.from!==currentUser?'<img class="profile" src="https://i.pravatar.cc/40?u='+m.from+'">':''} <span>${m.text}</span> <div class="timestamp">${new Date(m.time).toLocaleTimeString()}</div>`;
chatArea.appendChild(div);
chatArea.scrollTop=chatArea.scrollHeight;
}
}
});
}

// Send message
function sendMessage(){
const text=document.getElementById("messageInput").value.trim();
if(!text) return;
const msgRef=push(ref(db,"messages"));
set(msgRef,{from:currentUser,to:currentChat,text:text,time:Date.now()});
document.getElementById("messageInput").value="";
}

// Dark/Light toggle
function toggleDark(){
document.body.classList.toggle("dark-mode");
document.querySelector(".sidebar").classList.toggle("dark-mode");
document.querySelector(".header").classList.toggle("dark-mode");
}
</script>
</body>
</html>
