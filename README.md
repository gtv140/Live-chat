<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pro Live Chat</title>

<!-- Firebase -->
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.firebasestorage.app",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let me="", current="";

/* LOGIN */
window.loginUser = () => {
  me = document.getElementById("username").value.trim();
  if(!me) return alert("Enter username");
  document.getElementById("login").style.display = "none";
  document.getElementById("app").style.display = "flex";

  const userRef = ref(db,"users/"+me);
  set(userRef,{online:true, lastLogin: Date.now()});
  onDisconnect(userRef).set({online:false});

  // Welcome new user
  const chatRef = ref(db,"chats/welcome_"+me);
  push(chatRef,{from:"System",text:`Welcome ${me}! Start chatting.`,time:Date.now()});

  loadUsers();
}

/* LOAD USERS */
function loadUsers(){
  const usersRef = ref(db,"users");
  const list = document.getElementById("users");
  onValue(usersRef, snap=>{
    list.innerHTML = "";
    snap.forEach(u=>{
      if(u.key!==me){
        let div = document.createElement("div");
        div.className="user";
        div.innerHTML = `<div class="avatar">${u.key[0]}</div>
        <div class="user-info"><b>${u.key}</b><br><small>${u.val().online?"Online":"Offline"}</small></div>`;
        div.onclick = ()=>openChat(u.key);
        list.appendChild(div);
      }
    });
  });
}

/* OPEN CHAT */
window.openChat = (u) => {
  current = u;
  document.getElementById("chatUser").innerText = u;

  const chatRef = ref(db,"chats/"+[me,u].sort().join("_"));
  const msgsDiv = document.getElementById("messages");

  onValue(chatRef, snap=>{
    msgsDiv.innerHTML = "";
    snap.forEach(m=>{
      const data = m.val();
      const div = document.createElement("div");
      div.className = data.from===me?"msg me":"msg other";
      div.innerHTML = `<b>${data.from}</b><p>${data.text}</p><small>${new Date(data.time).toLocaleTimeString()}</small>`;
      msgsDiv.appendChild(div);
    });
    msgsDiv.scrollTop = msgsDiv.scrollHeight;
  });
}

/* SEND MESSAGE */
window.sendMsg = ()=>{
  if(!current) return alert("Select a user");
  let text = document.getElementById("msg").value.trim();
  if(!text) return;
  const chatRef = ref(db,"chats/"+[me,current].sort().join("_"));
  push(chatRef,{from:me,text,time:Date.now(),seen:false});
  document.getElementById("msg").value="";
}

/* TYPING INDICATOR */
let typingTimeout;
document.getElementById("msg")?.addEventListener("input", ()=>{
  if(!current) return;
  const typingRef = ref(db,"typing/"+current+"/"+me);
  set(typingRef,true);
  clearTimeout(typingTimeout);
  typingTimeout = setTimeout(()=>{ set(typingRef,false); },2000);
});
const typingIndicator = ref(db,"typing");
onValue(typingIndicator, snap=>{
  let status = "";
  if(current && snap.hasChild(current)){
    snap.child(current).forEach(t=>{
      if(t.key!==me && t.val()) status = `${t.key} is typing...`;
    });
  }
  document.getElementById("status").innerText = status;
});

/* DARK MODE */
window.toggleTheme = ()=>document.body.classList.toggle("dark");

</script>

<style>
:root{
--bg:#f0f2f5;--card:#fff;--primary:#25D366;--text:#111;--muted:#667781;
}
body.dark{
--bg:#0b141a;--card:#111b21;--primary:#25D366;--text:#e9edef;--muted:#8696a0;
}
body{margin:0;height:100vh;font-family:system-ui;background:var(--bg);color:var(--text)}
.app{display:flex;height:100vh;overflow:hidden}

/* SIDEBAR */
.sidebar{width:100%;max-width:360px;background:var(--card);display:flex;flex-direction:column}
.sidebar header{height:56px;padding:0 14px;display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid #00000015}
.user-list{flex:1;overflow:auto}
.user{padding:12px 14px;display:flex;gap:12px;align-items:center;cursor:pointer;border-radius:8px}
.user:hover{background:#00000010}
.avatar{width:44px;height:44px;border-radius:50%;background:var(--primary);color:white;display:flex;align-items:center;justify-content:center;font-weight:600}
.user-info b{font-size:15px}
.user-info small{font-size:12px;color:var(--muted)}

/* CHAT */
.chat{flex:1;display:flex;flex-direction:column}
.chat-header{height:56px;padding:0 14px;display:flex;align-items:center;justify-content:space-between;background:var(--card);border-bottom:1px solid #00000015}
.messages{flex:1;padding:14px;overflow-y:auto;background:linear-gradient(180deg,#00000005,transparent)}
.msg{max-width:78%;padding:10px 12px;border-radius:14px;margin-bottom:8px;font-size:14px;line-height:1.4;box-shadow:0 1px 3px rgba(0,0,0,0.1)}
.me{background:#dcf8c6;margin-left:auto;border-bottom-right-radius:4px}
.other{background:var(--card);border-bottom-left-radius:4px}
.msg small{font-size:11px;color:var(--muted)}
.input{padding:10px;display:flex;gap:8px;background:var(--card);position:sticky;bottom:0}
.input input{flex:1;padding:12px 14px;border-radius:22px;border:1px solid #00000020;font-size:15px}
.input button{width:46px;height:46px;border-radius:50%;background:var(--primary);border:none;color:white;font-size:18px}

/* LOGIN */
.login{position:fixed;inset:0;background:var(--bg);display:flex;align-items:center;justify-content:center}
.login-box{background:var(--card);padding:30px;border-radius:10px;width:300px;text-align:center}
.login-box input{width:100%;padding:10px;margin-top:10px}

/* DARK MODE FIX */
body.dark .other{background:#1f2c34}
body.dark .me{background:#005c4b;color:white}
body.dark input{background:#111b21;color:white}

/* MOBILE */
@media(max-width:768px){
.sidebar{max-width:100px}
.user-info{display:none}
}
</style>

<body>
<div class="login" id="login">
  <div class="login-box">
    <h3>Enter Username</h3>
    <input id="username" placeholder="Your name"/>
    <button onclick="loginUser()">Start</button>
  </div>
</div>

<div class="app" id="app" style="display:none">
  <div class="sidebar">
    <header>
      <b>Live Chat</b>
      <i class="fa fa-moon" onclick="toggleTheme()"></i>
    </header>
    <div class="user-list" id="users"></div>
  </div>

  <div class="chat">
    <div class="chat-header">
      <div id="chatUser">Select user</div>
      <small id="status"></small>
    </div>
    <div class="messages" id="messages"></div>
    <div class="input">
      <input id="msg" placeholder="Type message"/>
      <button onclick="sendMsg()"><i class="fa fa-paper-plane"></i></button>
    </div>
  </div>
</div>
</body>
</html>
