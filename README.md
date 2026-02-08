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
const unread = {};

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

/* LOAD USERS + ACTIVE COUNT + GROUPS */
function loadUsers(){
  const usersRef = ref(db,"users");
  const list = document.getElementById("users");
  const activeCountDiv = document.getElementById("activeCount");
  onValue(usersRef, snap=>{
    list.innerHTML = "";
    let activeCount = 0;
    snap.forEach(u=>{
      if(u.key!==me){
        let div = document.createElement("div");
        div.className = "user " + (u.val().online ? "online":"offline");
        div.id = "user-"+u.key;
        div.innerHTML = `<span class="status"></span>${u.key}` + (unread[u.key]?` • ${unread[u.key]}`:"");
        div.onclick = ()=>openChat(u.key);
        list.appendChild(div);
      }
      if(u.val().online) activeCount++;
    });
    activeCountDiv.innerText = `Active Users: ${activeCount}`;
  });
  loadGroups();
}

/* GROUP CHAT LIST */
function loadGroups(){
  const groupsRef = ref(db,"groups");
  const list = document.getElementById("users");
  onValue(groupsRef, snap=>{
    snap.forEach(g=>{
      let div = document.createElement("div");
      div.className = "user group";
      div.id = "group-"+g.key;
      div.innerHTML = `<i class="fa fa-users"></i> ${g.key}` + (unread[g.key]?` • ${unread[g.key]}`:"");
      div.onclick = ()=>openChat(g.key,true);
      list.appendChild(div);
    });
  });
}

/* CREATE GROUP */
window.createGroup = ()=>{
  const name = prompt("Enter Group Name");
  if(!name) return;
  const groupRef = ref(db,"groups/"+name);
  set(groupRef,{createdBy:me});
  alert(`Group ${name} created!`);
}

/* OPEN CHAT */
window.openChat = (u,isGroup=false) => {
  current = u;
  document.getElementById("chatUser").innerText = u + (isGroup?" (Group)":"");

  if(unread[u]) unread[u]=0;
  const div = document.getElementById((isGroup?"group-":"user-")+u);
  if(div) div.innerHTML = div.innerText.replace(/ • \d+/,"");

  const chatRef = ref(db,isGroup?"groupChats/"+u:"chats/"+[me,u].sort().join("_"));
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
  if(!current) return alert("Select a user or group");
  let text = document.getElementById("msg").value.trim();
  if(!text) return;
  const chatRef = ref(db,current.startsWith("group")?"groupChats/"+current:"chats/"+[me,current].sort().join("_"));
  push(chatRef,{from:me,text,time:Date.now(),seen:false});
  document.getElementById("msg").value="";
}

/* SEND IMAGE */
window.sendImage = ()=>{
  if(!current) return alert("Select a user or group");
  const url = prompt("Enter image URL (free image, e.g., Unsplash)");
  if(!url) return;
  const chatRef = ref(db,current.startsWith("group")?"groupChats/"+current:"chats/"+[me,current].sort().join("_"));
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

/* HERO SLIDER */
let heroIndex = 0;
const slides = [
  {img:"https://images.unsplash.com/photo-1603415526960-faa0b7a6d1f8?ixlib=rb-4.0.3&auto=format&fit=crop&w=800&q=80",text:"Connect instantly with friends"},
  {img:"https://images.unsplash.com/photo-1533777324565-a040eb52fdb8?ixlib=rb-4.0.3&auto=format&fit=crop&w=800&q=80",text:"Share your moments"},
  {img:"https://images.unsplash.com/photo-1543269865-cbf427effbad?ixlib=rb-4.0.3&auto=format&fit=crop&w=800&q=80",text:"Chat anytime, anywhere"}
];
function showSlide(){
  const hero = document.getElementById("hero");
  hero.style.backgroundImage = `url('${slides[heroIndex].img}')`;
  document.getElementById("heroText").innerText = slides[heroIndex].text;
  heroIndex = (heroIndex+1)%slides.length;
}
setInterval(showSlide,4000);
window.onload = showSlide;
</script>

<style>
:root{
--bg:#f0f2f5;--card:#fff;--primary:#25D366;--text:#111;--muted:#667781;
}
body.dark{
--bg:#0b141a;--card:#111b21;--primary:#25D366;--text:#e9edef;--muted:#8696a0;
}
body{margin:0;font-family:system-ui;background:var(--bg);color:var(--text)}
.header{height:60px;background:var(--primary);display:flex;align-items:center;justify-content:center;color:white;font-size:1.8rem;font-weight:bold;position:sticky;top:0;z-index:100}
.hero{height:200px;background-size:cover;background-position:center;display:flex;align-items:center;justify-content:center;color:white;font-size:1.4rem;font-weight:bold;text-shadow:1px 1px 6px #000}
.section{padding:15px;text-align:center}
.section h2{margin-bottom:10px}
.section p{margin:5px auto;max-width:300px;font-size:14px}
.app{display:flex;flex-direction:column;height:calc(100vh - 260px);overflow:hidden}
.sidebar{display:flex;overflow-x:auto;padding:8px;background:var(--card)}
.user{padding:6px 12px;margin:0 5px;background:#00000010;border-radius:20px;cursor:pointer;white-space:nowrap;font-size:14px;display:flex;align-items:center;gap:6px}
.user .status{width:8px;height:8px;border-radius:50%;display:inline-block}
.user.online .status{background:#25D366}
.user.offline .status{background:#8696a0}
.activeCount{padding:5px;font-size:12px;color:var(--muted)}
.chat{flex:1;display:flex;flex-direction:column;background:var(--bg);min-height:0}
.chat-header{height:45px;padding:0 10px;display:flex;align-items:center;justify-content:space-between;background:var(--card);border-bottom:1px solid #00000015;font-weight:bold;font-size:14px}
.messages{flex:1;padding:8px;overflow-y:auto;background:linear-gradient(180deg,#00000005,transparent);min-height:0}
.msg{max-width:75%;padding:8px 10px;border-radius:12px;margin-bottom:5px;font-size:13px;line-height:1.3;box-shadow:0 1px 2px rgba(0,0,0,0.1)}
.me{background:#dcf8c6;margin-left:auto;border-bottom-right-radius:4px}
.other{background:#fff;border-bottom-left-radius:4px}
.msg small{font-size:10px;color:var(--muted)}
.input{padding:5px;display:flex;gap:5px;background:var(--card);position:sticky;bottom:0}
.input input{flex:1;padding:8px 10px;border-radius:22px;border:1px solid #00000020;font-size:13px}
.input button{width:36px;height:36px;border-radius:50%;background:var(--primary);border:none;color:white;font-size:16px}
.input .fa-image{background:#ff5e57;padding:6px;border-radius:50%;color:white;cursor:pointer}
body.dark .other{background:#1f2c34;color:white}
body.dark .me{background:#005c4b;color:white}
body.dark input{background:#111b21;color:white}
body.dark .user{color:white}
.login{position:fixed;inset:0;background:var(--bg);display:flex;align-items:center;justify-content:center;z-index:1000}
.login-box{background:var(--card);padding:20px;border-radius:10px;width:90%;max-width:320px;text-align:center}
.login-box input{width:100%;padding:10px;margin-top:10px;font-size:14px}
</style>

<body>
<div class="header">Live Connect</div>

<div class="hero" id="hero">
  <div id="heroText">Welcome to Live Connect</div>
</div>

<div class="section">
<h2>About</h2>
<p>Professional live chat website with private and group chats, active users, images, typing indicator, unread messages, mobile-friendly layout.</p>
</div>

<div class="section">
<h2>Features</h2>
<p>Real-time chat, group chat, Dark/Light mode, mobile-first UX, hero slider, active users, dashboard welcome message, unread indicators.</p>
<button onclick="createGroup()">Create Group</button>
</div>

<div class="login" id="login">
  <div class="login-box">
    <h3>Enter Your Name</h3>
    <input id="username" placeholder="Full Name"/>
    <button onclick="loginUser()">Start Chat</button>
  </div>
</div>

<div class="app" id="app" style="display:none">
  <div class="activeCount" id="activeCount">Active Users: 0</div>
  <div class="sidebar" id="users">
    <!-- Users list and groups -->
  </div>

  <div class="chat">
    <div class="chat-header">
      <div id="chatUser">Select User/Group</div>
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
