<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
/* ---------- Basic Reset & Body ---------- */
body {
  margin: 0;
  font-family: 'Segoe UI', Roboto, Arial, sans-serif;
  background: #111;
  color: #eee;
}
button {cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input {border:1px solid #555;border-radius:8px;padding:8px;width:100%;box-sizing:border-box;background:#1a1a1a;color:#eee;}
a{text-decoration:none;color:#0ff;}

/* ---------- Navbar ---------- */
.navbar{
  display:flex;
  justify-content:space-between;
  align-items:center;
  padding:10px 15px;
  background:#1f1f1f;
  border-bottom:1px solid #333;
}
.navbar h2{color:#0ff;}
.navbar .tabs button{margin-left:5px;background:#333;color:#eee;padding:6px 12px;}
.navbar .tabs button.active{background:#0ff;color:#000;}
.navbar .toggle-theme{cursor:pointer;padding:6px 12px;background:#333;color:#0ff;border-radius:8px;}

/* ---------- Layout ---------- */
.main-container{display:flex;flex-direction:row;height:calc(100vh - 60px);}
.sidebar{width:220px;background:#1f1f1f;border-right:1px solid #333;overflow-y:auto;}
.sidebar h3{text-align:center;padding:15px;border-bottom:1px solid #333;color:#0ff;}
.user{padding:12px 15px;cursor:pointer;border-bottom:1px solid #333;display:flex;justify-content:space-between;}
.user.online::after{content:"•";color:#0f0;font-weight:bold;margin-left:5px;}
.content-area{flex:1;display:flex;flex-direction:column;overflow-y:auto;padding:10px;}
.tab-panel{display:none;}
.tab-panel.active{display:block;}

/* ---------- Chat ---------- */
.chat-container{flex:1;display:flex;flex-direction:column;border-top:1px solid #333;}
.chat-messages{flex:1;overflow-y:auto;padding:10px;display:flex;flex-direction:column;gap:5px;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;position:relative;}
.message.self{align-self:flex-end;background:#0ff;color:#000;box-shadow:0 0 5px #0ff;}
.message.other{align-self:flex-start;background:#333;color:#eee;box-shadow:0 0 5px #0ff;}
.timestamp{font-size:10px;color:#aaa;margin-top:4px;text-align:right;}
.chat-input{display:flex;padding:10px;border-top:1px solid #333;gap:5px;}
.chat-input input{flex:1;border-radius:20px;padding:8px;border:1px solid #0ff;background:#1a1a1a;color:#eee;}
.chat-input button{background:#0ff;color:#000;padding:6px 12px;font-weight:bold;}

/* ---------- Dashboard ---------- */
.dashboard{padding:10px;background:#1f1f1f;margin-bottom:10px;border-radius:12px;}

/* ---------- Responsive ---------- */
@media(max-width:900px){
  .main-container{flex-direction:column;height:auto;}
  .sidebar{width:100%;height:auto;}
  .chat-input{flex-direction:column;}
}
</style>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-storage-compat.js"></script>

</head>
<body>

<!-- Login -->
<div id="login-screen" style="text-align:center;margin-top:50px;">
  <h2>Welcome to LiveConnect</h2>
  <input type="text" id="username" placeholder="Enter Your Name"><br><br>
  <button onclick="login()">Enter</button>
</div>

<!-- App -->
<div id="app-screen" style="display:none;flex-direction:column;">
  <div class="navbar">
    <h2>LiveConnect</h2>
    <div class="tabs">
      <button class="active" onclick="switchTab('dashboard')">Dashboard</button>
      <button onclick="switchTab('chat')">Chat</button>
    </div>
    <div class="toggle-theme" onclick="toggleTheme()">Dark/Light</div>
  </div>

  <div class="main-container">
    <!-- Sidebar -->
    <div class="sidebar" id="users-sidebar">
      <h3>Active Users (<span id="active-count">0</span>)</h3>
      <div id="users-list"></div>
    </div>

    <!-- Content -->
    <div class="content-area">
      <div id="dashboard" class="tab-panel active">
        <div class="dashboard">
          <h3 id="welcome-msg"></h3>
          <p>About LiveConnect: Connect with your friends instantly. Modern, clean, and easy-to-use chat platform.</p>
          <p>Contact Support: <a href="mailto:support@liveconnect.com">support@liveconnect.com</a></p>
        </div>
      </div>

      <div id="chat" class="tab-panel">
        <div class="chat-container">
          <div class="chat-messages" id="messages-container"></div>
          <div class="chat-input">
            <input type="text" id="message-input" placeholder="Type a message...">
            <button id="send-btn">Send</button>
            <input type="file" id="media-input" style="display:none">
            <button onclick="document.getElementById('media-input').click()">Media</button>
            <audio id="notify-sound" src="https://www.myinstants.com/media/sounds/facebook_messenger.mp3" preload="auto"></audio>
          </div>
        </div>
      </div>
    </div>
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
firebase.initializeApp(firebaseConfig);
const db=firebase.database();
const storage=firebase.storage();

let currentUser="",chatId="",typingTimeout;

// ---------- Login ----------
function login(){
  const username=document.getElementById("username").value.trim();
  if(!username){alert("Enter username");return;}
  currentUser=username;
  document.getElementById("login-screen").style.display="none";
  document.getElementById("app-screen").style.display="flex";
  document.getElementById("welcome-msg").textContent=`Welcome, ${currentUser}!`;
  db.ref("active_users/"+currentUser).set(true);
  db.ref("active_users/"+currentUser).onDisconnect().remove();
  loadActiveUsers();
  chatId="global_chat"; // simple global chat
  loadChat(chatId);
}

// ---------- Tabs ----------
function switchTab(tab){
  const tabs=["dashboard","chat"];
  tabs.forEach(t=>{
    document.getElementById(t).style.display=(t===tab)?"block":"none";
    document.querySelectorAll(".tabs button").forEach(b=>{
      if(b.textContent.toLowerCase()===tab) b.classList.add("active"); else b.classList.remove("active");
    });
  });
}

// ---------- Theme Toggle ----------
function toggleTheme(){
  if(document.body.style.background==="" || document.body.style.background=="#111"){
    document.body.style.background="#eee";document.body.style.color="#111";
  } else {document.body.style.background="#111";document.body.style.color="#eee";}
}

// ---------- Active Users ----------
function loadActiveUsers(){
  db.ref("active_users").on("value",snap=>{
    const users=snap.val()||{};
    document.getElementById("users-list").innerHTML="";
    let count=0;
    for(let u in users){
      if(u!==currentUser){
        count++;
        const div=document.createElement("div");
        div.className="user online";
        div.textContent=u;
        div.onclick=()=>openPrivateChat(u);
        document.getElementById("users-list").appendChild(div);
      }
    }
    document.getElementById("active-count").textContent=count;
  });
}

// ---------- Chat ----------
function loadChat(id){
  const msgContainer=document.getElementById("messages-container");
  db.ref("chat/"+id).on("child_added",snap=>{
    const msg=snap.val();
    const div=document.createElement("div");
    div.className="message "+(msg.user===currentUser?"self":"other");
    div.innerHTML=`<strong>${msg.user}</strong>: ${msg.text || ""}${msg.media?`<br>${msg.media.endsWith('.mp4')?'<video controls src="'+msg.media+'"></video>':'<img src="'+msg.media+'" style="max-width:200px;">':''}<div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>`;
    msgContainer.appendChild(div);
    msgContainer.scrollTop=msgContainer.scrollHeight;
    if(msg.user!==currentUser) document.getElementById("notify-sound").play();
  });

  document.getElementById("message-input").oninput=()=>{
    db.ref("typing/"+id+"/"+currentUser).set(true);
    clearTimeout(typingTimeout);
    typingTimeout=setTimeout(()=>{db.ref("typing/"+id+"/"+currentUser).remove();},2000);
  };

  document.getElementById("send-btn").onclick=()=>sendMessage();
  document.getElementById("media-input").onchange=function(e){
    const file=e.target.files[0]; if(!file) return;
    const storageRef=storage.ref('chat_media/'+Date.now()+'_'+file.name);
    storageRef.put(file).then(snap=>snap.ref.getDownloadURL().then(url=>sendMessage(url)));
  };
}

// ---------- Send Message ----------
function sendMessage(mediaUrl=null){
  const text=document.getElementById("message-input").value.trim();
  if(!text && !mediaUrl) return;
  db.ref("chat/"+chatId).push({user:currentUser,text:text,media:mediaUrl||"",time:Date.now()});
  document.getElementById("message-input").value="";
  db.ref("typing/"+chatId+"/"+currentUser).remove();
}

// ---------- Private Chat Shortcut ----------
function openPrivateChat(user){
  alert("Private chat feature coming soon!");
}
</script>

</body>
</html>
