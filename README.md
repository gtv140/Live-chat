<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SimpleChat</title>
<style>
body{margin:0;font-family:'Segoe UI',Roboto,sans-serif;background:#111;color:#eee;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input{border:1px solid #555;border-radius:8px;padding:8px;width:100%;box-sizing:border-box;background:#1a1a1a;color:#eee;}

/* Navbar */
.navbar{display:flex;justify-content:space-between;align-items:center;padding:10px 15px;background:#1f1f1f;border-bottom:1px solid #333;flex-wrap:wrap;}
.navbar h2{color:#0ff;}

/* Layout */
.main-container{display:flex;flex-direction:row;height:calc(100vh - 60px);}
.sidebar{width:200px;background:#1f1f1f;border-right:1px solid #333;overflow-y:auto;display:block;}
.sidebar h3{text-align:center;padding:10px;border-bottom:1px solid #333;color:#0ff;}
.user{padding:10px;cursor:pointer;border-bottom:1px solid #333;display:flex;align-items:center;justify-content:space-between;}
.user.online::after{content:"•";color:#0ff;font-weight:bold;margin-left:5px;}
.content-area{flex:1;display:flex;flex-direction:column;}
.chat-container{flex:1;display:flex;flex-direction:column;border-left:1px solid #333;}
.chat-messages{flex:1;overflow-y:auto;padding:10px;display:flex;flex-direction:column;gap:5px;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;box-shadow:0 0 3px #0ff;position:relative;}
.message.self{align-self:flex-end;background:#0ff;color:#000;}
.message.other{align-self:flex-start;background:#333;color:#eee;}
.timestamp{font-size:10px;color:#aaa;margin-top:4px;text-align:right;}
.typing-indicator{font-size:12px;color:#0ff;margin:2px 0;}
.chat-input{display:flex;padding:10px;border-top:1px solid #333;gap:5px;}
.chat-input input{flex:1;padding:8px;border-radius:20px;border:1px solid #0ff;background:#1a1a1a;color:#eee;}
.chat-input button{background:#0ff;color:#000;padding:6px 12px;font-weight:bold;}
</style>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-database.js";

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
const db = getDatabase();

let currentUser="", chatId="", typingTimeout="", isPrivateChat=true, isGroupChat=false;

// ---- Login ----
function login(){
  const username=document.getElementById("username").value.trim();
  if(!username){alert("Enter username"); return;}
  currentUser=username;
  document.getElementById("login-screen").style.display="none";
  document.getElementById("app-screen").style.display="flex";
  set(ref(db,"active_users/"+currentUser),{online:true});
  onDisconnect(ref(db,"active_users/"+currentUser)).remove();
  loadActiveUsers(); loadGroups();
}

// ---- Active Users ----
function loadActiveUsers(){
  onValue(ref(db,"active_users"),snap=>{
    const usersList=document.getElementById("users-list");
    usersList.innerHTML="";
    const users = snap.val()||{};
    Object.keys(users).forEach(user=>{
      if(user!==currentUser){
        const div=document.createElement("div");
        div.textContent=user; div.className="user online"; div.onclick=()=>openPrivateChat(user);
        usersList.appendChild(div);
      }
    });
  });
}

// ---- Groups ----
function loadGroups(){
  onValue(ref(db,"groups"),snap=>{
    const groupsList=document.getElementById("groups-list");
    groupsList.innerHTML="";
    const groups = snap.val()||{};
    Object.keys(groups).forEach(gid=>{
      const g=groups[gid];
      const div=document.createElement("div");
      div.textContent=g.name; div.className="user";
      div.onclick=()=>openGroupChat(gid);
      groupsList.appendChild(div);
    });
  });
}

function createGroup(){
  const gname=prompt("Enter group name:");
  if(!gname) return;
  const groupRef=push(ref(db,"groups"));
  set(groupRef,{name:gname,creator:currentUser,members:{[currentUser]:true}});
}

// ---- Chat ----
function getChatId(user1,user2){ return [user1,user2].sort().join("_"); }

function openPrivateChat(user){
  chatId=getChatId(currentUser,user);
  isPrivateChat=true; isGroupChat=false;
  document.getElementById("messages-container").innerHTML="";
  loadChat(chatId,true,false);
}

function openGroupChat(gid){
  chatId="group_"+gid;
  isPrivateChat=false; isGroupChat=true;
  document.getElementById("messages-container").innerHTML="";
  loadChat(chatId,false,true);
}

// ---- Load Chat ----
function loadChat(id,privateChat=true,groupChat=false){
  isPrivateChat=privateChat; isGroupChat=groupChat;
  const msgContainer=document.getElementById("messages-container");
  const typingIndicator=document.getElementById("typing-indicator");

  onValue(ref(db,"chat/"+id),snap=>{
    msgContainer.innerHTML="";
    const msgs = snap.val()||{};
    Object.values(msgs).forEach(msg=>{
      if(isPrivateChat && !( [msg.user,msg.to].includes(currentUser) )) return;
      const div=document.createElement("div");
      div.className="message "+(msg.user===currentUser?"self":"other");
      div.innerHTML=`<strong>${msg.user}</strong>: ${msg.text || ""}<div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>`;
      msgContainer.appendChild(div);
    });
    msgContainer.scrollTop=msgContainer.scrollHeight;
  });

  onValue(ref(db,"typing/"+id),snap=>{
    const data=snap.val()||{};
    const typingUsers=Object.keys(data).filter(u=>u!==currentUser);
    typingIndicator.textContent=typingUsers.length>0?typingUsers.join(", ")+" is typing...":"";
  });

  document.getElementById("message-input").oninput=()=>{
    set(ref(db,"typing/"+id+"/"+currentUser),true);
    clearTimeout(typingTimeout);
    typingTimeout=setTimeout(()=>{ remove(ref(db,"typing/"+id+"/"+currentUser)); },2000);
  };

  document.getElementById("send-btn").onclick=()=>sendMessage();
}

// ---- Send Message ----
function sendMessage(){
  const text=document.getElementById("message-input").value.trim();
  if(!text) return;
  const msgData={user:currentUser,text:text,time:Date.now()};
  if(isPrivateChat){ msgData.to=chatId.replace(currentUser+"_","").replace("_"+currentUser,""); }
  set(push(ref(db,"chat/"+chatId)),msgData);
  document.getElementById("message-input").value="";
  remove(ref(db,"typing/"+chatId+"/"+currentUser));
}
</script>
</head>
<body>
<!-- Login -->
<div id="login-screen" style="text-align:center;margin-top:50px;">
<h2>Welcome to SimpleChat</h2>
<input type="text" id="username" placeholder="Enter Your Name"><br><br>
<button onclick="login()">Enter</button>
</div>

<!-- App -->
<div id="app-screen" style="display:none;flex-direction:column;">
<div class="navbar">
<h2>SimpleChat</h2>
</div>

<div class="main-container">
<div class="sidebar" id="users-sidebar">
<h3>Active Users</h3>
<div id="users-list"></div>
<h3>Groups</h3>
<div id="groups-list"></div>
<button onclick="createGroup()">+ Create Group</button>
</div>

<div class="content-area">
<div class="chat-container" id="chat-container">
<div class="chat-messages" id="messages-container"></div>
<div class="typing-indicator" id="typing-indicator"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button id="send-btn">Send</button>
</div>
</div>
</div>
</div>
</div>
</body>
</html>
