<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
<style>
body, html{margin:0;padding:0;font-family:'Roboto',sans-serif;background:#f0f0f0;color:#000;height:100vh;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input,textarea{border:1px solid #ccc;border-radius:8px;padding:8px;width:100%;box-sizing:border-box;}
.navbar{display:flex;justify-content:space-between;align-items:center;padding:10px 15px;background:#075e54;color:#fff;}
.navbar h2{margin:0;font-size:20px;}
.navbar .dark-mode-btn{background:#25d366;color:#000;padding:5px 10px;border-radius:5px;}
.container{display:flex;height:calc(100vh - 50px);}
.sidebar{width:250px;background:#ededed;border-right:1px solid #ccc;display:flex;flex-direction:column;}
.sidebar h3{text-align:center;padding:10px 0;border-bottom:1px solid #ccc;margin:0;}
.sidebar .users-list,.sidebar .groups-list{flex:1;overflow-y:auto;}
.sidebar .user-item{padding:10px;cursor:pointer;border-bottom:1px solid #ddd;display:flex;justify-content:space-between;align-items:center;}
.sidebar .user-item:hover{background:#ccc;}
.chat-area{flex:1;display:flex;flex-direction:column;justify-content:flex-end;position:relative;background:#e5ddd5;}
.chat-messages{flex:1;padding:10px;overflow-y:auto;display:flex;flex-direction:column;gap:8px;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;position:relative;}
.message.self{align-self:flex-end;background:#dcf8c6;}
.message.other{align-self:flex-start;background:#fff;}
.timestamp{font-size:10px;color:#555;margin-top:4px;text-align:right;}
.chat-input{display:flex;padding:10px;gap:5px;border-top:1px solid #ccc;}
.chat-input input{flex:1;border-radius:20px;padding:8px;}
.chat-input button{background:#075e54;color:#fff;padding:8px 12px;border-radius:20px;font-weight:bold;}
#login-screen{display:flex;flex-direction:column;justify-content:center;align-items:center;height:100vh;}
#login-screen input{margin-bottom:10px;padding:10px;width:250px;}
#login-screen button{padding:8px 20px;background:#075e54;color:#fff;}
.dark-mode{background:#121212;color:#eee;}
.dark-mode .sidebar{background:#1f1f1f;border-right:1px solid #333;}
.dark-mode .sidebar .user-item{border-bottom:1px solid #333;color:#eee;}
.dark-mode .chat-area{background:#222;}
.dark-mode .chat-input input{background:#333;color:#eee;border:1px solid #0ff;}
.dark-mode .chat-input button{background:#0ff;color:#000;}
</style>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-storage-compat.js"></script>
</head>
<body>
<div id="login-screen">
<h2>LiveConnect</h2>
<input type="text" id="username" placeholder="Enter Your Name">
<button onclick="login()">Enter</button>
</div>
<div id="app-screen" style="display:none;height:100vh;flex-direction:column;">
<div class="navbar">
<h2>LiveConnect</h2>
<button class="dark-mode-btn" onclick="toggleDarkMode()">Dark/Light</button>
</div>
<div class="container">
<div class="sidebar">
<h3>Active Users (<span id="active-count">0</span>)</h3>
<div class="users-list" id="users-list"></div>
</div>
<div class="chat-area">
<div class="chat-messages" id="messages-container"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
<input type="file" id="media-input" style="display:none">
<button onclick="document.getElementById('media-input').click()">Media</button>
<audio id="notify-sound" src="https://www.myinstants.com/media/sounds/facebook_messenger.mp3" preload="auto"></audio>
</div>
</div>
</div>
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
const db=firebase.database();
const storage=firebase.storage();
let currentUser="",chatId="",typingTimeout;

// Login
function login(){
currentUser=document.getElementById("username").value.trim();
if(!currentUser){alert("Enter username");return;}
document.getElementById("login-screen").style.display="none";
document.getElementById("app-screen").style.display="flex";
db.ref("active_users/"+currentUser).set(true);
db.ref("active_users/"+currentUser).onDisconnect().remove();
loadUsers();
}

// Load Active Users
function loadUsers(){
db.ref("active_users").on("value",snap=>{
const users=snap.val()||{};
const usersList=document.getElementById("users-list");
usersList.innerHTML="";
let count=0;
for(let user in users){
if(user!==currentUser){
count++;
const div=document.createElement("div");
div.textContent=user;
div.className="user-item";
div.onclick=()=>openChat(user);
usersList.appendChild(div);
}}
document.getElementById("active-count").textContent=count;
});
}

// Open Chat
function openChat(user){
chatId=[currentUser,user].sort().join("_");
document.getElementById("messages-container").innerHTML="";
loadChat(chatId,user);
}

// Load Chat
function loadChat(id,user){
const msgContainer=document.getElementById("messages-container");
db.ref("chat/"+id).on("child_added",snap=>{
const msg=snap.val();
const div=document.createElement("div");
div.className="message "+(msg.user===currentUser?"self":"other");
div.innerHTML=`<strong>${msg.user}</strong>: ${msg.text || ""}${msg.media?`<br>${msg.media.endsWith('.mp4')?'<video controls src="'+msg.media+'"></video>':'<img src="'+msg.media+'">':''}<div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>`;
msgContainer.appendChild(div); msgContainer.scrollTop=msgContainer.scrollHeight;
if(msg.user!==currentUser)document.getElementById("notify-sound").play();
});
document.getElementById("message-input").oninput=()=>{
db.ref("typing/"+id+"/"+currentUser).set(true);
clearTimeout(typingTimeout);
typingTimeout=setTimeout(()=>{db.ref("typing/"+id+"/"+currentUser).remove();},2000);
};
document.getElementById("media-input").onchange=function(e){
const file=e.target.files[0]; if(!file) return;
const storageRef=storage.ref('chat_media/'+Date.now()+'_'+file.name);
storageRef.put(file).then(snap=>snap.ref.getDownloadURL().then(url=>sendMessage(url)));
};
}

// Send Message
function sendMessage(mediaUrl=null){
const text=document.getElementById("message-input").value.trim();
if(!text && !mediaUrl) return;
const msg={user:currentUser,text:text,media:mediaUrl||"",time:Date.now()};
db.ref("chat/"+chatId).push(msg);
document.getElementById("message-input").value="";
db.ref("typing/"+chatId+"/"+currentUser).remove();
}

// Dark Mode Toggle
function toggleDarkMode(){document.body.classList.toggle("dark-mode");}
</script>
</body>
</html>
