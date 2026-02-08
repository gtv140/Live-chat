<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap" rel="stylesheet">
<style>
body{margin:0;font-family:'Roboto',sans-serif;background:#f0f2f5;color:#000;transition:0.3s;}
.dark-mode{background:#121212;color:#eee;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input,textarea{border:1px solid #ccc;border-radius:20px;padding:8px;width:100%;box-sizing:border-box;}
a{text-decoration:none;color:#0af;}
/* Navbar */
.navbar{display:flex;justify-content:space-between;align-items:center;padding:10px 15px;background:#075e54;color:#fff;}
.navbar h2{margin:0;font-size:22px;}
.navbar .toggle-btn{background:#25d366;color:#fff;padding:6px 12px;cursor:pointer;border-radius:20px;}
/* Layout */
.container{display:flex;height:calc(100vh - 50px);}
.sidebar{width:280px;background:#f5f5f5;border-right:1px solid #ccc;overflow-y:auto;transition:0.3s;}
.dark-mode .sidebar{background:#1f1f1f;border-color:#333;}
.sidebar h3{padding:12px 15px;margin:0;border-bottom:1px solid #ccc;}
.user{padding:10px 15px;border-bottom:1px solid #ccc;display:flex;justify-content:space-between;align-items:center;cursor:pointer;}
.user.online::after{content:"●";color:#25d366;margin-left:5px;font-size:16px;}
.content{flex:1;display:flex;flex-direction:column;}
.chat-header{padding:10px 15px;border-bottom:1px solid #ccc;background:#eee;display:flex;justify-content:space-between;align-items:center;}
.dark-mode .chat-header{background:#222;border-color:#333;color:#eee;}
.chat-messages{flex:1;overflow-y:auto;padding:10px;display:flex;flex-direction:column;gap:5px;background:#fff;}
.dark-mode .chat-messages{background:#121212;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;position:relative;box-shadow:0 1px 2px rgba(0,0,0,0.2);}
.message.self{align-self:flex-end;background:#dcf8c6;}
.message.other{align-self:flex-start;background:#fff;}
.dark-mode .message.other{background:#2a2a2a;color:#eee;}
.timestamp{font-size:10px;color:#555;margin-top:2px;text-align:right;}
.chat-input{display:flex;padding:10px;border-top:1px solid #ccc;gap:5px;background:#f0f2f5;}
.dark-mode .chat-input{background:#1a1a1a;border-color:#333;}
.chat-input input{flex:1;border-radius:20px;padding:8px;}
.chat-input button{background:#25d366;color:#fff;padding:6px 12px;font-weight:bold;border-radius:20px;}
/* Responsive */
@media(max-width:900px){.container{flex-direction:column;height:auto;}.sidebar{width:100%;height:auto;}}
</style>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-storage-compat.js"></script>
</head>
<body>

<!-- Navbar -->
<div class="navbar">
<h2>LiveConnect</h2>
<button class="toggle-btn" onclick="toggleDarkMode()">🌙 / ☀️</button>
</div>

<!-- Login -->
<div id="login-screen" style="text-align:center;margin-top:50px;">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter your name"><br><br>
<button onclick="login()">Enter</button>
</div>

<!-- App -->
<div id="app-screen" style="display:none;flex-direction:column;height:calc(100vh - 50px);">
<div class="container">
<div class="sidebar">
<h3>Active Users <span id="active-count">0</span></h3>
<div id="users-list"></div>
</div>

<div class="content">
<div class="chat-header" id="chat-header">Select user to start chat</div>
<div class="chat-messages" id="messages-container"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
<input type="file" id="media-input" style="display:none">
<button onclick="document.getElementById('media-input').click()">📎</button>
<audio id="notify-sound" src="https://www.myinstants.com/media/sounds/facebook_messenger.mp3" preload="auto"></audio>
</div>
</div>
</div>
</div>

<script>
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
firebase.initializeApp(firebaseConfig);
const db=firebase.database();
const storage=firebase.storage();

let currentUser="",chatId="",typingTimeout;

// Login
function login(){
const username=document.getElementById("username").value.trim();
if(!username){alert("Enter name");return;}
currentUser=username;
document.getElementById("login-screen").style.display="none";
document.getElementById("app-screen").style.display="block";
db.ref("active_users/"+currentUser).set(true);
db.ref("active_users/"+currentUser).onDisconnect().remove();
loadActiveUsers();
}

// Dark/Light toggle
function toggleDarkMode(){document.body.classList.toggle("dark-mode");}

// Load Active Users
function loadActiveUsers(){
db.ref("active_users").on("value",snap=>{
const users=snap.val()||{};
const list=document.getElementById("users-list");
list.innerHTML="";
let count=0;
for(let user in users){if(user!==currentUser){
const div=document.createElement("div");div.className="user online";div.textContent=user;div.onclick=()=>openChat(user);list.appendChild(div);count++;}}
document.getElementById("active-count").textContent=count;
});
}

// Open Chat
function openChat(user){
chatId=[currentUser,user].sort().join("_");
document.getElementById("chat-header").textContent="Chat with "+user;
document.getElementById("messages-container").innerHTML="";
loadMessages();
}

// Load Messages
function loadMessages(){
const container=document.getElementById("messages-container");
db.ref("chat/"+chatId).off();
db.ref("chat/"+chatId).on("child_added",snap=>{
const msg=snap.val();
if(!([msg.user,msg.to].includes(currentUser))) return;
const div=document.createElement("div");
div.className="message "+(msg.user===currentUser?"self":"other");
div.innerHTML=`<strong>${msg.user}</strong>: ${msg.text||""}${msg.media?`<br>${msg.media.endsWith('.mp4')?'<video controls src="'+msg.media+'"></video>':'<img src="'+msg.media+'" style="max-width:200px;">':''}<div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>`;
container.appendChild(div);
container.scrollTop=container.scrollHeight;
if(msg.user!==currentUser)document.getElementById("notify-sound").play();
});
}

// Send Message
document.getElementById("media-input").onchange=function(e){
const file=e.target.files[0]; if(!file) return;
const ref=storage.ref('chat_media/'+Date.now()+'_'+file.name);
ref.put(file).then(snap=>snap.ref.getDownloadURL().then(url=>sendMessage(url)));
};
function sendMessage(mediaUrl=null){
const text=document.getElementById("message-input").value.trim();
if(!text && !mediaUrl) return;
const userTo=chatId.replace(currentUser+"_","").replace("_"+currentUser,"");
db.ref("chat/"+chatId).push({user:currentUser,to:userTo,text:text,media:mediaUrl,time:Date.now()});
document.getElementById("message-input").value="";
}

</script>
</body>
</html>
