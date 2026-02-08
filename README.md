<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
<style>
body{margin:0;font-family:'Roboto',sans-serif;background:#e5ddd5;}
.login-screen, .app-screen{display:flex;flex-direction:column;align-items:center;justify-content:center;height:100vh;}
.login-screen input{padding:12px;margin:10px 0;width:250px;border-radius:25px;border:1px solid #ccc;}
.login-screen button{padding:12px 25px;border-radius:25px;background:#128C7E;color:white;border:none;cursor:pointer;font-weight:bold;}
.app-screen{display:none;flex-direction:column;width:100%;height:100vh;}

/* Layout */
.chat-container{display:flex;flex:1;width:100%;height:100vh;overflow:hidden;}
.users-list{width:25%;min-width:200px;background:#075E54;color:white;overflow-y:auto;padding:10px;border-right:1px solid #ccc;}
.users-list h3{margin-top:0;text-align:center;}
.users-list .user{padding:12px;margin:6px 0;background:#128C7E;border-radius:15px;cursor:pointer;transition:0.3s;}
.users-list .user:hover{background:#25D366;}
.users-list .user.active{background:#25D366;font-weight:bold;}
.chat-area{flex:1;display:flex;flex-direction:column;}
.chat-header{background:#075E54;color:white;padding:15px;font-weight:bold;font-size:18px;display:flex;justify-content:space-between;align-items:center;}
.messages{flex:1;overflow-y:auto;padding:10px;background:#e5ddd5;scroll-behavior:smooth;}
.message{margin:6px 0;padding:10px 15px;border-radius:25px;max-width:70%;word-wrap:break-word;position:relative;font-size:14px;line-height:1.3;}
.message.self{background:#DCF8C6;align-self:flex-end;}
.message.other{background:white;align-self:flex-start;}
.timestamp{font-size:10px;color:#999;margin-top:5px;text-align:right;}
.chat-input{display:flex;padding:10px;border-top:1px solid #ccc;gap:5px;background:#f0f0f0;}
.chat-input input{flex:1;padding:12px;border-radius:25px;border:1px solid #ccc;font-size:14px;}
.chat-input button{padding:12px 20px;border-radius:25px;background:#128C7E;color:white;border:none;cursor:pointer;font-weight:bold;}
.typing-indicator{font-size:12px;color:#555;margin:5px 10px;}
.dark-mode body{background:#111;}
.dark-mode .messages{background:#222;}
.dark-mode .chat-header{background:#333;color:#0ff;}
.dark-mode .chat-input{background:#222;}
.dark-mode .chat-input input{background:#111;color:#eee;border:1px solid #0ff;}
@media(max-width:768px){
  .chat-container{flex-direction:column;}
  .users-list{width:100%;height:150px;display:flex;overflow-x:auto;overflow-y:hidden;white-space:nowrap;}
  .users-list .user{display:inline-block;margin:5px 5px;width:auto;}
  .chat-area{flex:1;}
}
</style>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
</head>
<body>

<!-- Login -->
<div class="login-screen" id="login-screen">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter Your Name">
<button onclick="login()">Login</button>
</div>

<!-- App -->
<div class="app-screen" id="app-screen">
<div class="chat-container">
<div class="users-list" id="users-list">
<h3>Active Users</h3>
<div id="active-users"></div>
</div>
<div class="chat-area">
<div class="chat-header" id="chat-header">Select a user to chat</div>
<div class="messages" id="messages"></div>
<div class="typing-indicator" id="typing"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
</div>
</div>
</div>
<button onclick="toggleDark()">Toggle Dark/Light Mode</button>
</div>

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

let currentUser = "";
let chatWith = "";
let typingTimeout;

// Login
function login(){
  const username = document.getElementById("username").value.trim();
  if(!username){alert("Enter username");return;}
  currentUser = username;
  document.getElementById("login-screen").style.display="none";
  document.getElementById("app-screen").style.display="flex";
  db.ref("active_users/"+currentUser).set({online:true});
  db.ref("active_users/"+currentUser).onDisconnect().remove();
  loadUsers();
}

// Load Active Users
function loadUsers(){
  const usersDiv = document.getElementById("active-users");
  db.ref("active_users").on("value", snapshot=>{
    usersDiv.innerHTML="";
    const users = snapshot.val()||{};
    for(let user in users){
      if(user!==currentUser){
        const div = document.createElement("div");
        div.textContent = user;
        div.className = "user";
        div.onclick = ()=>openChat(user);
        usersDiv.appendChild(div);
      }
    }
  });
}

// Open Chat
function openChat(user){
  chatWith = user;
  document.getElementById("chat-header").textContent = "Chat with " + user;
  document.getElementById("messages").innerHTML = "";
  db.ref("chat/"+getChatId(currentUser,chatWith)).off();
  db.ref("chat/"+getChatId(currentUser,chatWith)).on("child_added", snap=>{
    const msg = snap.val();
    const div = document.createElement("div");
    div.className = "message " + (msg.user===currentUser?"self":"other");
    div.innerHTML = `<strong>${msg.user}</strong>: ${msg.text}<div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>`;
    document.getElementById("messages").appendChild(div);
    document.getElementById("messages").scrollTop = document.getElementById("messages").scrollHeight;
  });
}

// Get Chat ID
function getChatId(u1,u2){
  return [u1,u2].sort().join("_");
}

// Send Message
function sendMessage(){
  const text = document.getElementById("message-input").value.trim();
  if(!text || !chatWith) return;
  const msgData = {user: currentUser, text: text, time: Date.now()};
  db.ref("chat/"+getChatId(currentUser,chatWith)).push(msgData);
  document.getElementById("message-input").value="";
}

// Dark Mode
function toggleDark(){
  document.body.classList.toggle("dark-mode");
}
</script>
</body>
</html>
