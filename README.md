<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
body{margin:0;font-family:'Segoe UI',sans-serif;background:#f0f2f5;color:#000;transition:0.3s;}
.dark-mode{background:#121212;color:#eee;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input{border:1px solid #ccc;border-radius:20px;padding:8px;width:100%;box-sizing:border-box;}
a{text-decoration:none;color:#0b81ff;}

/* Navbar */
.navbar{display:flex;justify-content:space-between;align-items:center;padding:10px 15px;background:#075e54;color:#fff;}
.navbar h2{margin:0;font-size:20px;}
.navbar button{background:#25d366;color:#fff;padding:6px 12px;}

/* Layout */
.main-container{display:flex;height:calc(100vh - 50px);}
.sidebar{width:280px;background:#f0f2f5;border-right:1px solid #ccc;overflow-y:auto;}
.dark-mode .sidebar{background:#1f1f1f;}
.sidebar h3{padding:15px;margin:0;border-bottom:1px solid #ccc;}
.sidebar .user{padding:12px 15px;cursor:pointer;display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid #ddd;}
.dark-mode .sidebar .user{border-color:#333;color:#eee;}
.user.online::after{content:"•";color:green;margin-left:5px;font-weight:bold;}

/* Chat */
.chat-area{flex:1;display:flex;flex-direction:column;justify-content:flex-end;background:#e5ddd5;position:relative;}
.dark-mode .chat-area{background:#222;}
.chat-header{padding:10px;background:#075e54;color:#fff;display:flex;align-items:center;justify-content:space-between;}
.messages{flex:1;padding:10px;overflow-y:auto;display:flex;flex-direction:column;gap:5px;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;position:relative;}
.message.self{align-self:flex-end;background:#dcf8c6;}
.message.other{align-self:flex-start;background:#fff;}
.message .time{font-size:10px;color:#555;margin-top:2px;text-align:right;}
.typing-indicator{font-size:12px;color:#555;margin:5px 0;}

/* Input */
.chat-input{display:flex;padding:10px;border-top:1px solid #ccc;gap:5px;}
.chat-input input{flex:1;padding:8px;border-radius:20px;border:1px solid #ccc;}
.chat-input button{background:#25d366;color:#fff;padding:8px 12px;border-radius:20px;}

/* Responsive */
@media(max-width:900px){
  .main-container{flex-direction:column;}
  .sidebar{width:100%;height:200px;}
}
</style>

<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

</head>
<body>

<!-- Login -->
<div id="login-screen" style="text-align:center;margin-top:100px;">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter Username"><br><br>
<button onclick="login()">Enter</button>
</div>

<!-- App -->
<div id="app-screen" style="display:none;flex-direction:column;height:100vh;">
<div class="navbar">
<h2>LiveConnect</h2>
<button onclick="toggleDarkMode()">Dark/Light</button>
</div>

<div class="main-container">
<div class="sidebar">
<h3>Active Users (<span id="active-count">0</span>)</h3>
<div id="users-list"></div>
</div>

<div class="chat-area">
<div class="chat-header" id="chat-header">Select a user to chat</div>
<div class="messages" id="messages"></div>
<div class="typing-indicator" id="typing"></div>
<div class="chat-input">
<input type="text" id="msg-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
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

let currentUser="",chatUser="",chatId="",typingTimeout;

function login(){
  const username=document.getElementById("username").value.trim();
  if(!username){alert("Enter username");return;}
  currentUser=username;
  document.getElementById("login-screen").style.display="none";
  document.getElementById("app-screen").style.display="flex";

  db.ref("active_users/"+currentUser).set({online:true});
  db.ref("active_users/"+currentUser).onDisconnect().remove();
  loadUsers();
}

function toggleDarkMode(){document.body.classList.toggle("dark-mode");}

function loadUsers(){
  db.ref("active_users").on("value",snap=>{
    const users=snap.val()||{};
    const usersList=document.getElementById("users-list");
    usersList.innerHTML=""; let count=0;
    for(let user in users){if(user!==currentUser){
      count++;
      const div=document.createElement("div");
      div.textContent=user; div.className="user online";
      div.onclick=()=>startChat(user);
      usersList.appendChild(div);
    }}
    document.getElementById("active-count").textContent=count;
  });
}

function startChat(user){
  chatUser=user;
  chatId=[currentUser,user].sort().join("_");
  document.getElementById("chat-header").textContent="Chat with "+user;
  document.getElementById("messages").innerHTML="";
  loadMessages();
}

function loadMessages(){
  const msgContainer=document.getElementById("messages");
  const typingInd=document.getElementById("typing");

  db.ref("chat/"+chatId).on("child_added",snap=>{
    const msg=snap.val();
    if(!([msg.from,msg.to].includes(currentUser))) return;
    const div=document.createElement("div");
    div.className="message "+(msg.from===currentUser?"self":"other");
    div.innerHTML=`<strong>${msg.from}</strong>: ${msg.text}<div class="time">${new Date(msg.time).toLocaleTimeString()}</div>`;
    msgContainer.appendChild(div);
    msgContainer.scrollTop=msgContainer.scrollHeight;
  });

  db.ref("typing/"+chatId).on("value",snap=>{
    const data=snap.val()||{};
    const typingUsers=Object.keys(data).filter(u=>u!==currentUser);
    typingInd.textContent=typingUsers.length>0?typingUsers.join(", ")+" is typing...":"";
  });

  document.getElementById("msg-input").oninput=()=>{
    db.ref("typing/"+chatId+"/"+currentUser).set(true);
    clearTimeout(typingTimeout);
    typingTimeout=setTimeout(()=>{db.ref("typing/"+chatId+"/"+currentUser).remove();},1500);
  };
}

function sendMessage(){
  const text=document.getElementById("msg-input").value.trim();
  if(!text || !chatUser) return;
  db.ref("chat/"+chatId).push({from:currentUser,to:chatUser,text, time:Date.now()});
  document.getElementById("msg-input").value="";
  db.ref("typing/"+chatId+"/"+currentUser).remove();
}
</script>
</body>
</html>
