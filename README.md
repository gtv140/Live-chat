<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap" rel="stylesheet">
<style>
*{box-sizing:border-box;margin:0;padding:0;}
body{font-family:'Roboto',sans-serif;background:#0f0f0f;color:#eee;height:100vh;display:flex;flex-direction:column;}

/* Navbar */
.navbar{
  height:60px;background:#111;display:flex;align-items:center;padding:0 20px;box-shadow:0 2px 8px #0ff2;
}
.navbar h2{color:#0ff;}

/* Layout */
.main-container{flex:1;display:flex;overflow:hidden;}
.sidebar{
  width:250px;background:#1a1a1a;border-right:1px solid #222;display:flex;flex-direction:column;
}
.sidebar h3{text-align:center;padding:15px 0;color:#0ff;border-bottom:1px solid #222;}
.user{
  padding:12px 15px;margin:5px 10px;border-radius:12px;display:flex;justify-content:space-between;align-items:center;
  cursor:pointer;background:#111;transition:0.2s;
}
.user:hover{background:#0ff10;}
.user.online::after{content:"•";color:#0ff;margin-left:5px;font-weight:bold;}

/* Chat Area */
.chat-area{flex:1;display:flex;flex-direction:column;background:#0d0d0d;}
.chat-header{height:60px;border-bottom:1px solid #222;display:flex;align-items:center;padding:0 20px;font-weight:500;color:#0ff;}
.chat-messages{flex:1;overflow-y:auto;padding:15px;display:flex;flex-direction:column;gap:10px;}
.message{
  max-width:70%;padding:10px 14px;border-radius:20px;word-wrap:break-word;position:relative;
}
.message.self{align-self:flex-end;background:#0ff;color:#000;border-bottom-right-radius:4px;}
.message.other{align-self:flex-start;background:#222;color:#eee;border-bottom-left-radius:4px;}
.timestamp{font-size:11px;color:#aaa;margin-top:4px;text-align:right;}
.typing-indicator{font-size:13px;color:#0ff;font-style:italic;margin:4px 20px;}

/* Chat Input */
.chat-input{display:flex;padding:10px;border-top:1px solid #222;background:#111;gap:10px;}
.chat-input input{flex:1;padding:10px 14px;border-radius:20px;border:1px solid #0ff;background:#1a1a1a;color:#eee;outline:none;}
.chat-input button{background:#0ff;color:#000;padding:10px 20px;border-radius:20px;font-weight:700;cursor:pointer;transition:0.2s;}
.chat-input button:hover{transform:scale(1.05);}

/* Scrollbar */
::-webkit-scrollbar{width:6px;}
::-webkit-scrollbar-thumb{background:#0ff;border-radius:3px;}
::-webkit-scrollbar-track{background:#111;}

/* Login */
#login-screen{text-align:center;margin-top:100px;}
#login-screen input{width:200px;margin-bottom:10px;}
#login-screen button{padding:10px 20px;border-radius:20px;background:#0ff;color:#000;font-weight:700;}
#login-screen button:hover{opacity:0.85;}

/* Responsive */
@media(max-width:900px){
  .main-container{flex-direction:column;}
  .sidebar{width:100%;flex-direction:row;overflow-x:auto;}
  .user{flex-shrink:0;margin:5px;}
}
</style>

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

let currentUser="", chatId="", typingTimeout="", isPrivateChat=true;

// ---- Login ----
function login(){
  const username = document.getElementById("username").value.trim();
  if(!username){alert("Enter username"); return;}
  currentUser=username;
  document.getElementById("login-screen").style.display="none";
  document.getElementById("app-screen").style.display="flex";
  db.ref("active_users/"+currentUser).set({online:true});
  db.ref("active_users/"+currentUser).onDisconnect().remove();
  loadActiveUsers();
}

// ---- Load Users ----
function loadActiveUsers(){
  db.ref("active_users").on("value",snap=>{
    const usersList = document.getElementById("users-list");
    usersList.innerHTML="";
    const users = snap.val()||{};
    for(let user in users){
      if(user!==currentUser){
        const div=document.createElement("div");
        div.textContent=user; div.className="user online";
        div.onclick=()=>openPrivateChat(user);
        usersList.appendChild(div);
      }
    }
  });
}

// ---- Chat ----
function getChatId(user1,user2){ return [user1,user2].sort().join("_"); }
function openPrivateChat(user){
  chatId=getChatId(currentUser,user);
  isPrivateChat=true;
  document.getElementById("messages-container").innerHTML="";
  loadChat(chatId);
}

function loadChat(id){
  const msgContainer=document.getElementById("messages-container");
  const typingIndicator=document.getElementById("typing-indicator");
  
  db.ref("chat/"+id).on("child_added",snap=>{
    const msg = snap.val();
    if(isPrivateChat && !( [msg.user,msg.to].includes(currentUser))) return;
    const div=document.createElement("div");
    div.className="message "+(msg.user===currentUser?"self":"other");
    div.innerHTML=`<strong>${msg.user}</strong>: ${msg.text}<div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>`;
    msgContainer.appendChild(div);
    msgContainer.scrollTop = msgContainer.scrollHeight;
  });

  db.ref("typing/"+id).on("value",snap=>{
    const data = snap.val()||{};
    const typingUsers = Object.keys(data).filter(u=>u!==currentUser);
    typingIndicator.textContent = typingUsers.length>0 ? typingUsers.join(", ")+" is typing..." : "";
  });

  document.getElementById("message-input").oninput=()=>{
    db.ref("typing/"+id+"/"+currentUser).set(true);
    clearTimeout(typingTimeout);
    typingTimeout=setTimeout(()=>{ db.ref("typing/"+id+"/"+currentUser).remove(); },2000);
  };
}

// ---- Send Message ----
function sendMessage(){
  const text=document.getElementById("message-input").value.trim();
  if(!text) return;
  let msgData={user:currentUser,text:text,time:Date.now()};
  if(isPrivateChat){ msgData.to = chatId.replace(currentUser+"_","").replace("_"+currentUser,""); }
  db.ref("chat/"+chatId).push(msgData);
  document.getElementById("message-input").value="";
  db.ref("typing/"+chatId+"/"+currentUser).remove();
}
</script>
</head>

<body>

<!-- Login -->
<div id="login-screen">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter Your Name"><br><br>
<button onclick="login()">Enter</button>
</div>

<!-- App -->
<div id="app-screen" style="display:none;flex:1;flex-direction:column;">
<div class="navbar"><h2>LiveConnect</h2></div>
<div class="main-container">
<div class="sidebar">
<h3>Active Users</h3>
<div id="users-list"></div>
</div>
<div class="chat-area">
<div class="chat-header">Chat</div>
<div class="chat-messages" id="messages-container"></div>
<div class="typing-indicator" id="typing-indicator"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
</div>
</div>
</div>
</div>

</body>
</html>
