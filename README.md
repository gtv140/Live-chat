<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
/* Body & Theme */
body{margin:0;font-family:'Segoe UI',sans-serif;background:#e5ddd5;color:#111;transition:0.3s;}
body.dark-mode{background:#121212;color:#eee;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input{border:1px solid #ccc;border-radius:20px;padding:8px;flex:1;}
body.dark-mode input{background:#1f1f1f;color:#eee;border:1px solid #555;}

/* Container */
.container{display:flex;height:100vh;overflow:hidden;}
.sidebar{width:250px;background:#f0f0f0;flex-shrink:0;display:flex;flex-direction:column;}
body.dark-mode .sidebar{background:#1e1e1e;}
.sidebar h3{margin:0;padding:10px;border-bottom:1px solid #ccc;}
body.dark-mode .sidebar h3{border-color:#333;}
.user{padding:10px;display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid #ccc;cursor:pointer;}
body.dark-mode .user{border-color:#333;}
.user.online::after{content:'•';color:green;margin-left:5px;font-weight:bold;}

/* Chat Area */
.content{flex:1;display:flex;flex-direction:column;}
.chat-header{padding:10px;border-bottom:1px solid #ccc;display:flex;align-items:center;justify-content:space-between;}
body.dark-mode .chat-header{border-color:#333;}
.chat-messages{flex:1;overflow-y:auto;padding:10px;background:#f7f7f7;}
body.dark-mode .chat-messages{background:#121212;}
.message{max-width:70%;padding:10px 14px;margin:5px 0;border-radius:18px;word-wrap:break-word;position:relative;box-shadow:0 1px 2px rgba(0,0,0,0.2);}
.message.self{align-self:flex-end;background:#0d6efd;color:#fff;}
.message.other{align-self:flex-start;background:#e4e6eb;color:#111;}
body.dark-mode .message.other{background:#2a2a2a;color:#eee;}
.timestamp{font-size:10px;color:#555;margin-top:2px;text-align:right;}
.read-tick{font-size:12px;color:#0d6efd;margin-left:4px;}
.chat-input{display:flex;padding:10px;gap:5px;border-top:1px solid #ccc;}
body.dark-mode .chat-input{border-color:#333;}

/* Profile */
.profile{display:flex;align-items:center;gap:10px;}
.profile img{width:40px;height:40px;border-radius:50%;}
.welcome{padding:10px;font-size:14px;color:#555;}
body.dark-mode .welcome{color:#aaa;}

/* Emoji Picker */
.emoji-picker{position:absolute;bottom:60px;right:20px;background:#fff;border:1px solid #ccc;padding:5px;border-radius:10px;display:none;flex-wrap:wrap;gap:5px;max-width:200px;}
body.dark-mode .emoji-picker{background:#1f1f1f;border:1px solid #555;}
.emoji-picker span{cursor:pointer;font-size:20px;}

/* Responsive Mobile */
@media(max-width:768px){
  .container{flex-direction:column;}
  .sidebar{width:100%;height:180px;overflow-x:auto;display:flex;flex-direction:row;}
  .sidebar h3{flex-shrink:0;padding:5px;}
  .user{flex-direction:column;align-items:center;padding:5px;}
  .chat-header{flex-direction:column;align-items:flex-start;gap:5px;}
}
</style>
</head>
<body>

<div class="welcome" id="welcome"></div>

<div class="container">
<div class="sidebar">
<h3>Active Users (<span id="user-count">0</span>)</h3>
<div id="users-list" style="overflow-y:auto;flex:1"></div>
<h3>About</h3>
<div style="padding:10px;font-size:13px;">
<p>LiveConnect - Realtime chat platform with WhatsApp-like design, emojis, typing indicators, and read receipts.</p>
</div>
<h3>Contact Support</h3>
<div style="padding:10px;font-size:13px;">
<p>Email: support@liveconnect.com</p>
</div>
<button onclick="toggleTheme()">Dark/Light Mode</button>
</div>

<div class="content">
<div class="chat-header">
<div class="profile">
<img src="https://cdn-icons-png.flaticon.com/512/149/149071.png" id="chat-user-pic">
<strong id="chat-username">Select User</strong>
</div>
<div id="typing-indicator" style="font-size:12px;color:#0d6efd"></div>
</div>

<div class="chat-messages" id="chat-messages"></div>

<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
<button onclick="toggleEmojiPicker()">😊</button>
<div class="emoji-picker" id="emoji-picker">
<span>😀</span><span>😂</span><span>😍</span><span>👍</span><span>🙏</span><span>💯</span>
</div>
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

// Login
let currentUser = prompt("Enter your username:");
document.getElementById('welcome').innerHTML = `Welcome, <strong>${currentUser}</strong>!`;
db.ref("active_users/"+currentUser).set({online:true});
db.ref("active_users/"+currentUser).onDisconnect().remove();

// Load Users
function loadUsers(){
  db.ref("active_users").on("value", snap=>{
    const users = snap.val() || {};
    const ul = document.getElementById('users-list'); ul.innerHTML="";
    let count=0;
    for(let u in users){
      if(u!==currentUser){
        count++;
        let div = document.createElement('div');
        div.className="user online"; div.textContent = u;
        div.onclick = ()=>openChat(u);
        ul.appendChild(div);
      }
    }
    document.getElementById('user-count').textContent=count;
  });
}
loadUsers();

// Chat
let chatId = ""; let typingTimeout;
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
    div.innerHTML = msg.text + (msg.read?'<span class="read-tick">✓✓</span>':'');
    const ts = document.createElement('div');
    ts.className="timestamp"; ts.textContent = new Date(msg.time).toLocaleTimeString();
    div.appendChild(ts);
    msgContainer.appendChild(div);
    msgContainer.scrollTop = msgContainer.scrollHeight;
    if(msg.to===currentUser) db.ref("chat/"+chatId+"/"+snap.key).update({read:true});
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
if(!text || !chatId) return;
db.ref("chat/"+chatId).push({user:currentUser,to:chatId.replace(currentUser+"_","").replace("_"+currentUser,""),text:text,time:Date.now(),read:false});
document.getElementById('message-input').value="";
db.ref("typing/"+chatId+"/"+currentUser).remove();
}

function toggleTheme(){document.body.classList.toggle("dark-mode");}

function toggleEmojiPicker(){
const picker = document.getElementById('emoji-picker');
picker.style.display = picker.style.display==="flex"?"none":"flex";
picker.querySelectorAll('span').forEach(emoji=>{
emoji.onclick = ()=>{document.getElementById('message-input').value+=emoji.textContent;}
});
}
</script>
</body>
</html>
