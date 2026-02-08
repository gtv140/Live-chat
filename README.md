<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect - Mobile</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

/* FIREBASE CONFIG */
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
  set(userRef,{online:true,lastLogin:Date.now()});
  onDisconnect(userRef).set({online:false});

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
        div.innerHTML = `<b>${u.key}</b>`;
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
      if(data.image){
        div.innerHTML = `<b>${data.from}</b><p><img src="${data.image}" style="max-width:150px;border-radius:8px"/></p>
        <small>${new Date(data.time).toLocaleTimeString()}</small>`;
      } else {
        div.innerHTML = `<b>${data.from}</b><p>${data.text}</p>
        <small>${new Date(data.time).toLocaleTimeString()}</small>`;
      }
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

/* SEND IMAGE */
window.sendImage = ()=>{
  if(!current) return alert("Select a user");
  const url = prompt("Enter image URL (free image, e.g., Unsplash)");
  if(!url) return;
  const chatRef = ref(db,"chats/"+[me,current].sort().join("_"));
  push(chatRef,{from:me,image:url,time:Date.now()});
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
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text)}
.header{height:150px;background:url('https://images.unsplash.com/photo-1557682261-1b0b0a3ef16b?ixlib=rb-4.0.3&auto=format&fit=crop&w=1600&q=80') center/cover;display:flex;align-items:center;justify-content:center;color:white;font-size:1.8rem;font-weight:bold;text-shadow:1px 1px 5px #000}
.app{display:flex;flex-direction:column;height:100vh;overflow:hidden}
.sidebar{display:flex;flex-direction:row;overflow-x:auto;background:var(--card);border-bottom:1px solid #00000015;padding:5px}
.user{padding:8px 12px;margin:0 5px;background:#00000010;border-radius:20px;cursor:pointer;white-space:nowrap}
.chat{flex:1;display:flex;flex-direction:column;background:var(--bg)}
.chat-header{height:50px;padding:0 10px;display:flex;align-items:center;justify-content:space-between;background:var(--card);border-bottom:1px solid #00000015;font-weight:bold}
.messages{flex:1;padding:10px;overflow-y:auto;background:linear-gradient(180deg,#00000005,transparent)}
.msg{max-width:75%;padding:8px 12px;border-radius:14px;margin-bottom:6px;font-size:14px;line-height:1.4;box-shadow:0 1px 2px rgba(0,0,0,0.1)}
.me{background:#dcf8c6;margin-left:auto;border-bottom-right-radius:4px}
.other{background:#fff;border-bottom-left-radius:4px}
.msg small{font-size:11px;color:var(--muted)}
.input{padding:6px;display:flex;gap:6px;background:var(--card);position:sticky;bottom:0}
.input input{flex:1;padding:10px 12px;border-radius:22px;border:1px solid #00000020;font-size:14px}
.input button{width:40px;height:40px;border-radius:50%;background:var(--primary);border:none;color:white;font-size:16px}
.input .fa-image{background:#ff5e57;padding:8px;border-radius:50%;color:white;cursor:pointer}
body.dark .other{background:#1f2c34;color:white}
body.dark .me{background:#005c4b;color:white}
body.dark input{background:#111b21;color:white}
.login{position:fixed;inset:0;background:var(--bg);display:flex;align-items:center;justify-content:center;z-index:1000}
.login-box{background:var(--card);padding:20px;border-radius:10px;width:90%;max-width:320px;text-align:center}
.login-box input{width:100%;padding:10px;margin-top:10px;font-size:14px}
</style>

<body>
<div class="header">Live Connect</div>

<div class="login" id="login">
  <div class="login-box">
    <h3>Enter Your Name</h3>
    <input id="username" placeholder="Full Name"/>
    <button onclick="loginUser()">Start Chat</button>
  </div>
</div>

<div class="app" id="app" style="display:none">
  <div class="sidebar" id="users">
    <!-- Users will be loaded here as full names -->
  </div>

  <div class="chat">
    <div class="chat-header">
      <div id="chatUser">Select User</div>
      <small id="status"></small>
      <i class="fa fa-moon" onclick="toggleTheme()"></i>
    </div>
    <div class="messages" id="messages"></div>
    <div class="input">
      <input id="msg" placeholder="Type message"/>
      <button onclick="sendMsg()"><i class="fa fa-paper-plane"></i></button>
      <i class="fa fa-image" onclick="sendImage()"></i>
    </div>
  </div>
</div>
</body>
</html>
