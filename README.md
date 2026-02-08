<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
body{
  margin:0; font-family:'Segoe UI',Roboto,sans-serif; background:#0d0d0d; color:#eee;
}
button{cursor:pointer; border:none; outline:none; border-radius:8px; transition:0.3s;}
button:hover{opacity:0.85;}
input{border:1px solid #444; border-radius:20px; padding:10px; width:100%; background:#1a1a1a; color:#eee; outline:none;}
::-webkit-scrollbar{width:8px;}
::-webkit-scrollbar-thumb{background:#0ff; border-radius:4px;}
::-webkit-scrollbar-track{background:#111;}

/* Navbar */
.navbar{
  display:flex; justify-content:space-between; align-items:center;
  padding:12px 20px; background:#111; border-bottom:1px solid #222; box-shadow:0 0 10px #0ff33a33;
}
.navbar h2{color:#0ff; font-size:22px; letter-spacing:1px;}

/* Layout */
.main-container{
  display:flex; flex-direction:row; height:calc(100vh - 60px);
}
.sidebar{
  width:220px; background:#111; border-right:1px solid #222; overflow-y:auto;
  padding-top:10px;
}
.sidebar h3{color:#0ff; text-align:center; margin:5px 0; letter-spacing:1px;}
.user{
  padding:12px; margin:5px 10px; border-radius:12px; background:#1a1a1a; 
  display:flex; justify-content:space-between; align-items:center; transition:0.3s;
}
.user:hover{background:#0ff10;}
.user.online::after{content:"•"; color:#0ff; font-weight:bold; margin-left:5px;}

/* Chat */
.content-area{flex:1; display:flex; flex-direction:column;}
.chat-container{flex:1; display:flex; flex-direction:column; padding:10px;}
.chat-messages{flex:1; overflow-y:auto; display:flex; flex-direction:column; gap:8px; padding-right:5px;}
.message{
  max-width:70%; padding:10px 14px; border-radius:20px; word-wrap:break-word;
  box-shadow:0 0 10px #0ff33a33; position:relative; transition:0.3s;
}
.message.self{align-self:flex-end; background:#0ff; color:#000;}
.message.other{align-self:flex-start; background:#222; color:#eee;}
.timestamp{font-size:11px; color:#aaa; margin-top:4px; text-align:right;}
.typing-indicator{font-size:13px; color:#0ff; margin:4px 0; font-style:italic;}
.chat-input{display:flex; gap:8px; padding-top:5px;}
.chat-input input{flex:1; padding:10px 14px; border-radius:20px; border:1px solid #0ff; background:#1a1a1a; color:#eee;}
.chat-input button{background:#0ff; color:#000; padding:8px 16px; font-weight:bold; border-radius:20px;}

/* Buttons hover */
button:hover{transform:scale(1.03);}

/* Responsive */
@media(max-width:900px){
  .main-container{flex-direction:column; height:auto;}
  .sidebar{width:100%; height:auto; display:flex; overflow-x:auto;}
  .sidebar .user{flex-shrink:0;}
  .chat-input{flex-direction:column;}
}
</style>

<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

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
const db = firebase.database();

let currentUser="", chatId="", typingTimeout="", isPrivateChat=true;

// ---- Login ----
function login(){
  const username = document.getElementById("username").value.trim();
  if(!username){alert("Enter username"); return;}
  currentUser = username;
  document.getElementById("login-screen").style.display="none";
  document.getElementById("app-screen").style.display="flex";
  
  db.ref("active_users/"+currentUser).set({online:true});
  db.ref("active_users/"+currentUser).onDisconnect().remove();
  loadActiveUsers();
}

// ---- Load Active Users ----
function loadActiveUsers(){
  db.ref("active_users").on("value", snap=>{
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

// ---- Chat Functions ----
function getChatId(user1,user2){ return [user1,user2].sort().join("_"); }

function openPrivateChat(user){
  chatId = getChatId(currentUser,user);
  isPrivateChat = true;
  document.getElementById("messages-container").innerHTML="";
  loadChat(chatId);
}

function loadChat(id){
  const msgContainer = document.getElementById("messages-container");
  const typingIndicator = document.getElementById("typing-indicator");

  db.ref("chat/"+id).on("child_added", snap=>{
    const msg = snap.val();
    if(isPrivateChat && !( [msg.user,msg.to].includes(currentUser) )) return;
    const div = document.createElement("div");
    div.className = "message "+(msg.user===currentUser?"self":"other");
    div.innerHTML = `<strong>${msg.user}</strong>: ${msg.text}<div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>`;
    msgContainer.appendChild(div);
    msgContainer.scrollTop = msgContainer.scrollHeight;
  });

  db.ref("typing/"+id).on("value",snap=>{
    const data=snap.val()||{};
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
  if(isPrivateChat){ msgData.to=chatId.replace(currentUser+"_","").replace("_"+currentUser,""); }
  db.ref("chat/"+chatId).push(msgData);
  document.getElementById("message-input").value="";
  db.ref("typing/"+chatId+"/"+currentUser).remove();
}
</script>
</head>
<body>

<!-- Login -->
<div id="login-screen" style="text-align:center;margin-top:80px;">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter Your Name"><br><br>
<button onclick="login()">Enter</button>
</div>

<!-- App -->
<div id="app-screen" style="display:none;flex-direction:column;">
<div class="navbar"><h2>LiveConnect</h2></div>

<div class="main-container">
<div class="sidebar" id="users-sidebar">
<h3>Active Users</h3>
<div id="users-list"></div>
</div>

<div class="content-area">
<div class="chat-container" id="chat-container">
<div class="chat-messages" id="messages-container"></div>
<div class="typing-indicator" id="typing-indicator"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button id="send-btn" onclick="sendMessage()">Send</button>
</div>
</div>
</div>
</div>
</div>
</body>
</html>
