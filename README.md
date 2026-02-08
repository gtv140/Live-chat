<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
/* --- Reset & Base --- */
body, html {margin:0;padding:0;font-family:Segoe UI,Roboto,sans-serif;background:#f0f2f5;overflow:hidden;}
.dark-mode body{background:#121212;color:#eee;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.2s;}
button:hover{opacity:0.8;}
input{border:1px solid #ccc;border-radius:20px;padding:8px;width:100%;box-sizing:border-box;}
.dark-mode input{background:#1a1a1a;border:1px solid #555;color:#eee;}

/* --- Layout --- */
#login-screen{position:absolute;top:0;left:0;width:100%;height:100%;display:flex;justify-content:center;align-items:center;flex-direction:column;}
#login-screen input{margin-bottom:10px;width:250px;}
#app-screen{display:none;height:100vh;display:flex;flex-direction:row;}

/* Sidebar */
#sidebar{width:250px;background:#fff;border-right:1px solid #ccc;display:flex;flex-direction:column;}
.dark-mode #sidebar{background:#1f1f1f;border-right:1px solid #333;}
#sidebar h2{padding:15px;margin:0;border-bottom:1px solid #ccc;text-align:center;}
#active-users{flex:1;overflow-y:auto;}
.user{padding:12px 15px;cursor:pointer;border-bottom:1px solid #eee;display:flex;justify-content:space-between;align-items:center;}
.dark-mode .user{border-bottom:1px solid #333;color:#eee;}
.user:hover{background:#f1f1f1;}
.dark-mode .user:hover{background:#333;}

/* Chat area */
#chat-area{flex:1;display:flex;flex-direction:column;}
#chat-header{padding:15px;background:#fff;border-bottom:1px solid #ccc;display:flex;justify-content:space-between;align-items:center;}
.dark-mode #chat-header{background:#1f1f1f;border-bottom:1px solid #333;color:#eee;}
#messages{flex:1;padding:15px;overflow-y:auto;background:#e5ddd5;}
.dark-mode #messages{background:#121212;}
.message{max-width:70%;padding:10px 14px;margin-bottom:10px;border-radius:20px;position:relative;word-wrap:break-word;}
.self{align-self:flex-end;background:#dcf8c6;}
.other{align-self:flex-start;background:#fff;}
.dark-mode .self{background:#056162;color:#fff;}
.dark-mode .other{background:#333;color:#eee;}
.timestamp{font-size:10px;color:#555;margin-top:5px;text-align:right;}
.dark-mode .timestamp{color:#aaa;}

/* Input */
#input-area{display:flex;padding:10px;border-top:1px solid #ccc;background:#fff;}
.dark-mode #input-area{background:#1f1f1f;border-top:1px solid #333;}
#input-area input{flex:1;padding:10px;border-radius:20px;border:1px solid #ccc;margin-right:10px;}
.dark-mode #input-area input{background:#1a1a1a;border:1px solid #555;color:#eee;}
#input-area button{padding:10px 15px;border-radius:20px;background:#0084ff;color:#fff;font-weight:bold;}

/* Scrollbar */
#messages::-webkit-scrollbar,#active-users::-webkit-scrollbar{width:6px;}
#messages::-webkit-scrollbar-thumb,#active-users::-webkit-scrollbar-thumb{background:#888;border-radius:3px;}
.dark-mode #messages::-webkit-scrollbar-thumb,#dark-mode #active-users::-webkit-scrollbar-thumb{background:#555;}
</style>
</head>
<body>

<!-- Login Screen -->
<div id="login-screen">
<h2>LiveConnect</h2>
<input type="text" id="username" placeholder="Enter Your Name">
<button onclick="login()">Login</button>
</div>

<!-- App Screen -->
<div id="app-screen">
<div id="sidebar">
<h2>Active Users (<span id="user-count">0</span>)</h2>
<div id="active-users"></div>
<button onclick="toggleDark()">Toggle Dark/Light Mode</button>
</div>

<div id="chat-area">
<div id="chat-header"><span id="chat-with">Select User</span></div>
<div id="messages"></div>
<div id="input-area">
<input type="text" id="message-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
</div>
</div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, onValue, push, remove, onDisconnect, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let currentUser = "", currentChat = "";

function login(){
const name = document.getElementById("username").value.trim();
if(!name){alert("Enter username");return;}
currentUser = name;
document.getElementById("login-screen").style.display="none";
document.getElementById("app-screen").style.display="flex";

// Set user online
set(ref(db,"active_users/"+currentUser),{online:true});
onDisconnect(ref(db,"active_users/"+currentUser)).remove();

// Load users
loadUsers();
}

function loadUsers(){
const userRef = ref(db,"active_users");
onValue(userRef,snap=>{
const data = snap.val()||{};
const container = document.getElementById("active-users");
container.innerHTML="";
let count=0;
for(let user in data){
if(user===currentUser) continue;
count++;
const div = document.createElement("div");
div.className="user";
div.textContent=user;
div.onclick=()=>openChat(user);
container.appendChild(div);
}
document.getElementById("user-count").textContent=count;
});
}

function openChat(user){
currentChat = user;
document.getElementById("chat-with").textContent = user;
document.getElementById("messages").innerHTML="";
const msgRef = ref(db,"chat/"+getChatId(currentUser,user));
onValue(msgRef,snap=>{
document.getElementById("messages").innerHTML="";
const data = snap.val()||{};
for(let key in data){
const m = data[key];
const div = document.createElement("div");
div.className="message "+(m.sender===currentUser?"self":"other");
div.innerHTML=`<strong>${m.sender}</strong>: ${m.text}<div class="timestamp">${new Date(m.time).toLocaleTimeString()}</div>`;
document.getElementById("messages").appendChild(div);
}
document.getElementById("messages").scrollTop = document.getElementById("messages").scrollHeight;
});
}

function sendMessage(){
const text = document.getElementById("message-input").value.trim();
if(!text || !currentChat) return;
const msgRef = ref(db,"chat/"+getChatId(currentUser,currentChat));
push(msgRef,{sender:currentUser,text:text,time:Date.now()});
document.getElementById("message-input").value="";
}

function getChatId(u1,u2){return [u1,u2].sort().join("_");}

function toggleDark(){
document.body.classList.toggle("dark-mode");
}
</script>

</body>
</html>
