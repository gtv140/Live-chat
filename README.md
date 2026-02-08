<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
/* Reset & Fonts */
body, html {margin:0;padding:0;font-family:'Segoe UI',Roboto,sans-serif;background:#e5ddd5; height:100vh;}
* {box-sizing:border-box;}

/* Layout */
.container {display:flex;height:100vh;overflow:hidden;}
.sidebar {width:280px;background:#075e54;color:#fff;display:flex;flex-direction:column;}
.sidebar-header {padding:20px;font-size:20px;font-weight:bold;border-bottom:1px solid #128c7e;display:flex;justify-content:space-between;align-items:center;}
.active-users {flex:1;overflow-y:auto;padding:10px;}
.user {padding:10px;border-radius:8px;margin:5px 0;cursor:pointer;background:#128c7e;display:flex;align-items:center;gap:10px;}
.user:hover {background:#25d366;}
.user img {width:35px;height:35px;border-radius:50%;}

/* Chat Area */
.chat-area {flex:1;display:flex;flex-direction:column;background:#ece5dd;}
.chat-header {padding:15px;background:#075e54;color:#fff;font-weight:bold;border-bottom:1px solid #128c7e;display:flex;justify-content:space-between;align-items:center;}
.chat-messages {flex:1;padding:15px;overflow-y:auto;display:flex;flex-direction:column;gap:10px;}
.message {max-width:70%;padding:10px;border-radius:15px;word-wrap:break-word;position:relative;}
.message.self {align-self:flex-end;background:#dcf8c6;}
.message.other {align-self:flex-start;background:#fff;}
.timestamp {font-size:10px;color:#999;margin-top:5px;text-align:right;}
.typing-indicator {font-size:12px;color:#555;padding-left:5px;}

/* Input */
.chat-input {display:flex;padding:10px;background:#f0f0f0;border-top:1px solid #ccc;gap:5px;}
.chat-input input {flex:1;padding:10px;border-radius:20px;border:1px solid #ccc;}
.chat-input button {padding:10px 15px;border:none;border-radius:50px;background:#25d366;color:#fff;font-weight:bold;cursor:pointer;}
.chat-input button:hover {opacity:0.8;}

/* Dashboard & Welcome */
.dashboard {padding:15px;background:#075e54;color:#fff;font-size:16px;text-align:center;display:flex;justify-content:space-between;align-items:center;gap:10px;}
.toggle-btn {padding:5px 10px;background:#25d366;border:none;border-radius:8px;color:#fff;cursor:pointer;}

/* Dark Mode */
body.dark-mode {background:#111;}
body.dark-mode .chat-area {background:#222;}
body.dark-mode .message.self {background:#0f0;}
body.dark-mode .message.other {background:#333;color:#eee;}
body.dark-mode .chat-input {background:#333;}
body.dark-mode .chat-input input {background:#222;color:#eee;border:1px solid #0ff;}
body.dark-mode .dashboard {background:#075e54;color:#fff;}
</style>
</head>
<body>

<div id="login-screen" style="display:flex;justify-content:center;align-items:center;height:100vh;flex-direction:column;">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter your name" style="padding:10px;width:200px;border-radius:8px;border:1px solid #ccc;">
<br>
<button onclick="login()" style="padding:10px 20px;background:#25d366;color:#fff;border:none;border-radius:8px;cursor:pointer;">Login</button>
</div>

<div id="app-screen" style="display:none;flex-direction:column;height:100vh;">
<div class="dashboard" id="dashboard">
<span id="welcome-msg"></span>
<button class="toggle-btn" onclick="toggleDarkMode()">🌙 / ☀️</button>
</div>
<div class="container">
<div class="sidebar">
<div class="sidebar-header">Active Users <span id="active-count"></span></div>
<div class="active-users" id="users-list"></div>
</div>

<div class="chat-area">
<div class="chat-header" id="chat-header">Select a user to chat</div>
<div class="chat-messages" id="messages-container"></div>
<div class="typing-indicator" id="typing-indicator"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button id="send-btn">Send</button>
</div>
</div>
</div>
</div>

<audio id="notify-sound" src="https://www.myinstants.com/media/sounds/facebook_messenger.mp3" preload="auto"></audio>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, onValue, push, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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
let chatId = "";
let typingTimeout = null;

// Login
function login() {
    const username = document.getElementById("username").value.trim();
    if(!username){alert("Enter your name"); return;}
    currentUser = username;
    document.getElementById("login-screen").style.display="none";
    document.getElementById("app-screen").style.display="flex";
    document.getElementById("welcome-msg").textContent = `Welcome, ${currentUser} | About: LiveConnect`;

    const userRef = ref(db,'active_users/'+currentUser);
    set(userRef,{online:true});
    onDisconnect(userRef).remove();
    loadActiveUsers();
}

// Dark / Light mode
function toggleDarkMode(){document.body.classList.toggle("dark-mode");}

// Active Users
function loadActiveUsers(){
    const usersRef = ref(db,'active_users');
    onValue(usersRef,(snapshot)=>{
        const usersList = document.getElementById("users-list");
        usersList.innerHTML = "";
        const users = snapshot.val() || {};
        document.getElementById("active-count").textContent = `(${Object.keys(users).length})`;
        for(let user in users){
            if(user!==currentUser){
                const div = document.createElement("div");
                div.className="user";
                div.innerHTML = `<img src="https://ui-avatars.com/api/?name=${user}&background=25d366&color=fff&size=32">${user}`;
                div.onclick = ()=>openChat(user);
                usersList.appendChild(div);
            }
        }
    });
}

// Open Chat
function openChat(user){
    chatId = [currentUser,user].sort().join("_");
    document.getElementById("chat-header").textContent = "Chat with "+user;
    document.getElementById("messages-container").innerHTML="";
    loadChat(chatId);
}

// Load chat
function loadChat(id){
    const msgContainer = document.getElementById("messages-container");
    const typingIndicator = document.getElementById("typing-indicator");
    const chatRef = ref(db,'chat/'+id);
    onValue(chatRef,(snapshot)=>{
        msgContainer.innerHTML="";
        const msgs = snapshot.val()||{};
        for(let key in msgs){
            const msg = msgs[key];
            if([msg.user,msg.to].includes(currentUser)){
                const div = document.createElement("div");
                div.className="message "+(msg.user===currentUser?"self":"other");
                div.innerHTML = `<strong>${msg.user}</strong>: ${msg.text}<div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>`;
                msgContainer.appendChild(div);
            }
        }
        msgContainer.scrollTop = msgContainer.scrollHeight;
    });

    const typingRef = ref(db,'typing/'+id);
    onValue(typingRef,(snapshot)=>{
        const data = snapshot.val()||{};
        const typingUsers = Object.keys(data).filter(u=>u!==currentUser);
        typingIndicator.textContent = typingUsers.length>0 ? typingUsers.join(", ")+" is typing..." : "";
    });

    document.getElementById("message-input").oninput = ()=>{
        const tRef = ref(db,'typing/'+id+'/'+currentUser);
        set(tRef,true);
        clearTimeout(typingTimeout);
        typingTimeout = setTimeout(()=>{remove(tRef);},2000);
    };

    document.getElementById("send-btn").onclick = sendMessage;
}

// Send message
function sendMessage(){
    const text = document.getElementById("message-input").value.trim();
    if(!text) return;
    const msgRef = ref(db,'chat/'+chatId);
    push(msgRef,{user:currentUser,to:chatId.replace(currentUser+"_","").replace("_"+currentUser,""),text:text,time:Date.now()});
    document.getElementById("message-input").value="";
    document.getElementById("notify-sound").play();
}

window.login = login;
</script>

</body>
</html>
