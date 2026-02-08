<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect Messenger Pro</title>

<style>
body { font-family: Arial, sans-serif; background:#f5f7fb; margin:0; padding:0; }
#login-screen, #chat-screen { max-width:600px; margin:auto; padding:20px; }
#login-screen { text-align:center; margin-top:50px; }
#chat-screen { display:none; background:#fff; border-radius:10px; box-shadow:0 0 10px rgba(0,0,0,0.1); }
#users-list { border-right:1px solid #ccc; width:30%; float:left; height:400px; overflow-y:auto; }
.user { padding:10px; border-bottom:1px solid #eee; cursor:pointer; }
.user.online::after { content:" •"; color:green; }
.user:hover { background:#f0f0f0; }
#messages-container { width:70%; float:right; height:400px; overflow-y:auto; border-left:1px solid #ccc; padding:10px; background:#fafafa;}
.message { margin:5px 0; padding:5px 10px; border-radius:10px; max-width:70%; word-wrap:break-word; position:relative; }
.message.self { background:#d9f0d9; margin-left:auto; text-align:right;}
.message.other { background:#f0d9d9; margin-right:auto; text-align:left;}
.message .timestamp { font-size:10px; color:#555; display:block; text-align:right; margin-top:2px; }
#message-input { width:70%; padding:8px; }
#send-btn { padding:8px 12px; margin-left:5px; }
.clearfix::after { content:""; clear:both; display:table; }
.typing-indicator { font-size:12px; color:#555; margin-top:5px; }
</style>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

</head>
<body>

<div id="login-screen">
<h2>Enter Your Username</h2>
<input type="text" id="username" placeholder="Your name">
<button onclick="login()">Login</button>
</div>

<div id="chat-screen">
<h2>LiveConnect Messenger Pro</h2>
<div class="clearfix">
<div id="users-list"></div>
<div id="chat-right">
<div id="messages-container"></div>
<div class="typing-indicator" id="typing-indicator"></div>
</div>
</div>
<div style="margin-top:10px;">
<input type="text" id="message-input" placeholder="Type a message...">
<button id="send-btn">Send</button>
</div>
</div>

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

// Initialize Firebase
firebase.initializeApp(firebaseConfig);
const db = firebase.database();

let currentUser = "";
let activeChatUser = "";

// Login function
function login() {
  const usernameInput = document.getElementById("username").value.trim();
  if(!usernameInput) return;
  currentUser = usernameInput;
  document.getElementById("login-screen").style.display = "none";
  document.getElementById("chat-screen").style.display = "block";

  // Add user to active users
  db.ref("active_users/" + currentUser).set({online:true});
  db.ref("active_users/" + currentUser).onDisconnect().remove();

  // Load users
  db.ref("active_users").on("value", snapshot=>{
    const usersList = document.getElementById("users-list");
    usersList.innerHTML = "";
    const users = snapshot.val() || {};
    for(let user in users){
      if(user!==currentUser){
        const div = document.createElement("div");
        div.textContent = user;
        div.className = "user online";
        div.onclick = ()=>selectUser(user);
        usersList.appendChild(div);
      }
    }
  });

  // Typing indicator
  const msgInput = document.getElementById("message-input");
  msgInput.addEventListener("input", ()=>{
    if(activeChatUser) db.ref("typing/" + activeChatUser + "/" + currentUser).set(msgInput.value ? true : false);
  });
}

// Select user to chat
function selectUser(user){
  activeChatUser = user;
  document.getElementById("messages-container").innerHTML = "";
  document.getElementById("typing-indicator").textContent = "";

  const chatRef = db.ref("private_chats/" + [currentUser,activeChatUser].sort().join("_"));

  // Listen for messages
  chatRef.on("child_added", snapshot=>{
    const data = snapshot.val();
    const msgDiv = document.createElement("div");
    msgDiv.textContent = data.user + ": " + data.message;
    msgDiv.className = "message " + (data.user===currentUser ? "self" : "other");
    const ts = document.createElement("span");
    ts.className="timestamp";
    ts.textContent = new Date(data.timestamp).toLocaleTimeString();
    msgDiv.appendChild(ts);
    document.getElementById("messages-container").appendChild(msgDiv);
    document.getElementById("messages-container").scrollTop = document.getElementById("messages-container").scrollHeight;
  });

  // Typing indicator
  db.ref("typing/" + currentUser + "/" + activeChatUser).on("value", snap=>{
    document.getElementById("typing-indicator").textContent = snap.val() ? activeChatUser + " is typing..." : "";
  });

  // Send button
  document.getElementById("send-btn").onclick = ()=>{
    const msg = document.getElementById("message-input").value.trim();
    if(!msg) return;
    chatRef.push({
      user: currentUser,
      message: msg,
      timestamp: Date.now()
    });
    document.getElementById("message-input").value = "";
  };
}

// Remove user on disconnect
window.addEventListener("beforeunload", ()=>{
  if(currentUser) db.ref("active_users/" + currentUser).remove();
});
</script>

</body>
</html>
