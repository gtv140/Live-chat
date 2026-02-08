<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
  /* Reset & fonts */
  body,html{margin:0;padding:0;font-family:'Segoe UI',Roboto,sans-serif;background:#f2f2f2;color:#111;overflow:hidden;}
  button,input{outline:none;border:none;}
  button{cursor:pointer;border-radius:8px;transition:0.3s;}
  button:hover{opacity:0.85;}
  input{padding:8px;border-radius:20px;border:1px solid #ccc;width:100%;box-sizing:border-box;}

  /* Dark mode */
  body.dark{background:#111;color:#eee;}
  body.dark input{background:#222;color:#eee;border:1px solid #555;}
  body.dark .sidebar,.dark .chat-container,.dark .navbar{background:#1f1f1f;color:#eee;}

  /* Navbar */
  .navbar{height:60px;background:#0d6efd;color:#fff;display:flex;align-items:center;justify-content:space-between;padding:0 15px;box-shadow:0 2px 5px rgba(0,0,0,0.2);}
  .navbar h2{margin:0;font-size:20px;}
  .navbar button{background:#fff;color:#0d6efd;font-weight:bold;padding:6px 12px;}

  /* Layout */
  .main{display:flex;height:calc(100vh - 60px);}
  .sidebar{width:260px;background:#fff;border-right:1px solid #ccc;display:flex;flex-direction:column;overflow-y:auto;}
  .sidebar h3{margin:10px;padding:0;text-align:center;}
  .user{padding:10px 15px;border-bottom:1px solid #eee;cursor:pointer;display:flex;justify-content:space-between;align-items:center;}
  .user:hover{background:#f1f1f1;}
  .user.online::after{content:"●";color:green;margin-left:5px;font-size:12px;}

  .chat-container{flex:1;display:flex;flex-direction:column;}
  .chat-header{padding:10px;border-bottom:1px solid #ccc;font-weight:bold;}
  .chat-messages{flex:1;padding:10px;overflow-y:auto;display:flex;flex-direction:column;gap:5px;background:#f9f9f9;}
  .message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;}
  .message.self{align-self:flex-end;background:#0d6efd;color:#fff;}
  .message.other{align-self:flex-start;background:#eee;color:#111;}
  .timestamp{font-size:10px;color:#888;margin-top:2px;text-align:right;}
  .typing{font-size:12px;color:#555;margin:4px 0;}

  .chat-input{display:flex;gap:5px;padding:10px;border-top:1px solid #ccc;}
  .chat-input input{flex:1;border-radius:20px;padding:8px;}
  .chat-input button{background:#0d6efd;color:#fff;padding:6px 12px;border-radius:20px;}

  /* Login Screen */
  #login-screen{display:flex;flex-direction:column;align-items:center;justify-content:center;height:100vh;background:#0d6efd;color:#fff;}
  #login-screen input{width:250px;margin:10px 0;}

  /* Dashboard */
  .dashboard{padding:15px;border-bottom:1px solid #ccc;background:#f1f1f1;}
  .dashboard.dark{background:#222;color:#eee;}
  .dashboard h3{margin:5px 0;}
</style>
</head>
<body>

<!-- Login Screen -->
<div id="login-screen">
  <h2>Welcome to LiveConnect</h2>
  <input type="text" id="username" placeholder="Enter your username">
  <button onclick="login()">Enter</button>
</div>

<!-- Main App -->
<div id="app" style="display:none;flex-direction:column;height:100vh;">
  <div class="navbar">
    <h2>LiveConnect</h2>
    <div>
      <button onclick="toggleDark()">Dark/Light</button>
    </div>
  </div>

  <div class="dashboard" id="dashboard">
    <h3 id="welcome"></h3>
    <p>Connect with friends in realtime. Contact support at <a href="mailto:support@liveconnect.com">support@liveconnect.com</a></p>
  </div>

  <div class="main">
    <div class="sidebar">
      <h3>Active Users (<span id="active-count">0</span>)</h3>
      <div id="users-list"></div>
    </div>

    <div class="chat-container">
      <div class="chat-header" id="chat-with">Select a user to chat</div>
      <div class="chat-messages" id="messages"></div>
      <div class="typing" id="typing"></div>
      <div class="chat-input">
        <input type="text" id="msg-input" placeholder="Type a message...">
        <button onclick="sendMsg()">Send</button>
      </div>
    </div>
  </div>
</div>

<!-- Firebase -->
<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
  import { getDatabase, ref, set, onValue, push, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

  let currentUser="", chatUser="", chatId="";
  let typingTimeout;

  function login(){
    const uname = document.getElementById("username").value.trim();
    if(!uname){alert("Enter username"); return;}
    currentUser = uname;
    document.getElementById("login-screen").style.display="none";
    document.getElementById("app").style.display="flex";
    document.getElementById("welcome").innerText = `Welcome, ${currentUser}`;

    const userRef = ref(db, 'active_users/' + currentUser);
    set(userRef, {online:true});
    onDisconnect(userRef).remove();

    loadUsers();
  }

  function toggleDark(){document.body.classList.toggle("dark");document.querySelector(".dashboard").classList.toggle("dark");}

  function loadUsers(){
    const usersList = document.getElementById("users-list");
    const activeCount = document.getElementById("active-count");
    const usersRef = ref(db, 'active_users/');
    onValue(usersRef, snapshot=>{
      usersList.innerHTML="";
      const users = snapshot.val() || {};
      let count=0;
      for(let user in users){
        if(user!==currentUser){
          count++;
          const div = document.createElement("div");
          div.className="user online";
          div.textContent=user;
          div.onclick=()=>openChat(user);
          usersList.appendChild(div);
        }
      }
      activeCount.innerText = count;
    });
  }

  function getChatId(u1,u2){return [u1,u2].sort().join("_");}

  function openChat(user){
    chatUser = user;
    chatId = getChatId(currentUser,user);
    document.getElementById("chat-with").innerText = "Chat with " + user;
    document.getElementById("messages").innerHTML="";
    const chatRef = ref(db,'chats/'+chatId);
    onValue(chatRef,snap=>{
      const msgs = snap.val()||{};
      const messagesDiv = document.getElementById("messages");
      messagesDiv.innerHTML="";
      Object.values(msgs).forEach(m=>{
        const div = document.createElement("div");
        div.className = m.user===currentUser?"message self":"message other";
        div.innerHTML=`<strong>${m.user}</strong>: ${m.text}<div class="timestamp">${new Date(m.time).toLocaleTimeString()}</div>`;
        messagesDiv.appendChild(div);
        messagesDiv.scrollTop = messagesDiv.scrollHeight;
      });
    });
  }

  function sendMsg(){
    const input = document.getElementById("msg-input");
    const text = input.value.trim();
    if(!text || !chatId) return;
    const chatRef = ref(db,'chats/'+chatId);
    const newMsgRef = push(chatRef);
    set(newMsgRef,{user:currentUser,text:text,time:Date.now()});
    input.value="";
  }
</script>

</body>
</html>
