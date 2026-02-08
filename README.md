<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
body{margin:0;font-family:Arial,sans-serif;background:#111;color:#eee;transition:0.3s;}
.dark-mode{background:#111;color:#eee;}
.light-mode{background:#f5f5f5;color:#111;}
button{cursor:pointer;border:none;padding:8px 12px;border-radius:8px;transition:0.2s;}
button:hover{opacity:0.8;}
input{padding:8px;border-radius:8px;border:1px solid #555;background:#1a1a1a;color:#eee;width:100%;box-sizing:border-box;}
.light-mode input{background:#fff;color:#111;border:1px solid #ccc;}
.navbar{display:flex;justify-content:space-between;padding:10px;background:#1f1f1f;align-items:center;}
.light-mode .navbar{background:#eee;color:#111;}
.tabs button{margin-right:5px;background:#333;color:#eee;}
.light-mode .tabs button{background:#ddd;color:#111;}
.tabs button.active{background:#0ff;color:#000;}
.chat-container{display:none;flex-direction:column;height:70vh;border:1px solid #333;margin-top:10px;overflow:hidden;}
.light-mode .chat-container{border:1px solid #aaa;}
.chat-messages{flex:1;overflow-y:auto;padding:10px;display:flex;flex-direction:column;gap:5px;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;}
.message.self{align-self:flex-end;background:#0ff;color:#000;}
.message.other{align-self:flex-start;background:#333;color:#eee;}
.light-mode .message.other{background:#ddd;color:#111;}
.chat-input{display:flex;padding:10px;gap:5px;}
input[type="file"]{display:none;}
.active-users{margin-top:10px;padding:10px;border:1px solid #333;border-radius:8px;max-height:200px;overflow-y:auto;}
.light-mode .active-users{border:1px solid #aaa;}
.user{padding:6px;cursor:pointer;border-bottom:1px solid #333;}
.light-mode .user{border-bottom:1px solid #aaa;}
</style>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
</head>
<body class="dark-mode">

<div id="login-screen" style="text-align:center;margin-top:50px;">
<h2>LiveConnect</h2>
<input type="text" id="username" placeholder="Enter your name"><br><br>
<button onclick="login()">Login</button>
</div>

<div id="app-screen" style="display:none;padding:10px;">
<div class="navbar">
<h2>LiveConnect</h2>
<div>
<button onclick="toggleDarkLight()">🌗 Toggle</button>
<button onclick="toggleChat()">💬 Chat</button>
<span id="active-count">Active Users: 0</span>
</div>
</div>

<div class="active-users" id="users-list"></div>

<div class="chat-container" id="chat-container">
<div class="chat-messages" id="messages-container"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
<input type="file" id="media-input" onchange="sendMedia(event)">
<button onclick="document.getElementById('media-input').click()">Media</button>
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
let currentUser="",chatId="global";

function login(){
let u=document.getElementById("username").value.trim();
if(!u){alert("Enter username");return;}
currentUser=u;
document.getElementById("login-screen").style.display="none";
document.getElementById("app-screen").style.display="block";
db.ref("active_users/"+currentUser).set({online:true});
db.ref("active_users/"+currentUser).onDisconnect().remove();
loadActiveUsers();
loadChat();
}

function toggleDarkLight(){
document.body.classList.toggle("light-mode");
document.body.classList.toggle("dark-mode");
}

function loadActiveUsers(){
db.ref("active_users").on("value",snap=>{
const users=snap.val()||{};
const list=document.getElementById("users-list");
list.innerHTML="";
let count=0;
for(let u in users){count++; const div=document.createElement("div"); div.textContent=u; div.className="user"; div.onclick=()=>openChat(u); list.appendChild(div);}
document.getElementById("active-count").textContent="Active Users: "+count;
});
}

function openChat(user){
chatId=[currentUser,user].sort().join("_");
document.getElementById("messages-container").innerHTML="";
document.getElementById("chat-container").style.display="flex";
loadChat();
}

function loadChat(){
const msgContainer=document.getElementById("messages-container");
db.ref("chat/"+chatId).on("child_added",snap=>{
const m=snap.val();
const div=document.createElement("div");
div.className="message "+(m.user===currentUser?"self":"other");
div.textContent=m.user+": "+(m.text||"");
msgContainer.appendChild(div);
msgContainer.scrollTop=msgContainer.scrollHeight;
});
}

function sendMessage(){
const text=document.getElementById("message-input").value.trim();
if(!text) return;
db.ref("chat/"+chatId).push({user:currentUser,text:text,time:Date.now()});
document.getElementById("message-input").value="";
}

function sendMedia(e){
const file=e.target.files[0];
if(!file) return;
alert("Media upload working, add Firebase Storage for full support");
}
function toggleChat(){
const chat=document.getElementById("chat-container");
chat.style.display=(chat.style.display==="flex")?"none":"flex";
}
</script>
</body>
</html>
