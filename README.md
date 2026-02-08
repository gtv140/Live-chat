<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
body {
    margin:0; font-family:'Segoe UI',sans-serif; background:#e5ddd5; color:#000;
    display:flex; justify-content:center; align-items:flex-start; min-height:100vh;
}
.dark-mode body { background:#121b22; color:#fff; }
#login-screen, #app-screen { width:100%; max-width:500px; margin-top:50px; }
input,button { padding:10px; margin:5px 0; border-radius:5px; border:none; }
button { cursor:pointer; }
#chat-container { display:flex; flex-direction:column; border:1px solid #ccc; height:80vh; border-radius:8px; overflow:hidden; }
#messages-container { flex:1; padding:10px; overflow-y:auto; background:#f0f0f0; }
.dark-mode #messages-container { background:#1e2c33; }
.message { max-width:70%; margin:5px 0; padding:8px 12px; border-radius:20px; position:relative; }
.self { align-self:flex-end; background:#dcf8c6; }
.dark-mode .self { background:#056162; color:#fff; }
.other { align-self:flex-start; background:#fff; }
.dark-mode .other { background:#262f34; color:#fff; }
.timestamp { font-size:10px; color:gray; margin-top:4px; text-align:right; }
.chat-input { display:flex; gap:5px; padding:5px; border-top:1px solid #ccc; }
.active-users { margin:10px 0; }
.user-btn { display:flex; justify-content:space-between; padding:5px 10px; border-radius:5px; border:1px solid #ccc; margin-bottom:5px; cursor:pointer; }
.dark-mode .user-btn { border-color:#444; }
#dark-toggle { margin:10px 0; }
</style>
<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
  import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

  let currentUser = "", chatId = "";

  function login() {
    const username = document.getElementById("username").value.trim();
    if(!username) return alert("Enter username");
    currentUser = username;
    set(ref(db, 'active_users/'+currentUser), {online:true});
    document.getElementById("login-screen").style.display="none";
    document.getElementById("app-screen").style.display="block";
    loadActiveUsers();
  }

  function loadActiveUsers() {
    const usersList = document.getElementById("users-list");
    onValue(ref(db,'active_users'),snap=>{
      usersList.innerHTML="";
      const users = snap.val() || {};
      for(let user in users) {
        if(user!==currentUser){
          const div = document.createElement("div");
          div.className="user-btn";
          div.textContent = user + " (online)";
          div.onclick = ()=> openChat(user);
          usersList.appendChild(div);
        }
      }
    });
  }

  function openChat(user){
    chatId = [currentUser,user].sort().join("_");
    document.getElementById("messages-container").innerHTML="";
    onValue(ref(db,'chat/'+chatId),snap=>{
      const messagesContainer = document.getElementById("messages-container");
      messagesContainer.innerHTML="";
      const msgs = snap.val()||{};
      for(let key in msgs){
        const m = msgs[key];
        const div = document.createElement("div");
        div.className="message "+(m.user===currentUser?"self":"other");
        div.innerHTML = `<strong>${m.user}</strong>: ${m.text}<div class="timestamp">${new Date(m.time).toLocaleTimeString()}</div>`;
        messagesContainer.appendChild(div);
        messagesContainer.scrollTop = messagesContainer.scrollHeight;
      }
    });
  }

  function sendMessage() {
    const text = document.getElementById("message-input").value.trim();
    if(!text || !chatId) return;
    push(ref(db,'chat/'+chatId),{user:currentUser,text:text,time:Date.now()});
    document.getElementById("message-input").value="";
  }

  function toggleDark() {
    document.body.classList.toggle("dark-mode");
  }

  window.login=login;
  window.sendMessage=sendMessage;
  window.toggleDark=toggleDark;
</script>
</head>
<body>
<div id="login-screen">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter username"><br>
<button onclick="login()">Login</button>
</div>

<div id="app-screen" style="display:none;">
<h2>LiveConnect</h2>
<button id="dark-toggle" onclick="toggleDark()">Toggle Dark / Light</button>
<div class="active-users">
<h3>Active Users</h3>
<div id="users-list"></div>
</div>

<div id="chat-container">
<div id="messages-container"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button onclick="sendMessage()">Send</button>
</div>
</div>
</div>
</body>
</html>
