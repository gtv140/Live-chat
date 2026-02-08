<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
/* Reset & basic */
body,html{margin:0;padding:0;font-family:'Segoe UI',Roboto,Arial,sans-serif;background:#111;color:#eee;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input,textarea{border:1px solid #555;border-radius:8px;padding:8px;width:100%;box-sizing:border-box;background:#1a1a1a;color:#eee;}
a{text-decoration:none;color:#0ff;}

/* Navbar */
.navbar{display:flex;justify-content:space-between;align-items:center;padding:10px 15px;background:#1f1f1f;border-bottom:1px solid #333;flex-wrap:wrap;}
.navbar h2{color:#0ff;}
.navbar .tabs{display:flex;gap:5px;flex-wrap:wrap;}
.navbar .tabs button{padding:6px 12px;background:#333;color:#eee;}
.navbar .tabs button.active{background:#0ff;color:#000;}
.navbar .chat-icon{font-size:22px;color:#0ff;cursor:pointer;}
.navbar .notification{font-size:22px;color:#0ff;cursor:pointer;position:relative;}
.navbar .notification span{position:absolute;top:-5px;right:-5px;background:red;color:#fff;border-radius:50%;padding:2px 5px;font-size:12px;}

/* Layout */
.main-container{display:flex;flex-direction:row;height:calc(100vh - 60px);}
.sidebar{width:220px;background:#1f1f1f;border-right:1px solid #333;overflow-y:auto;display:none;transition:0.3s;}
.sidebar h3{text-align:center;padding:15px;border-bottom:1px solid #333;color:#0ff;}
.user{padding:12px 15px;cursor:pointer;border-bottom:1px solid #333;display:flex;align-items:center;justify-content:space-between;}
.user.online::after{content:"•";color:#0ff;font-weight:bold;margin-left:5px;animation:blink 1s infinite;}
@keyframes blink{0%,50%,100%{opacity:1;}25%,75%{opacity:0;}}
.content-area{flex:1;display:flex;flex-direction:column;overflow-y:auto;padding:10px;}
.tab-panel{display:none;}
.tab-panel.active{display:block;}

/* Chat */
.chat-container{flex:1;display:flex;flex-direction:column;border-top:1px solid #333;display:none;}
.chat-messages{flex:1;overflow-y:auto;padding:10px;display:flex;flex-direction:column;gap:5px;scroll-behavior:smooth;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;box-shadow:0 0 3px #0ff;position:relative;animation:fadeIn 0.3s;}
.message.self{align-self:flex-end;background:#0ff;color:#000;}
.message.other{align-self:flex-start;background:#333;color:#eee;}
.timestamp{font-size:10px;color:#aaa;margin-top:4px;text-align:right;}
.typing-indicator{font-size:12px;color:#0ff;margin:2px 0;animation:blink 1s infinite;}
.chat-input{display:flex;padding:10px;border-top:1px solid #333;gap:5px;flex-wrap:wrap;}
.chat-input input{flex:1;min-width:150px;padding:8px;border-radius:20px;border:1px solid #0ff;background:#1a1a1a;color:#eee;}
.chat-input button{background:#0ff;color:#000;padding:6px 12px;font-weight:bold;}

/* Login */
#login-screen{display:flex;flex-direction:column;align-items:center;justify-content:center;height:100vh;gap:10px;}
#login-screen input{width:250px;padding:8px;border-radius:12px;border:1px solid #0ff;}

/* Dashboard */
.dashboard{padding:10px;background:#1f1f1f;border-bottom:1px solid #333;margin-bottom:10px;}
.dashboard h3{margin:5px 0;color:#0ff;}

/* Dark/Light toggle */
body.light-mode{background:#f4f4f4;color:#111;}
body.light-mode .navbar{background:#ddd;color:#111;}
body.light-mode .navbar .tabs button.active{background:#0ff;color:#fff;}
body.light-mode .chat-container{border-top:1px solid #aaa;}
body.light-mode input,body.light-mode textarea{background:#fff;color:#111;border:1px solid #0ff;}
body.light-mode .message.self{background:#0ff;color:#fff;}
body.light-mode .message.other{background:#ddd;color:#111;}

/* Animations */
@keyframes fadeIn{0%{opacity:0;transform:translateY(5px);}100%{opacity:1;transform:translateY(0);}}
</style>

<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-storage-compat.js"></script>

</head>
<body>

<!-- Login -->
<div id="login-screen">
<h2>LiveConnect</h2>
<input type="text" id="username" placeholder="Enter your name">
<button onclick="login()">Enter</button>
</div>

<!-- App -->
<div id="app-screen" style="display:none;flex-direction:column;">

<div class="navbar">
<h2>LiveConnect</h2>
<div class="tabs">
<button class="active" onclick="switchTab('dashboard')">Dashboard</button>
<button class="chat-icon" onclick="toggleChat()">💬 Chat</button>
<button onclick="toggleTheme()">🌗 Dark/Light</button>
<div class="notification" onclick="toggleActiveUsers()">🟢 Active <span id="active-count">0</span></div>
</div>
</div>

<div class="main-container">
<div class="sidebar" id="users-sidebar">
<h3>Active Users</h3>
<div id="users-list"></div>
</div>

<div class="content-area">
<div id="dashboard" class="tab-panel active">
<div class="dashboard">
<h3>Welcome, <span id="dash-username"></span> 😎</h3>
<p>LiveConnect is a modern real-time chat platform with professional look & feel.</p>
<p>About Company: LiveConnect Inc. provides seamless and secure chat experience for everyone.</p>
<p>Contact Support: support@liveconnect.com</p>
</div>
</div>

<div class="chat-container" id="chat-container">
<div class="chat-messages" id="messages-container"></div>
<div class="typing-indicator" id="typing-indicator"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button id="send-btn">Send</button>
<button onclick="showEmojiPicker()">😊</button>
<input type="file" id="media-input" style="display:none">
<button onclick="document.getElementById('media-input').click()">Media</button>
<audio id="notify-sound" src="https://www.myinstants.com/media/sounds/facebook_messenger.mp3" preload="auto"></audio>
<div id="emoji-picker" style="display:none;background:#222;padding:5px;border-radius:8px;position:absolute;bottom:60px;"></div>
</div>
</div>

</div>
</div>

<script>
// Firebase
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

// ---- Login ----
function login(){
  const username=document.getElementById("username").value.trim();
  if(!username){alert("Enter your name"); return;}
  currentUser=username;
  document.getElementById("login-screen").style.display="none";
  document.getElementById("app-screen").style.display="flex";
  document.getElementById("dash-username").textContent=currentUser;
  db.ref("active_users/"+currentUser).set(true);
  db.ref("active_users/"+currentUser).onDisconnect().remove();
  loadActiveUsers();
}

// ---- Tabs ----
function switchTab(tab){
document.querySelectorAll(".tab-panel").forEach(t=>t.style.display="none");
document.getElementById(tab).style.display="block";
document.querySelectorAll(".tabs button").forEach(b=>{
b.classList.remove("active");
});
}

// ---- Chat Toggle ----
function toggleChat(){
const chat=document.getElementById("chat-container");
chat.style.display=(chat.style.display==="flex")?"none":"flex";
}

// ---- Dark/Light ----
function toggleTheme(){
document.body.classList.toggle("light-mode");
}

// ---- Active Users ----
function loadActiveUsers(){
db.ref("active_users").on("value",snap=>{
const users=snap.val()||{};
const usersList=document.getElementById("users-list");
usersList.innerHTML="";
let count=0;
for(let user in users){
if(user!==currentUser){
const div=document.createElement("div");
div.textContent=user;
div.className="user online";
div.onclick=()=>openChat(user);
usersList.appendChild(div);
count++;
}
}
document.getElementById("active-count").textContent=count;
});
}

// ---- Chat ----
function getChatId(user1,user2){return [user1,user2].sort().join("_");}
function openChat(user){
chatId=getChatId(currentUser,user);
document.getElementById("messages-container").innerHTML="";
loadChat();
}

function loadChat(){
const container=document.getElementById("messages-container");
db.ref("chat/"+chatId).on("child_added",snap=>{
const msg=snap.val();
if(msg.to && msg.to!==currentUser && msg.user!==currentUser) return;
const div=document.createElement("div");
div.className="message "+(msg.user===currentUser?"self":"other");
div.innerHTML=`<strong>${msg.user}</strong>: ${msg.text||""}${msg.media?`<br>${msg.media.endsWith('.mp4')?'<video controls src="'+msg.media+'"></video>':'<img src="'+msg.media+'">'}`:""}<div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>`;
container.appendChild(div); container.scrollTop=container.scrollHeight;
if(msg.user!==currentUser)document.getElementById("notify-sound").play();
});
document.getElementById("message-input").oninput=()=>{
db.ref("typing/"+chatId+"/"+currentUser).set(true);
clearTimeout(typingTimeout);
typingTimeout=setTimeout(()=>{db.ref("typing/"+chatId+"/"+currentUser).remove();},2000);
};
document.getElementById("send-btn").onclick=sendMessage;
document.getElementById("media-input").onchange=function(e){
const file=e.target.files[0]; if(!file) return;
const storageRef=storage.ref('chat_media/'+Date.now()+'_'+file.name);
storageRef.put(file).then(snap=>snap.ref.getDownloadURL().then(url=>sendMessage(url)));
};
}

// ---- Send Message ----
function sendMessage(mediaUrl=null){
const text=document.getElementById("message-input").value.trim();
if(!text && !mediaUrl) return;
const msg={user:currentUser,text:text,media:mediaUrl||"",time:Date.now(),to:chatId.replace(currentUser+"_","").replace("_"+currentUser,"")};
db.ref("chat/"+chatId).push(msg);
document.getElementById("message-input").value="";
db.ref("typing/"+chatId+"/"+currentUser).remove();
}

// ---- Emoji Picker ----
const emojis=["😀","😎","😂","😍","😢","👍","🙏","🔥","💯","🎉"];
function showEmojiPicker(){
const picker=document.getElementById("emoji-picker");
picker.innerHTML="";
emojis.forEach(e=>{
const btn=document.createElement("button"); btn.textContent=e; btn.style.background="none"; btn.style.border="none"; btn.style.fontSize="20px"; btn.style.margin="2px";
btn.onclick=()=>{document.getElementById("message-input").value+=e;picker.style.display="none";};
picker.appendChild(btn);
});
picker.style.display=(picker.style.display==="none")?"block":"none";
}
</script>

</body>
</html>
