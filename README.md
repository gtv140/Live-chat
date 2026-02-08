<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
<style>
body {margin:0;font-family:'Segoe UI',Roboto,sans-serif;background:#f0f2f5;color:#111;transition:0.3s;}
.dark-mode body {background:#121212;color:#eee;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input,textarea{border:1px solid #ccc;border-radius:8px;padding:8px;width:100%;box-sizing:border-box;background:#fff;color:#111;}
.dark-mode input,.dark-mode textarea{background:#1f1f1f;color:#eee;border:1px solid #555;}
a{text-decoration:none;color:#0d6efd;}

/* Navbar */
.navbar{display:flex;justify-content:space-between;align-items:center;padding:10px 15px;background:#0d6efd;color:#fff;}
.dark-mode .navbar{background:#0a4c8c;}
.navbar h2{margin:0;}
.tabs{display:flex;gap:10px;}
.tabs button{padding:6px 12px;background:#fff;color:#0d6efd;}
.dark-mode .tabs button{background:#1f1f1f;color:#0d6efd;}
.tabs button.active{background:#0b5ed7;color:#fff;}
.navbar .right{display:flex;align-items:center;gap:10px;}
.navbar .toggle-theme{padding:5px 10px;background:#fff;color:#0d6efd;}
.dark-mode .toggle-theme{background:#1f1f1f;color:#0d6efd;}

/* Layout */
.container{display:flex;height:calc(100vh - 50px);}
.sidebar{width:250px;background:#e4e6eb;border-right:1px solid #ccc;overflow-y:auto;display:flex;flex-direction:column;}
.dark-mode .sidebar{background:#1e1e1e;border-color:#333;}
.sidebar h3{padding:10px;margin:0;border-bottom:1px solid #ccc;}
.dark-mode .sidebar h3{border-color:#333;}
.user{padding:10px;display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid #ccc;cursor:pointer;}
.dark-mode .user{border-color:#333;}
.user.online::after{content:'•';color:green;margin-left:5px;font-weight:bold;}
.content{flex:1;display:flex;flex-direction:column;}
.chat-header{padding:10px;border-bottom:1px solid #ccc;display:flex;align-items:center;justify-content:space-between;}
.dark-mode .chat-header{border-color:#333;}
.chat-messages{flex:1;overflow-y:auto;padding:10px;background:#f7f7f7;}
.dark-mode .chat-messages{background:#121212;}
.message{max-width:70%;padding:10px 14px;margin:5px 0;border-radius:18px;word-wrap:break-word;position:relative;box-shadow:0 1px 2px rgba(0,0,0,0.2);}
.message.self{align-self:flex-end;background:#0d6efd;color:#fff;}
.message.other{align-self:flex-start;background:#e4e6eb;color:#111;}
.dark-mode .message.other{background:#2a2a2a;color:#eee;}
.timestamp{font-size:10px;color:#555;margin-top:2px;text-align:right;}
.chat-input{display:flex;padding:10px;gap:5px;border-top:1px solid #ccc;}
.dark-mode .chat-input{border-color:#333;}
.chat-input input{flex:1;border-radius:20px;padding:8px;border:1px solid #ccc;}
.dark-mode .chat-input input{border-color:#555;background:#1f1f1f;color:#eee;}
.chat-input button{padding:6px 12px;background:#0d6efd;color:#fff;border-radius:20px;}

/* Profile & Info */
.profile{display:flex;align-items:center;gap:10px;}
.profile img{width:40px;height:40px;border-radius:50%;}
.welcome{padding:10px;font-size:14px;color:#555;}
.dark-mode .welcome{color:#aaa;}

/* Responsive */
@media(max-width:900px){.container{flex-direction:column;}.sidebar{width:100%;height:auto;}}
</style>
</head>
<body>

<!-- Navbar -->
<div class="navbar">
<h2>LiveConnect</h2>
<div class="right">
<button class="toggle-theme" onclick="toggleTheme()">Dark/Light</button>
</div>
</div>

<!-- Main Layout -->
<div class="welcome" id="welcome"></div>
<div class="container">
<div class="sidebar">
<h3>Active Users <span id="user-count">0</span></h3>
<div id="users-list"></div>
<h3>About Company</h3>
<div style="padding:10px;font-size:13px;">
<p>LiveConnect is a modern chat platform to connect with friends in realtime. Enjoy private chats, profile pics, and a smooth WhatsApp-like experience.</p>
</div>
<h3>Contact Support</h3>
<div style="padding:10px;font-size:13px;"><p>Email: support@liveconnect.com</p></div>
</div>

<div class="content">
<div class="chat-header">
<div class="profile">
<img src="https://cdn-icons-png.flaticon.com/512/149/149071.png" id="chat-user-pic">
<strong id="chat-username">Select a user</strong>
</div>
<div id="typing-indicator" style="font-size:12px;color:#0d6efd"></div>
</div>

<div class="chat-messages" id="chat-messages"></div>

<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
</div>
</div>
</div>

<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

<script>
// Firebase Config
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

let currentUser = prompt("Enter your username:");
document.getElementById('welcome').innerHTML = `Welcome, <strong>${currentUser}</strong>!`;
db.ref("active_users/"+currentUser).set({online:true});
db.ref("active_users/"+currentUser).onDisconnect().remove();

// Load active users
function loadUsers(){
  db.ref("active_users").on("value", snap=>{
    const users = snap.val() || {};
    const ul = document.getElementById('users-list');
    ul.innerHTML = "";
    let count=0;
    for(let u in users){
      if(u!==currentUser){
        count++;
        let div = document.createElement('div');
        div.className="user online";
        div.textContent = u;
        div.onclick = ()=>openChat(u);
        ul.appendChild(div);
      }
    }
    document.getElementById('user-count').textContent=count;
  });
}
loadUsers();

// Chat
let chatId = "";
let typingTimeout;

function getChatId(u1,u2){ return [u1,u2].sort().join("_"); }
function openChat(user){
chatId = getChatId(currentUser,user);
document.getElementById('chat-username').textContent = user;
document.getElementById('chat-messages').innerHTML = "";
loadChat();
}

function loadChat(){
const msgContainer = document.getElementById('chat-messages');
db.ref("chat/"+chatId).on("child_added", snap=>{
  const msg = snap.val();
  if([msg.user,msg.to].includes(currentUser)){
    const div = document.createElement('div');
    div.className = "message "+(msg.user===currentUser?"self":"other");
    div.textContent = msg.text;
    msgContainer.appendChild(div);
    msgContainer.scrollTop = msgContainer.scrollHeight;
  }
});

db.ref("typing/"+chatId).on("value",snap=>{
const data = snap.val()||{};
const typingUsers = Object.keys(data).filter(u=>u!==currentUser);
document.getElementById('typing-indicator').textContent = typingUsers.length>0?typingUsers.join(", ")+" is typing...":"";
});

document.getElementById('message-input').oninput = ()=>{
db.ref("typing/"+chatId+"/"+currentUser).set(true);
clearTimeout(typingTimeout);
typingTimeout = setTimeout(()=>db.ref("typing/"+chatId+"/"+currentUser).remove(),2000);
};
}

function sendMessage(){
const text = document.getElementById('message-input').value.trim();
if(!text) return;
db.ref("chat/"+chatId).push({user:currentUser,to:chatId.replace(currentUser+"_","").replace("_"+currentUser,""),text:text,time:Date.now()});
document.getElementById('message-input').value="";
db.ref("typing/"+chatId+"/"+currentUser).remove();
}

// Theme
function toggleTheme(){document.body.classList.toggle("dark-mode");}
</script>

</body>
</html>
